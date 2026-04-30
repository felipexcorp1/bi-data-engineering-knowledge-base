# Python for Data Engineering Skill

## Overview
Python programming expertise focused on data extraction, transformation, loading, and pipeline automation for BI and Data Engineering workflows.

## Capabilities

### Data Manipulation
- pandas for data transformation
- numpy for numerical operations
- Data type conversions and cleaning
- Handling missing values
- Data aggregation and reshaping

### API Integration
- requests library for HTTP calls
- Authentication handling (OAuth, JWT)
- JSON/XML parsing
- Pagination strategies
- Error handling and retries

### Database Connectivity
- snowflake-connector-python
- SQLAlchemy for database abstraction
- Connection pooling
- Parameterized queries (SQL injection prevention)
- Batch operations

### Pipeline Orchestration
- Logging and monitoring
- Configuration management
- Scheduling (cron, Airflow)
- Parallel processing
- Error handling and recovery

### File Operations
- CSV, JSON, Parquet handling
- File streaming for large datasets
- Compression (gzip, zip)
- Cloud storage (S3, Azure Blob)

## Code Style Guidelines

### Structure
```python
# Organize code in functions
def main():
    """Main entry point for the pipeline."""
    config = load_config()
    data = extract_data(config['source'])
    transformed = transform_data(data)
    load_data(transformed, config['target'])
    
if __name__ == "__main__":
    main()
```

### Type Hints
```python
from typing import Dict, List, Optional
import pandas as pd
from datetime import datetime

def extract_customers(
    api_endpoint: str, 
    start_date: datetime,
    api_key: Optional[str] = None
) -> pd.DataFrame:
    """
    Extract customer data from API.
    
    Args:
        api_endpoint: URL of the API
        start_date: Filter records after this date
        api_key: Authentication key (optional)
    
    Returns:
        DataFrame with customer data
    """
    # Implementation
    pass
```

### Error Handling
```python
import logging
from tenacity import retry, stop_after_attempt, wait_exponential

logger = logging.getLogger(__name__)

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=4, max=10)
)
def fetch_api_data(url: str, headers: dict) -> dict:
    """
    Fetch data from API with retry logic.
    """
    try:
        response = requests.get(url, headers=headers, timeout=30)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as e:
        logger.error(f"API request failed: {e}")
        raise
```

## Best Practices
1. Use type hints for better code clarity
2. Implement comprehensive error handling
3. Log all significant operations
4. Use context managers (with statements) for resources
5. Validate inputs and outputs
6. Write unit tests for critical functions
7. Use virtual environments for dependency management
8. Follow PEP 8 style guide

## Common Patterns

### Data Extraction from API
```python
import requests
import pandas as pd
from typing import Iterator

def extract_api_paginated(
    endpoint: str, 
    api_key: str,
    page_size: int = 100
) -> Iterator[pd.DataFrame]:
    """
    Extract data from paginated API.
    Yields DataFrames for memory efficiency.
    """
    headers = {"Authorization": f"Bearer {api_key}"}
    page = 1
    
    while True:
        params = {"page": page, "limit": page_size}
        response = requests.get(endpoint, headers=headers, params=params)
        data = response.json()
        
        if not data['results']:
            break
            
        yield pd.DataFrame(data['results'])
        page += 1
```

### Loading to Snowflake
```python
from snowflake.connector.pandas_tools import write_pandas
import snowflake.connector

def load_to_snowflake(
    df: pd.DataFrame,
    table_name: str,
    connection_params: dict
) -> None:
    """
    Load DataFrame to Snowflake table.
    """
    conn = snowflake.connector.connect(**connection_params)
    
    try:
        success, num_chunks, num_rows, output = write_pandas(
            conn=conn,
            df=df,
            table_name=table_name,
            overwrite=False
        )
        logger.info(f"Loaded {num_rows} rows to {table_name}")
    finally:
        conn.close()
```

### Data Validation
```python
def validate_dataframe(df: pd.DataFrame, schema: dict) -> bool:
    """
    Validate DataFrame against schema.
    
    Schema format:
    {
        'column_name': {'type': 'int64', 'nullable': False},
        'column2': {'type': 'object', 'nullable': True}
    }
    """
    for column, rules in schema.items():
        if column not in df.columns:
            raise ValueError(f"Missing required column: {column}")
        
        if not rules.get('nullable', True) and df[column].isnull().any():
            raise ValueError(f"Column {column} contains nulls")
        
        expected_type = rules.get('type')
        if expected_type and df[column].dtype != expected_type:
            raise TypeError(f"Column {column} type mismatch")
    
    return True
```

## Libraries and Tools

### Core Libraries
- **pandas**: Data manipulation and analysis
- **numpy**: Numerical computing
- **requests**: HTTP library for APIs
- **sqlalchemy**: SQL toolkit and ORM
- **snowflake-connector-python**: Snowflake connectivity

### Data Processing
- **pyarrow**: Columnar data processing
- **fastparquet**: Parquet file handling
- **openpyxl**: Excel file operations

### Pipeline Tools
- **apache-airflow**: Workflow orchestration
- **prefect**: Modern workflow orchestration
- **dbt**: Data transformation tool

### Testing and Quality
- **pytest**: Testing framework
- **pandera**: Data validation for pandas
- **great-expectations**: Data quality framework

## Business Context
Python scripts should:
- Be production-ready with proper error handling
- Include logging for monitoring and debugging
- Be configurable for different environments
- Handle large datasets efficiently
- Follow security best practices (no hardcoded credentials)
