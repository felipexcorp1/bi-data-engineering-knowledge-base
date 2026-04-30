# API Integration for Data Engineering

## Overview
Extract data from APIs, transform it for business needs, load into data warehouse, and rebuild business logic in the new context.

## Pipeline Architecture

### 1. Extract Phase
```python
import requests
import pandas as pd
from datetime import datetime, timedelta

def extract_api_data(base_url, endpoint, params=None, headers=None):
    """
    Extract data from REST API with pagination support
    """
    all_data = []
    page = 1
    has_more = True
    
    while has_more:
        url = f"{base_url}/{endpoint}"
        if params:
            params['page'] = page
        
        response = requests.get(url, params=params, headers=headers)
        
        if response.status_code == 200:
            data = response.json()
            all_data.extend(data.get('items', data))
            
            # Check for more pages
            has_more = len(data.get('items', [])) >= data.get('per_page', 100)
            page += 1
        else:
            raise Exception(f"API Error: {response.status_code} - {response.text}")
    
    return pd.DataFrame(all_data)

# Example usage
customer_data = extract_api_data(
    base_url="https://api.example.com/v1",
    endpoint="customers",
    params={"updated_since": "2024-01-01"},
    headers={"Authorization": "Bearer YOUR_TOKEN"}
)
```

### 2. Transform Phase
```python
def transform_api_data(raw_df, business_rules):
    """
    Apply business transformations to raw API data
    """
    df = raw_df.copy()
    
    # Data type conversions
    df['created_at'] = pd.to_datetime(df['created_at'])
    df['amount'] = pd.to_numeric(df['amount'], errors='coerce')
    
    # Handle missing values
    df['email'] = df['email'].fillna('unknown@example.com')
    df['status'] = df['status'].fillna('inactive')
    
    # Apply business rules
    if business_rules.get('normalize_status'):
        df['status'] = df['status'].str.lower().str.replace(' ', '_')
    
    # Add derived columns
    df['is_active'] = df['status'] == 'active'
    df['days_since_creation'] = (datetime.now() - df['created_at']).dt.days
    
    # Remove duplicates
    df = df.drop_duplicates(subset=['customer_id'])
    
    return df

# Example usage
transformed_data = transform_api_data(
    customer_data,
    business_rules={
        'normalize_status': True,
        'default_region': 'NA'
    }
)
```

### 3. Load Phase (Snowflake)
```python
import snowflake.connector
from snowflake.connector.pandas_tools import write_pandas

def load_to_snowflake(df, table_name, sf_config):
    """
    Load transformed data into Snowflake
    """
    conn = snowflake.connector.connect(
        user=sf_config['user'],
        password=sf_config['password'],
        account=sf_config['account'],
        warehouse=sf_config['warehouse'],
        database=sf_config['database'],
        schema=sf_config['schema']
    )
    
    try:
        # Write data to Snowflake
        success, n_chunks, n_rows = write_pandas(
            conn,
            df,
            table_name,
            quote_identifiers=False
        )
        
        print(f"Loaded {n_rows} rows to {table_name}")
        return success
    finally:
        conn.close()

# Example usage
sf_config = {
    'user': 'your_user',
    'password': 'your_password',
    'account': 'your_account',
    'warehouse': 'COMPUTE_WH',
    'database': 'BI_DATA',
    'schema': 'RAW'
}

load_to_snowflake(transformed_data, 'STG_CUSTOMERS', sf_config)
```

