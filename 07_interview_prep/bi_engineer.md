# BI Engineer Interview Preparation

## Core Topics to Master

### 1. SCD (Slowly Changing Dimensions)

#### SCD Type 2 Implementation
**Question**: How do you implement SCD Type 2 in Snowflake?

**Answer**:
```sql
-- SCD2 tracks historical changes with effective date ranges
MERGE INTO DIM_CUSTOMER tgt
USING STG_CUSTOMER src
ON tgt.CUSTOMER_ID = src.CUSTOMER_ID 
   AND tgt.IS_CURRENT = TRUE
   AND (
       tgt.EMAIL <> src.EMAIL OR
       tgt.STATUS <> src.STATUS
   )

WHEN MATCHED THEN
    UPDATE SET
        END_DATE = CURRENT_DATE(),
        IS_CURRENT = FALSE

WHEN NOT MATCHED THEN
    INSERT (CUSTOMER_ID, EMAIL, STATUS, START_DATE, END_DATE, IS_CURRENT)
    VALUES (src.CUSTOMER_ID, src.EMAIL, src.STATUS, CURRENT_DATE(), NULL, TRUE);
```

**Key Points**:
- Use IS_CURRENT flag for easy filtering
- Track START_DATE and END_DATE for historical analysis
- Compare all relevant columns to detect changes
- Handle NULL values properly in comparison

#### SCD Type 1 vs Type 2
**Question**: When would you use SCD Type 1 vs Type 2?

**Answer**:
- **SCD1**: Overwrite existing values, no history. Use for corrections, non-critical attributes
- **SCD2**: Maintain history with date ranges. Use for critical business attributes (customer status, pricing, geography)

### 2. Snowflake

#### Performance Optimization
**Question**: How do you optimize query performance in Snowflake?

**Answer**:
- **Clustering Keys**: Cluster by frequently filtered/joined columns
  ```sql
  ALTER TABLE FACT_TRANSACTIONS CLUSTER BY (DATE, CUSTOMER_ID);
  ```
- **Materialized Views**: Pre-compute expensive aggregations
  ```sql
  CREATE MATERIALIZED VIEW MV_DAILY_SALES AS
  SELECT DATE, SUM(AMOUNT) FROM FACT_TRANSACTIONS GROUP BY DATE;
  ```
- **Micro-partitions**: Design tables to leverage automatic pruning
- **Query Optimization**: Filter early, avoid SELECT *, use appropriate join types
- **Warehouse Sizing**: Right-size warehouses for workload

#### Snowflake Architecture
**Question**: Explain Snowflake's architecture components.

**Answer**:
- **Cloud Services Layer**: Authentication, query parsing, optimization
- **Compute Layer (Virtual Warehouses)**: Elastic compute resources
- **Storage Layer**: Scalable cloud storage with micro-partitions
- **Separation of Compute and Storage**: Scale independently

#### Streams and Tasks
**Question**: How do you implement incremental data loading in Snowflake?

**Answer**:
```sql
-- Create stream to track changes
CREATE STREAM CUSTOMER_STREAM ON TABLE STG_CUSTOMER;

-- Create task for automated processing
CREATE TASK LOAD_CUSTOMER_TASK
WAREHOUSE = COMPUTE_WH
SCHEDULE = 'USING CRON 0 2 * * * UTC'
AS
CALL LOAD_CUSTOMER_PROCEDURE();

-- Query stream for changes only
SELECT * FROM CUSTOMER_STREAM;
```

### 3. Power BI

#### Data Modeling
**Question**: What is a star schema and why is it preferred in Power BI?

**Answer**:
- **Star Schema**: Central fact table connected to dimension tables
- **Benefits**: 
  - Simpler queries with fewer joins
  - Better performance
  - Easier to understand and maintain
  - Optimized for BI tools

#### DAX Measures
**Question**: Write a DAX measure for year-over-year growth.

**Answer**:
```dax
YoY Growth % = 
VAR CurrentYear = [Total Sales]
VAR PreviousYear = 
    CALCULATE(
        [Total Sales],
        SAMEPERIODLASTYEAR(Dim_Date[Date])
    )
RETURN
    DIVIDE(CurrentYear - PreviousYear, PreviousYear)
```

#### Power Query Transformations
**Question**: How do you handle slowly changing dimensions in Power Query?

**Answer**:
- Use merge operations to detect changes
- Add conditional columns for change detection
- Use Table.Buffer for performance on large datasets
- Implement custom functions for complex transformations

