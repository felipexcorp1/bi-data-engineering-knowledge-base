# AI for Business Intelligence

## Overview
Leveraging AI tools (ChatGPT, Claude, Codex) to accelerate BI workflows, improve code quality, and enhance documentation.

## Use Cases

### 1. SQL Generation & Optimization

#### Prompt Template for SQL Queries
```
Context: I'm working with a Snowflake data warehouse for media analytics.
Schema:
- DIM_CUSTOMER (customer_id, name, email, region, status)
- FACT_TRANSACTIONS (transaction_id, customer_id, amount, date, product_id)
- DIM_PRODUCT (product_id, name, category, price)

Task: Write a SQL query to find the top 10 customers by total spend in the last 30 days.
Requirements:
- Use window functions
- Include customer name and region
- Filter for active customers only
- Order by total spend descending
```

#### AI-Generated SQL Example
```sql
WITH customer_spend AS (
    SELECT 
        c.customer_id,
        c.name,
        c.region,
        SUM(t.amount) as total_spend,
        ROW_NUMBER() OVER (ORDER BY SUM(t.amount) DESC) as rank
    FROM FACT_TRANSACTIONS t
    JOIN DIM_CUSTOMER c ON t.customer_id = c.customer_id
    WHERE t.date >= DATEADD(day, -30, CURRENT_DATE())
      AND c.status = 'active'
    GROUP BY c.customer_id, c.name, c.region
)
SELECT 
    customer_id,
    name,
    region,
    total_spend
FROM customer_spend
WHERE rank <= 10
ORDER BY total_spend DESC;
```

### 2. Documentation Generation

#### Prompt for Data Dictionary
```
Context: Create a data dictionary entry for the following Snowflake table.
Table: DIM_CUSTOMER
Columns:
- CUSTOMER_ID: Unique identifier for each customer
- CUSTOMER_NAME: Full name of the customer
- EMAIL: Customer's email address
- REGION: Geographic region (NA, EU, APAC)
- STATUS: Current status (active, inactive, pending)
- START_DATE: When the customer was acquired
- END_DATE: When the customer relationship ended (null if active)
- IS_CURRENT: Boolean flag for current record (SCD2)

Format: Markdown with business description, data type, and example values.
```

#### AI-Generated Documentation
```markdown
## DIM_CUSTOMER

### Business Description
Stores customer information with SCD2 tracking to maintain historical changes. Each customer can have multiple records representing different time periods.

### Columns

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| CUSTOMER_ID | NUMBER(38,0) | Unique identifier for each customer | 12345 |
| CUSTOMER_NAME | VARCHAR(100) | Full name of the customer | "John Doe" |
| EMAIL | VARCHAR(255) | Customer's email address | "john@example.com" |
| REGION | VARCHAR(50) | Geographic region | "NA", "EU", "APAC" |
| STATUS | VARCHAR(20) | Current status | "active", "inactive" |
| START_DATE | DATE | When customer was acquired | 2024-01-15 |
| END_DATE | DATE | When relationship ended | NULL or 2024-06-30 |
| IS_CURRENT | BOOLEAN | Current record flag | TRUE/FALSE |

### Usage Notes
- Always filter by IS_CURRENT = TRUE for current customer data
- Use START_DATE/END_DATE for historical analysis
- Region codes follow ISO 3166-1 alpha-2
```

### 3. Debugging Assistance

#### Prompt for Error Resolution
```
Error Message:
SQL compilation error: Invalid expression [CUSTOMER_ID] in SELECT list

Query:
SELECT 
    CUSTOMER_ID,
    SUM(AMOUNT) as total_spend
FROM FACT_TRANSACTIONS
GROUP BY PRODUCT_ID

Context: Snowflake SQL query for transaction aggregation.
Task: Identify the error and provide corrected query with explanation.
```

#### AI Response
```markdown
## Error Analysis

**Problem**: The query attempts to select CUSTOMER_ID but groups by PRODUCT_ID. In SQL, all non-aggregated columns in the SELECT list must be included in the GROUP BY clause.

**Solution Options**:

Option 1 - Group by CUSTOMER_ID:
```sql
SELECT 
    CUSTOMER_ID,
    SUM(AMOUNT) as total_spend
FROM FACT_TRANSACTIONS
GROUP BY CUSTOMER_ID
```

Option 2 - Group by both columns:
```sql
SELECT 
    CUSTOMER_ID,
    PRODUCT_ID,
    SUM(AMOUNT) as total_spend