### 4. Rebuild Logic Phase
```sql
-- Rebuild business logic in Snowflake after loading
CREATE OR REPLACE PROCEDURE REBUILD_CUSTOMER_LOGIC()
RETURNS STRING
LANGUAGE SQL
AS
BEGIN
    -- Update customer dimension with latest data
    MERGE INTO DIM_CUSTOMER tgt
    USING STG_CUSTOMERS src
    ON tgt.CUSTOMER_ID = src.CUSTOMER_ID
       AND tgt.IS_CURRENT = TRUE
       AND (
           tgt.EMAIL <> src.EMAIL OR
           tgt.STATUS <> src.STATUS
       )
    
    WHEN MATCHED THEN
        UPDATE SET
            END_DATE = CURRENT_DATE(),
            IS_CURRENT = FALSE,
            UPDATED_AT = CURRENT_TIMESTAMP()
    
    WHEN NOT MATCHED THEN
        INSERT (
            CUSTOMER_ID,
            EMAIL,
            STATUS,
            START_DATE,
            END_DATE,
            IS_CURRENT,
            UPDATED_AT
        )
        VALUES (
            src.CUSTOMER_ID,
            src.EMAIL,
            src.STATUS,
            CURRENT_DATE(),
            NULL,
            TRUE,
            CURRENT_TIMESTAMP()
        );
    
    RETURN 'Customer logic rebuilt successfully';
END;
```

## Orchestration with Airflow

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'data-engineering',
    'depends_on_past': False,
    'start_date': datetime(2024, 1, 1),
    'email_on_failure': True,
    'retries': 2,
    'retry_delay': timedelta(minutes=5)
}

dag = DAG(
    'api_integration_pipeline',
    default_args=default_args,
    schedule_interval='@daily',
    catchup=False
)

def extract_task(**context):
    return extract_api_data(
        base_url="https://api.example.com/v1",
        endpoint="customers",
        params={"updated_since": context['ds']}
    )

def transform_task(**context):
    raw_data = context['task_instance'].xcom_pull(task_ids='extract')
    return transform_api_data(raw_data, {'normalize_status': True})

def load_task(**context):
    transformed_data = context['task_instance'].xcom_pull(task_ids='transform')
    load_to_snowflake(transformed_data, 'STG_CUSTOMERS', sf_config)

extract = PythonOperator(
    task_id='extract',
    python_callable=extract_task,
    dag=dag
)

transform = PythonOperator(
    task_id='transform',
    python_callable=transform_task,
    dag=dag
)

load = PythonOperator(
    task_id='load',
    python_callable=load_task,
    dag=dag
)

extract >> transform >> load
```

## Error Handling & Monitoring

```python
def api_pipeline_with_monitoring(base_url, endpoint, sf_config):
    """
    API pipeline with comprehensive error handling and monitoring
    """
    start_time = datetime.now()
    metrics = {
        'start_time': start_time,
        'records_extracted': 0,
        'records_loaded': 0,
        'errors': []
    }
    
    try:
        # Extract
        raw_data = extract_api_data(base_url, endpoint)
        metrics['records_extracted'] = len(raw_data)
        
        # Transform
        transformed_data = transform_api_data(raw_data, {})
        
        # Load
        success = load_to_snowflake(transformed_data, 'STG_CUSTOMERS', sf_config)
        metrics['records_loaded'] = len(transformed_data)
        
        # Log success
        log_metrics(metrics)
        
    except Exception as e:
        metrics['errors'].append(str(e))
        log_metrics(metrics)
        raise
    
    return metrics

def log_metrics(metrics):
    """
    Log pipeline metrics for monitoring
    """
    duration = (datetime.now() - metrics['start_time']).total_seconds()
    print(f"Pipeline Duration: {duration}s")
    print(f"Records Extracted: {metrics['records_extracted']}")
    print(f"Records Loaded: {metrics['records_loaded']}")
    if metrics['errors']:
        print(f"Errors: {metrics['errors']}")
```

## Best Practices
- Implement exponential backoff for API rate limiting
- Use secrets management for API keys and credentials
- Add data validation checks before loading
- Implement idempotent operations for re-runs
- Monitor API usage and costs
- Cache API responses when appropriate
- Use connection pooling for database connections
- Implement circuit breakers for failing APIs
- Log all transformations for audit trail
- Test with sample data before full pipeline run
