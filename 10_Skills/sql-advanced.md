# SQL Advanced Skill

## Overview
Expert-level SQL capabilities for Business Intelligence and Data Engineering, with focus on complex queries, optimization, and analytical patterns.

## Capabilities

### Window Functions
- ROW_NUMBER(), RANK(), DENSE_RANK() for ranking and deduplication
- LAG(), LEAD() for time-series analysis
- SUM(), AVG() OVER () for running totals and moving averages
- PARTITION BY for group-based calculations

### Common Table Expressions (CTEs)
- Recursive CTEs for hierarchical data
- Multiple CTEs for query organization
- CTEs vs subqueries - when to use each

### Advanced Joins
- Self-joins for hierarchical relationships
- Anti-joins (NOT EXISTS vs NOT IN)
- Cross joins with filtering
- Join optimization strategies

### Data Manipulation
- MERGE statements for upserts
- CASE statements for conditional logic
- COALESCE, NULLIF for null handling
- String manipulation (CONCAT, SUBSTRING, REPLACE)

### Performance Optimization
- Query execution plan analysis
- Index usage and optimization
- Filtering early (predicate pushdown)
- Appropriate data types

## Code Style Guidelines

### Formatting
```sql
-- Use uppercase for keywords
SELECT 
    column1,
    column2
FROM table_name
WHERE condition = 'value'
ORDER BY column1;

-- Indent subqueries and CTEs
WITH cte_name AS (
    SELECT column1
    FROM table_name
    WHERE condition = 'value'
)
SELECT * FROM cte_name;
```

### Naming Conventions
- Use snake_case for table and column names
- Prefix CTEs with descriptive names
- Alias tables with meaningful abbreviations

### Comments
```sql
-- Single line comments for simple explanations
/* 
   Multi-line comments for complex logic
   explaining business rules or edge cases
*/
```

## Best Practices
1. Always handle NULL values explicitly
2. Use appropriate indexes for large tables
3. Filter data as early as possible
4. Avoid SELECT * in production queries
5. Use EXPLAIN to analyze query performance
6. Document complex business logic in comments

## Business Context
SQL queries should align with:
- Business grain (level of detail)
- Performance requirements
- Data quality standards
- Regulatory compliance (if applicable)