FROM FACT_TRANSACTIONS
GROUP BY CUSTOMER_ID, PRODUCT_ID
```

Option 3 - Use aggregate function:
```sql
SELECT 
    MAX(CUSTOMER_ID) as customer_id,
    SUM(AMOUNT) as total_spend
FROM FACT_TRANSACTIONS
GROUP BY PRODUCT_ID
```
```

### 4. Code Review & Optimization

#### Prompt for Query Optimization
```
Context: Snowflake data warehouse with large fact tables (100M+ rows).
Query:
SELECT *
FROM FACT_TRANSACTIONS
WHERE DATE >= '2024-01-01'
ORDER BY AMOUNT DESC;

Task: Optimize this query for better performance. Consider:
- Column selection
- Indexing/clustering
- Query structure
- Snowflake-specific optimizations
```

#### AI Optimization Suggestions
```markdown
## Optimization Recommendations

### 1. Replace SELECT * with specific columns
```sql
SELECT 
    TRANSACTION_ID,
    CUSTOMER_ID,
    PRODUCT_ID,
    AMOUNT,
    DATE
FROM FACT_TRANSACTIONS
WHERE DATE >= '2024-01-01'
ORDER BY AMOUNT DESC;
```

### 2. Add clustering key
```sql
ALTER TABLE FACT_TRANSACTIONS CLUSTER BY (DATE, AMOUNT);
```

### 3. Use materialized view for frequent queries
```sql
CREATE OR REPLACE MATERIALIZED VIEW MV_RECENT_TRANSACTIONS AS
SELECT 
    TRANSACTION_ID,
    CUSTOMER_ID,
    PRODUCT_ID,
    AMOUNT,
    DATE
FROM FACT_TRANSACTIONS
WHERE DATE >= DATEADD(month, -3, CURRENT_DATE());
```

### 4. Add WHERE clause before ORDER BY
```sql
-- Filter first, then sort
SELECT 
    TRANSACTION_ID,
    CUSTOMER_ID,
    PRODUCT_ID,
    AMOUNT,
    DATE
FROM FACT_TRANSACTIONS
WHERE DATE >= '2024-01-01'
  AND AMOUNT > 0
ORDER BY AMOUNT DESC
LIMIT 1000;
```

### Expected Performance Improvement
- Reduced data transfer: 60-80%
- Faster query execution: 3-5x
- Lower warehouse costs: 40-50%
```

## AI-Assisted Workflow Patterns

### Pattern 1: Iterative Query Development
1. **Initial Prompt**: Describe business requirement
2. **Review AI Output**: Check SQL syntax and logic
3. **Refine Prompt**: Add constraints or corrections
4. **Test**: Run query on sample data
5. **Iterate**: Adjust based on results

### Pattern 2: Documentation Automation
1. **Extract Schema**: Use INFORMATION_SCHEMA queries
2. **AI Enhancement**: Generate business descriptions
3. **Review**: Validate accuracy with stakeholders
4. **Publish**: Add to knowledge base

### Pattern 3: Debugging Loop
1. **Capture Error**: Copy error message and query
2. **Context Prompt**: Provide schema and business context
3. **AI Analysis**: Get root cause and solutions
4. **Apply Fix**: Implement suggested changes
5. **Verify**: Test corrected query

## Best Practices

### Prompt Engineering
- **Be Specific**: Include schema, context, and requirements
- **Provide Examples**: Show expected input/output format
- **Iterate**: Refine prompts based on AI responses
- **Validate**: Always test AI-generated code

### Context Management
- **Schema First**: Always provide table structures
- **Business Rules**: Include relevant business logic
- **Constraints**: Mention performance or security requirements
- **Environment**: Specify database platform (Snowflake, SQL Server, etc.)

### Quality Assurance
- **Review Code**: Never deploy AI code without review
- **Test Thoroughly**: Validate with sample and production data
- **Document Changes**: Track AI-assisted modifications
- **Monitor Performance**: Watch for performance regressions

## Tools Integration

### ChatGPT for BI
- **Strengths**: Natural language understanding, explanations
- **Use Cases**: Requirements gathering, documentation, learning
- **Limitations**: No direct database access

### Claude Code / Codex
- **Strengths**: Code generation, debugging, file context
- **Use Cases**: SQL writing, script development, refactoring
- **Limitations**: Requires proper context setup

### Windsurf / IDE Integration
- **Strengths**: Real-time assistance, codebase awareness
- **Use Cases**: Development workflow, quick fixes
- **Limitations**: IDE-specific functionality

## Security Considerations
- Never include sensitive data in prompts
- Use anonymized examples
- Review AI outputs for data leakage
- Follow company AI usage policies
- Validate AI suggestions against security standards
