# Data Engineering Skill

## Overview
Expertise in designing and implementing ETL/ELT pipelines, data integration patterns, and modern data architecture for scalable BI solutions.

## Capabilities

### ETL/ELT Design
- Extraction patterns (full, incremental, change data capture)
- Transformation strategies (in-database, in-memory, distributed)
- Loading patterns (truncate-load, merge, append)
- Data quality frameworks

### API Integration
- REST API consumption
- Pagination strategies (offset, cursor, keyset)
- Authentication (OAuth, API keys, tokens)
- Rate limiting and error handling
- Data format parsing (JSON, XML)

### Data Pipeline Architecture
- Orchestration tools (Airflow, dbt, etc.)
- Pipeline patterns (batch, streaming, hybrid)
- Data lineage and metadata management
- Error handling and retry logic
- Monitoring and alerting

### Data Quality
- Validation rules (completeness, accuracy, consistency)
- Data profiling and anomaly detection
- Data cleansing techniques
- Reconciliation and balancing

### Data Modeling
- Dimensional modeling (star schema, snowflake)
- SCD implementations (Type 1, 2, 3)
- Data vault concepts
- Data lake architecture

## Design Patterns

### API to Data Warehouse Pipeline
```
Extract (API) → Transform (Staging) → Load (Data Warehouse) → Rebuild (Business Logic)
```

### Incremental Loading Pattern
```
1. Identify new/changed records (CDC, timestamp, checksum)
2. Extract incremental data
3. Transform in staging area
4. Merge into target (SCD logic)
5. Update watermark/bookmark
```

### Error Handling Pattern
```
1. Try operation with retry logic
2. Log errors with context
3. Route failed records to dead letter queue
4. Alert on critical failures
5. Resume from checkpoint
```

## Code Style Guidelines

### Python for Data Pipelines
```python
# Use type hints
def extract_data(source: str, start_date: datetime) -> pd.DataFrame:
    """Extract data from source with error handling."""
    try:
        # Implementation
        return df
    except Exception as e:
        logger.error(f"Extraction failed: {e}")
        raise

# Use logging
import logging
logger = logging.getLogger(__name__)
logger.info(f"Processing {record_count} records")

# Idempotent operations
def load_data(df: pd.DataFrame, table: str) -> None:
    """Load data idempotently using MERGE logic."""
    # Implementation with upsert
    pass
```

### Configuration Management
```yaml
# Use YAML for configuration
pipeline:
  name: customer_etl
  schedule: "0 2 * * *"
  source:
    type: api
    endpoint: https://api.example.com/customers
    auth: oauth2
  target:
    type: snowflake
    warehouse: COMPUTE_WH
    database: ANALYTICS
    schema: DIM
```

## Best Practices
1. Design idempotent pipelines (safe to re-run)
2. Implement comprehensive logging
3. Use configuration over code
4. Validate data at each stage
5. Handle errors gracefully with retry logic
6. Monitor pipeline health and performance
7. Document data lineage
8. Optimize for cost and performance

## Common Patterns

### SCD Type 2 Implementation
```
Input: Staging Table with current data
Process:
  1. Detect changes (compare natural key + attributes)
  2. Update existing records (set END_DATE, IS_CURRENT = FALSE)
  3. Insert new records (START_DATE = today, IS_CURRENT = TRUE)
Output: Dimension table with history
```

### API Rate Limiting
```python
import time
from functools import wraps

def rate_limit(calls_per_second: int):
    """Decorator to enforce rate limiting."""
    min_interval = 1.0 / calls_per_second
    
    def decorator(func):
        last_called = [0.0]
        
        @wraps(func)
        def wrapper(*args, **kwargs):
            elapsed = time.time() - last_called[0]
            if elapsed < min_interval:
                time.sleep(min_interval - elapsed)
            last_called[0] = time.time()
            return func(*args, **kwargs)
        
        return wrapper
    return decorator
```

### Data Quality Check
```python
def validate_data(df: pd.DataFrame, rules: dict) -> dict:
    """Validate data against quality rules."""
    results = {}
    
    for column, rule in rules.items():
        if rule['type'] == 'not_null':
            null_count = df[column].isnull().sum()
            results[column] = null_count == 0
        elif rule['type'] == 'range':
            min_val, max_val = rule['min'], rule['max']
            in_range = df[column].between(min_val, max_val).all()
            results[column] = in_range
    
    return results
```

## Business Context
Data engineering solutions should:
- Meet SLAs for data freshness and availability
- Scale with business growth
- Maintain data quality standards
- Enable self-service analytics
- Ensure data security and compliance
