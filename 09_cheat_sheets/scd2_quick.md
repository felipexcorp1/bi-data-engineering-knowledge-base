# SCD2 Quick Reference

## Overview
Slowly Changing Dimension Type 2 maintains complete history of changes by creating new records with effective date ranges.

## Core Pattern
**Detect → Close → Insert**

## Implementation Steps

### Step 1: Table Structure
```sql
CREATE TABLE DIM_CUSTOMER (
    CUSTOMER_ID NUMBER(38,0),
    CUSTOMER_NAME VARCHAR(100),
    EMAIL VARCHAR(255),
    STATUS VARCHAR(20),
    START_DATE DATE,
    END_DATE DATE,
    IS_CURRENT BOOLEAN,
    UPDATED_AT TIMESTAMP
);

-- Add clustering for performance
ALTER TABLE DIM_CUSTOMER CLUSTER BY (CUSTOMER_ID, START_DATE);
```

### Step 2: Detect Changes
```sql
-- Identify records that have changed
WITH changes AS (
    SELECT 
        src.CUSTOMER_ID,
        src.CUSTOMER_NAME,
        src.EMAIL,
        src.STATUS,
        tgt.START_DATE as OLD_START_DATE
    FROM STG_CUSTOMER src
    LEFT JOIN DIM_CUSTOMER tgt 
        ON src.CUSTOMER_ID = tgt.CUSTOMER_ID 
        AND tgt.IS_CURRENT = TRUE
    WHERE tgt.CUSTOMER_ID IS NULL  -- New record
       OR src.CUSTOMER_NAME <> tgt.CUSTOMER_NAME
       OR src.EMAIL <> tgt.EMAIL
       OR src.STATUS <> tgt.STATUS
)
SELECT * FROM changes;
```

### Step 3: Close Old Records
```sql
-- Set END_DATE and IS_CURRENT = FALSE for changed records
UPDATE DIM_CUSTOMER
SET 
    END_DATE = CURRENT_DATE(),
    IS_CURRENT = FALSE,
    UPDATED_AT = CURRENT_TIMESTAMP()
WHERE CUSTOMER_ID IN (
    SELECT CUSTOMER_ID 
    FROM STG_CUSTOMER src
    JOIN DIM_CUSTOMER tgt 
        ON src.CUSTOMER_ID = tgt.CUSTOMER_ID 
        AND tgt.IS_CURRENT = TRUE
    WHERE src.CUSTOMER_NAME <> tgt.CUSTOMER_NAME
       OR src.EMAIL <> tgt.EMAIL
       OR src.STATUS <> tgt.STATUS
);
```

### Step 4: Insert New Records
```sql
-- Insert new versions of changed records and completely new records
INSERT INTO DIM_CUSTOMER (
    CUSTOMER_ID,
    CUSTOMER_NAME,
    EMAIL,
    STATUS,
    START_DATE,
    END_DATE,
    IS_CURRENT,
    UPDATED_AT
)
SELECT 
    CUSTOMER_ID,
    CUSTOMER_NAME,
    EMAIL,
    STATUS,
    CURRENT_DATE() as START_DATE,
    NULL as END_DATE,
    TRUE as IS_CURRENT,
    CURRENT_TIMESTAMP() as UPDATED_AT
FROM STG_CUSTOMER src
WHERE NOT EXISTS (
    SELECT 1 
    FROM DIM_CUSTOMER tgt 
    WHERE tgt.CUSTOMER_ID = src.CUSTOMER_ID 
      AND tgt.IS_CURRENT = TRUE
      AND tgt.CUSTOMER_NAME = src.CUSTOMER_NAME
      AND tgt.EMAIL = src.EMAIL
      AND tgt.STATUS = src.STATUS
);
```

## Complete MERGE Statement
```sql
MERGE INTO DIM_CUSTOMER tgt
USING STG_CUSTOMER src
ON tgt.CUSTOMER_ID = src.CUSTOMER_ID 
   AND tgt.IS_CURRENT = TRUE
   AND (
       tgt.CUSTOMER_NAME <> src.CUSTOMER_NAME OR
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
        CUSTOMER_NAME,
        EMAIL,
        STATUS,
        START_DATE,
        END_DATE,
        IS_CURRENT,
        UPDATED_AT
    )
    VALUES (
        src.CUSTOMER_ID,
        src.CUSTOMER_NAME,
        src.EMAIL,
        src.STATUS,
        CURRENT_DATE(),
        NULL,
        TRUE,
        CURRENT_TIMESTAMP()
    );
```

## Query Patterns

### Get Current Records
```sql
SELECT * FROM DIM_CUSTOMER WHERE IS_CURRENT = TRUE;
```

### Get Historical Record as of Date
```sql
SELECT * 
FROM DIM_CUSTOMER 
WHERE CUSTOMER_ID = 12345
  AND START_DATE <= '2024-03-15'
  AND (END_DATE >= '2024-03-15' OR END_DATE IS NULL);
```

