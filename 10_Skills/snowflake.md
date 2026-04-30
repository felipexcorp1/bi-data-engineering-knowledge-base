# Snowflake Skill

## Overview
Expertise in Snowflake cloud data platform, including architecture, data loading, performance optimization, and advanced features like Streams, Tasks, and Stored Procedures.

## Capabilities

### Data Loading
- COPY INTO for bulk loading
- Snowpipe for continuous ingestion
- File formats (CSV, JSON, Parquet, ORC)
- Stages (internal and external)

### Data Modeling
- Micro-partitions and clustering keys
- Materialized views for performance
- Zero-copy cloning
- Time Travel and Fail-safe

### Advanced Features
- Streams for CDC (Change Data Capture)
- Tasks for automated scheduling
- Stored Procedures (JavaScript)
- UDFs and Stored Functions
- Dynamic Tables (Snowflake's materialized views)

### Performance Optimization
- Warehouse sizing and scaling
- Query acceleration
- Result caching
- Search optimization service

### Security
- Role-based access control (RBAC)
- Row-level security
- Column-level security
- Data masking

## Code Style Guidelines

### SQL Syntax
```sql
-- Use Snowflake-specific functions
SELECT 
    CUSTOMER_ID,
    CUSTOMER_NAME,
    CURRENT_TIMESTAMP() as LOADED_AT
FROM DIM_CUSTOMER
WHERE IS_CURRENT = TRUE;

-- MERGE for SCD Type 2
MERGE INTO DIM_CUSTOMER tgt
USING STG_CUSTOMER src
ON tgt.CUSTOMER_ID = src.CUSTOMER_ID 
   AND tgt.IS_CURRENT = TRUE
   AND tgt.EMAIL <> src.EMAIL
WHEN MATCHED THEN
    UPDATE SET END_DATE = CURRENT_DATE(), IS_CURRENT = FALSE
WHEN NOT MATCHED THEN
    INSERT (CUSTOMER_ID, EMAIL, START_DATE, IS_CURRENT)
    VALUES (src.CUSTOMER_ID, src.EMAIL, CURRENT_DATE(), TRUE);
```

### Stored Procedures
```javascript
// Use JavaScript for complex logic
CREATE OR REPLACE PROCEDURE PROCESS_SCD2_CUSTOMERS()
RETURNS STRING
LANGUAGE JAVASCRIPT
AS $$
    // Implementation here
    return 'Success';
$$;
```

### Tasks and Streams
```sql
-- Create stream for CDC
CREATE STREAM CUSTOMER_CHANGES ON TABLE STG_CUSTOMER;

-- Create scheduled task
CREATE TASK LOAD_CUSTOMER_TASK
WAREHOUSE = COMPUTE_WH
SCHEDULE = 'USING CRON 0 2 * * * UTC'
AS
CALL PROCESS_SCD2_CUSTOMERS();
```

## Best Practices
1. Use appropriate warehouse sizes for workloads
2. Cluster large tables by frequently filtered columns
3. Leverage Time Travel for data recovery
4. Use Streams for incremental processing
5. Implement proper RBAC for security
6. Monitor credit usage and optimize costs
7. Use materialized views for repeated aggregations

## Common Patterns

### SCD Type 2 Implementation
```sql
-- Detect changes
-- Close old records (set END_DATE, IS_CURRENT = FALSE)
-- Insert new records (START_DATE = CURRENT_DATE, IS_CURRENT = TRUE)
```

### Incremental Loading
```sql
-- Use Streams to capture changes
-- Process only changed records
-- Merge into target table
```

### Data Quality Validation
```sql
-- Check for nulls in required fields
-- Validate data types and formats
-- Verify referential integrity
-- Flag anomalies for review
```
