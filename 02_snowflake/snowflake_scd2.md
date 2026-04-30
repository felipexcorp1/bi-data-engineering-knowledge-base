# Snowflake SCD2 Implementation

## Overview
Slowly Changing Dimension Type 2 tracks historical changes by maintaining multiple versions of a record with effective date ranges.

## Table Structure
```sql
CREATE OR REPLACE TABLE DIM_CUSTOMER (
    CUSTOMER_ID NUMBER(38,0) PRIMARY KEY,
    CUSTOMER_NAME VARCHAR(100),
    EMAIL VARCHAR(255),
    PHONE VARCHAR(50),
    REGION VARCHAR(50),
    START_DATE DATE,
    END_DATE DATE,
    IS_CURRENT BOOLEAN,
    UPDATED_AT TIMESTAMP_LTZ DEFAULT CURRENT_TIMESTAMP()
);

-- Create clustering key for performance
ALTER TABLE DIM_CUSTOMER CLUSTER BY (CUSTOMER_ID, START_DATE);
```

## SCD2 Merge Pattern
```sql
MERGE INTO DIM_CUSTOMER tgt
USING (
    SELECT 
        CUSTOMER_ID,
        CUSTOMER_NAME,
        EMAIL,
        PHONE,
        REGION,
        CURRENT_DATE() as NEW_START_DATE
    FROM STG_CUSTOMER
) src
ON tgt.CUSTOMER_ID = src.CUSTOMER_ID 
   AND tgt.IS_CURRENT = TRUE
   AND (
       tgt.CUSTOMER_NAME <> src.CUSTOMER_NAME OR
       tgt.EMAIL <> src.EMAIL OR
       tgt.PHONE <> src.PHONE OR
       tgt.REGION <> src.REGION OR
       (tgt.CUSTOMER_NAME IS NULL AND src.CUSTOMER_NAME IS NOT NULL) OR
       (tgt.EMAIL IS NULL AND src.EMAIL IS NOT NULL)
   )

WHEN MATCHED THEN
    UPDATE SET
        END_DATE = CURRENT_DATE(),
        IS_CURRENT = FALSE,
        UPDATED_AT = CURRENT_TIMESTAMP()

WHEN NOT MATCHED THEN
    INSERT (
        CUSTOMER_ID,
        CUSTOMER_NAME,
        EMAIL,
        PHONE,
        REGION,
        START_DATE,
        END_DATE,
        IS_CURRENT,
        UPDATED_AT
    )
    VALUES (
        src.CUSTOMER_ID,
        src.CUSTOMER_NAME,
        src.EMAIL,
        src.PHONE,
        src.REGION,
        src.NEW_START_DATE,
        NULL,
        TRUE,
        CURRENT_TIMESTAMP()
    );
```

## Handling New Records
```sql
-- Insert completely new customers (no existing record)
INSERT INTO DIM_CUSTOMER (
    CUSTOMER_ID,
    CUSTOMER_NAME,
    EMAIL,
    PHONE,
    REGION,
    START_DATE,
    END_DATE,
    IS_CURRENT
)
SELECT 
    CUSTOMER_ID,
    CUSTOMER_NAME,
    EMAIL,
    PHONE,
    REGION,
    CURRENT_DATE(),
    NULL,
    TRUE
FROM STG_CUSTOMER src
WHERE NOT EXISTS (
    SELECT 1 
    FROM DIM_CUSTOMER tgt 
    WHERE tgt.CUSTOMER_ID = src.CUSTOMER_ID
);
```

## Querying Current Records
```sql
-- Get only current (active) records
SELECT *
FROM DIM_CUSTOMER
WHERE IS_CURRENT = TRUE;

-- Get historical record as of specific date
SELECT *
FROM DIM_CUSTOMER
WHERE START_DATE <= '2024-03-15'
  AND (END_DATE >= '2024-03-15' OR END_DATE IS NULL);
```

## Performance Optimization
```sql
-- Use streams for change data capture
CREATE OR REPLACE STREAM CUSTOMER_STREAM ON TABLE STG_CUSTOMER;

-- Materialized view for frequently accessed current records
CREATE OR REPLACE MATERIALIZED VIEW MV_CURRENT_CUSTOMERS AS
SELECT *
FROM DIM_CUSTOMER
WHERE IS_CURRENT = TRUE;

-- Query the materialized view for better performance
SELECT * FROM MV_CURRENT_CUSTOMERS;
```

## Best Practices
- Use clustering keys on natural key and date columns
- Implement streams for incremental processing
- Consider materialized views for frequently accessed current state
- Add indexes on join columns
- Monitor table size and consider partitioning for large dimensions
- Use TASKS for automated SCD2 processing
- Implement error handling and logging in stored procedures