### Get Full History for Entity
```sql
SELECT * 
FROM DIM_CUSTOMER 
WHERE CUSTOMER_ID = 12345
ORDER BY START_DATE;
```

### Count Versions per Entity
```sql
SELECT 
    CUSTOMER_ID,
    COUNT(*) as version_count
FROM DIM_CUSTOMER
GROUP BY CUSTOMER_ID
HAVING COUNT(*) > 1;
```

## Snowflake-Specific Optimizations

### Use Streams for CDC
```sql
CREATE STREAM CUSTOMER_STREAM ON TABLE STG_CUSTOMER;

-- Query only changed records
SELECT * FROM CUSTOMER_STREAM WHERE METADATA$ACTION = 'INSERT';
```

### Materialized View for Current Records
```sql
CREATE MATERIALIZED VIEW MV_CURRENT_CUSTOMERS AS
SELECT * FROM DIM_CUSTOMER WHERE IS_CURRENT = TRUE;
```

### Automated Processing with Tasks
```sql
CREATE TASK SCD2_CUSTOMER_TASK
WAREHOUSE = COMPUTE_WH
SCHEDULE = 'USING CRON 0 2 * * * UTC'
AS
CALL PROCESS_SCD2_CUSTOMERS();
```

## Common Pitfalls

### ❌ Incorrect NULL Handling
```sql
-- Wrong: NULL comparison fails
WHERE tgt.EMAIL <> src.EMAIL

-- Correct: Handle NULLs explicitly
WHERE (tgt.EMAIL <> src.EMAIL OR 
       (tgt.EMAIL IS NULL AND src.EMAIL IS NOT NULL) OR
       (tgt.EMAIL IS NOT NULL AND src.EMAIL IS NULL))
```

### ❌ Missing IS_CURRENT Filter
```sql
-- Wrong: Updates all historical records
UPDATE DIM_CUSTOMER SET END_DATE = CURRENT_DATE();

-- Correct: Only update current records
UPDATE DIM_CUSTOMER 
SET END_DATE = CURRENT_DATE()
WHERE IS_CURRENT = TRUE;
```

### ❌ Date Boundary Issues
```sql
-- Wrong: Overlapping date ranges
SET START_DATE = CURRENT_DATE(),
    END_DATE = CURRENT_DATE() - 1

-- Correct: Non-overlapping ranges
SET START_DATE = CURRENT_DATE(),
    END_DATE = NULL  -- NULL means "to present"
```

## Performance Tips

1. **Cluster by natural key and date**
   ```sql
   ALTER TABLE DIM_CUSTOMER CLUSTER BY (CUSTOMER_ID, START_DATE);
   ```

2. **Use appropriate data types**
   - Use DATE for dates, not TIMESTAMP
   - Use BOOLEAN for flags, not VARCHAR

3. **Filter early in queries**
   ```sql
   -- Good: Filter before joins
   FROM DIM_CUSTOMER 
   WHERE IS_CURRENT = TRUE
   JOIN FACT_TRANSACTIONS ON ...
   
   -- Bad: Join all records then filter
   FROM DIM_CUSTOMER 
   JOIN FACT_TRANSACTIONS ON ...
   WHERE IS_CURRENT = TRUE
   ```

4. **Consider partitioning for large dimensions**
   ```sql
   ALTER TABLE DIM_CUSTOMER 
   ADD PARTITIONING KEY (START_DATE);
   ```

## Validation Queries

### Check for Overlapping Dates
```sql
SELECT 
    CUSTOMER_ID,
    START_DATE,
    END_DATE
FROM DIM_CUSTOMER
WHERE CUSTOMER_ID IN (
    SELECT CUSTOMER_ID
    FROM DIM_CUSTOMER
    GROUP BY CUSTOMER_ID
    HAVING COUNT(*) > 1
)
ORDER BY CUSTOMER_ID, START_DATE;
```

### Verify IS_CURRENT Consistency
```sql
-- Should return 0 rows if correct
SELECT CUSTOMER_ID
FROM DIM_CUSTOMER
GROUP BY CUSTOMER_ID
HAVING SUM(CASE WHEN IS_CURRENT = TRUE THEN 1 ELSE 0 END) > 1;
```

### Check for Gaps in History
```sql
WITH ordered_history AS (
    SELECT 
        CUSTOMER_ID,
        START_DATE,
        END_DATE,
        LAG(END_DATE) OVER (PARTITION BY CUSTOMER_ID ORDER BY START_DATE) as prev_end_date
    FROM DIM_CUSTOMER
)
SELECT CUSTOMER_ID, START_DATE, prev_end_date
FROM ordered_history
WHERE prev_end_date IS NOT NULL 
  AND START_DATE > prev_end_date;
```

## Quick Decision Tree

**When to use SCD2:**
- Need historical analysis? → YES
- Regulatory requirements? → YES
- Audit trail needed? → YES
- Performance critical? → Consider SCD1

**When to use SCD1:**
- Corrections only? → YES
- No history needed? → YES
- Performance priority? → YES
- Simple attributes? → YES