### 4. SQL Advanced Patterns

#### Window Functions
**Question**: Write a query to find the top 3 products per category by sales.

**Answer**:
```sql
WITH ranked_products AS (
    SELECT 
        p.category,
        p.product_name,
        SUM(s.amount) as total_sales,
        ROW_NUMBER() OVER (
            PARTITION BY p.category 
            ORDER BY SUM(s.amount) DESC
        ) as rank
    FROM FACT_SALES s
    JOIN DIM_PRODUCT p ON s.product_id = p.product_id
    GROUP BY p.category, p.product_name
)
SELECT category, product_name, total_sales
FROM ranked_products
WHERE rank <= 3;
```

#### Common Table Expressions
**Question**: When would you use CTEs vs subqueries?

**Answer**:
- **CTEs**: Better readability, can reference multiple times, easier to debug
- **Subqueries**: Simple one-time use, embedded in main query
- Use CTEs for complex logic, multiple references, or recursive queries

### 5. Data Engineering Concepts

#### ETL vs ELT
**Question**: Explain the difference between ETL and ELT.

**Answer**:
- **ETL**: Extract → Transform → Load. Transform before loading to target. Good for legacy systems with limited compute.
- **ELT**: Extract → Load → Transform. Load raw data first, transform in target. Better for modern cloud warehouses with powerful compute (Snowflake, BigQuery).

#### Data Pipeline Design
**Question**: How do you design a resilient data pipeline?

**Answer**:
- **Idempotency**: Safe to re-run without side effects
- **Error Handling**: Retry logic, dead letter queues
- **Monitoring**: Logging, alerts, metrics
- **Testing**: Unit tests, integration tests, data validation
- **Documentation**: Clear data lineage and business rules

## Behavioral Questions

### Problem Solving
**Question**: Tell me about a time you solved a complex data problem.

**STAR Framework**:
- **Situation**: Describe the context and challenge
- **Task**: What you needed to accomplish
- **Action**: Steps you took, technical approach
- **Result**: Quantifiable outcomes, lessons learned

### Collaboration
**Question**: How do you work with stakeholders to understand requirements?

**Key Points**:
- Ask clarifying questions to understand business needs
- Translate business requirements to technical specifications
- Provide prototypes or mockups for validation
- Communicate technical constraints clearly
- Iterate based on feedback

### Continuous Learning
**Question**: How do you stay current with BI and data engineering trends?

**Key Points**:
- Follow industry blogs and publications
- Participate in online communities (Stack Overflow, Reddit)
- Take online courses and certifications
- Experiment with new tools and technologies
- Share knowledge with team members

## Technical Assessment Preparation

### SQL Practice
- Practice window functions (ROW_NUMBER, RANK, LAG, LEAD)
- Master JOIN types and when to use each
- Understand aggregation and grouping
- Practice subqueries and CTEs

### Snowflake Practice
- Learn Snowflake-specific syntax and functions
- Practice writing efficient queries
- Understand clustering and micro-partitions
- Familiarize with Snowflake's INFORMATION_SCHEMA

### Power BI Practice
- Build reports from scratch
- Practice DAX measure writing
- Understand data modeling principles
- Learn Power Query transformations

## Mock Interview Scenarios

### Scenario 1: Performance Issue
**Problem**: A Power BI report is taking 10 minutes to load. How would you troubleshoot?

**Approach**:
1. Identify bottlenecks (data source vs DAX vs visual)
2. Check data model (relationships, cardinality)
3. Optimize DAX measures (remove CALCULATE filters, use variables)
4. Review Power Query transformations
5. Consider DirectQuery vs Import mode
6. Implement incremental refresh

### Scenario 2: Data Quality Issue
**Problem**: Users report inconsistent numbers between two reports. How would you investigate?

**Approach**:
1. Compare data sources and refresh schedules
2. Review data model and relationships
3. Check measure definitions and calculations
4. Verify filters and slicers
5. Examine data transformations
6. Document findings and implement fixes

## Tips for Success

### Before the Interview
- Research the company and their tech stack
- Review your past projects and be ready to discuss them
- Practice explaining technical concepts clearly
- Prepare questions to ask the interviewer

### During the Interview
- Think aloud when solving technical problems
- Ask clarifying questions before answering
- Be honest about what you don't know
- Show enthusiasm for learning and growth
- Provide specific examples from your experience

### After the Interview
- Send a thank-you note
- Reflect on areas for improvement
- Continue learning based on feedback
