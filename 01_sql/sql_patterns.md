# SQL Patterns for BI & Data Engineering

## 🔹 SCD Type 2
Track historical changes with START_DATE, END_DATE, IS_CURRENT.

```sql
-- Detect changes
WITH changes AS (
    SELECT 
        src.*,
        tgt.START_DATE as OLD_START_DATE,
        tgt.END_DATE as OLD_END_DATE
    FROM staging_table src
    LEFT JOIN dim_table tgt 
        ON src.ID = tgt.ID 
        AND tgt.IS_CURRENT = TRUE
    WHERE tgt.ID IS NULL  -- New record
       OR src.COLUMN_A <> tgt.COLUMN_A
       OR src.COLUMN_B <> tgt.COLUMN_B
)

-- Close old records
UPDATE dim_table
SET END_DATE = CURRENT_DATE(),
    IS_CURRENT = FALSE
WHERE ID IN (SELECT ID FROM changes WHERE OLD_START_DATE IS NOT NULL)

-- Insert new records
INSERT INTO dim_table (ID, COLUMN_A, COLUMN_B, START_DATE, END_DATE, IS_CURRENT)
SELECT ID, COLUMN_A, COLUMN_B, CURRENT_DATE(), NULL, TRUE
FROM changes
```

## 🔹 Window Functions
ROW_NUMBER() OVER (PARTITION BY ID ORDER BY UPDATED_AT DESC)

```sql
-- Get latest record per entity
WITH ranked AS (
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY ID ORDER BY UPDATED_AT DESC) as rn
    FROM table_name
)
SELECT * FROM ranked WHERE rn = 1

-- Running totals
SELECT 
    date,
    amount,
    SUM(amount) OVER (ORDER BY date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as running_total
FROM transactions

-- Moving averages
SELECT 
    date,
    value,
    AVG(value) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) as ma_3day
FROM metrics
```

## 🔹 Data Cleaning
COALESCE, NULLIF, TRIM

```sql
-- Handle nulls with default values
SELECT 
    COALESCE(email, 'unknown@example.com') as email,
    COALESCE(phone, 'N/A') as phone
FROM users

-- Convert empty strings to null
SELECT 
    NULLIF(TRIM(column_name), '') as cleaned_column
FROM table_name

-- Remove leading/trailing spaces
SELECT 
    TRIM(name) as clean_name,
    TRIM(BOTH ' ' FROM description) as clean_desc
FROM products
```

## 🔹 Advanced Joins
```sql
-- Left join with filtering
SELECT 
    o.order_id,
    c.customer_name,
    p.product_name
FROM orders o
LEFT JOIN customers c ON o.customer_id = c.customer_id
LEFT JOIN products p ON o.product_id = p.product_id
WHERE o.order_date >= '2024-01-01'

-- Self join for hierarchy
SELECT 
    e.employee_id,
    e.name as employee_name,
    m.name as manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id

-- Cross join for combinations
SELECT 
    d.department_name,
    l.location_name
FROM departments d
CROSS JOIN locations l
```

## 🔹 Conditional Aggregation
```sql
SELECT 
    department,
    COUNT(*) as total_employees,
    SUM(CASE WHEN status = 'Active' THEN 1 ELSE 0 END) as active_count,
    SUM(CASE WHEN status = 'Active' THEN salary ELSE 0 END) as active_salary_total,
    AVG(CASE WHEN status = 'Active' THEN salary END) as avg_active_salary
FROM employees
GROUP BY department
```

## 🔹 CTEs for Readability
```sql
WITH sales_summary AS (
    SELECT 
        product_id,
        SUM(quantity) as total_qty,
        SUM(amount) as total_amount
    FROM sales
    WHERE sale_date >= '2024-01-01'
    GROUP BY product_id
),
product_info AS (
    SELECT 
        p.product_id,
        p.product_name,
        c.category_name
    FROM products p
    JOIN categories c ON p.category_id = c.category_id
)
SELECT 
    pi.product_name,
    pi.category_name,
    ss.total_qty,
    ss.total_amount
FROM sales_summary ss
JOIN product_info pi ON ss.product_id = pi.product_id
ORDER BY ss.total_amount DESC
```

## 🔹 Best Practices
- Avoid SELECT * - specify columns explicitly
- Filter early in WHERE clauses before joins
- Align with business grain (daily, monthly, etc.)
- Use appropriate indexes for join columns
- Parameterize queries to prevent SQL injection
- Use transactions for data modifications
- Add comments for complex logic
- Test with sample data before full execution
