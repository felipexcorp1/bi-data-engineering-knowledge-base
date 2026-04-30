# Power BI / DAX Skill

## Overview
Expertise in Power BI report development, data modeling, DAX measure creation, and performance optimization for enterprise BI solutions.

## Capabilities

### Data Modeling
- Star schema design (facts and dimensions)
- Relationship types (one-to-many, many-to-many)
- Cardinality and cross-filter direction
- Calculated columns vs measures
- Hierarchies and drill-down

### DAX Measures
- Aggregation functions (SUM, AVERAGE, COUNT)
- Iterator functions (SUMX, AVERAGEX)
- Filter functions (CALCULATE, FILTER, ALL)
- Time intelligence (SAMEPERIODLASTYEAR, DATESYTD)
- Variables for performance (VAR, RETURN)

### Power Query (M Language)
- Data transformation and cleansing
- Merge and append queries
- Custom functions
- Error handling
- Query folding optimization

### Report Design
- Visualization best practices
- Bookmarks and buttons
- Drill-through and tooltips
- Row-level security (RLS)
- Paginated reports

### Performance Optimization
- Data model optimization
- Aggregations and composite models
- Incremental refresh
- DirectQuery vs Import mode
- DAX query optimization

## Code Style Guidelines

### DAX Measures
```dax
-- Use descriptive names
Total Sales = SUM(FACT_SALES[AMOUNT])

-- Use variables for complex calculations
Sales YoY % = 
VAR CurrentSales = [Total Sales]
VAR PriorSales = 
    CALCULATE(
        [Total Sales],
        SAMEPERIODLASTYEAR(DIM_DATE[DATE])
    )
RETURN
    DIVIDE(CurrentSales - PriorSales, PriorSales, 0)

-- Format for readability
Complex Measure =
VAR Step1 = CALCULATE([Base Measure], FILTER(DIM_PRODUCT, [Condition]))
VAR Step2 = CALCULATE(Step1, REMOVEFILTERS(DIM_REGION))
RETURN
    Step2
```

### Naming Conventions
- Measures: [Descriptive Name] (spaces allowed)
- Columns: ColumnName (no spaces, PascalCase)
- Tables: TableName (no spaces, PascalCase)
- Variables: VariableName (PascalCase)

### Comments
```dax
-- Single line comment
/*
   Multi-line comment for complex logic
   explaining business rules
*/
```

## Best Practices
1. Use star schema for optimal performance
2. Create measures instead of calculated columns when possible
3. Use variables to improve readability and performance
4. Avoid bi-directional relationships unless necessary
5. Implement incremental refresh for large datasets
6. Use aggregations for high-cardinality columns
7. Test measures with different filter contexts

## Common Patterns

### Time Intelligence
```dax
-- Year-to-Date
YTD Sales = TOTALYTD([Total Sales], DIM_DATE[DATE])

-- Previous Period
Prior Month Sales = CALCULATE([Total Sales], PREVIOUSMONTH(DIM_DATE[DATE]))

-- Rolling Average
12M Rolling Avg = 
AVERAGEX(
    DATESINPERIOD(DIM_DATE[DATE], MAX(DIM_DATE[DATE]), -12, MONTH),
    [Total Sales]
)
```

### Slicers and Filters
```dax
-- Filter based on slicer selection
Selected Region Sales = 
CALCULATE(
    [Total Sales],
    ALLSELECTED(DIM_REGION[REGION_NAME])
)

-- Dynamic ranking
Top N Customers = 
VAR TopN = SELECTEDVALUE(PARAM_TOPN[VALUE], 10)
RETURN
    CALCULATE(
        [Total Sales],
        TOPN(TopN, ALL(DIM_CUSTOMER[CUSTOMER_NAME]), [Total Sales], DESC)
    )
```

### Error Handling
```dax
-- Safe division
Safe Division = 
DIVIDE([Numerator], [Denominator], 0)

-- Handle blanks
Non Blank Sales = 
IF(
    ISBLANK([Total Sales]),
    0,
    [Total Sales]
)
```

## Business Context
Reports should:
- Align with business questions and KPIs
- Provide actionable insights
- Be optimized for end-user performance
- Follow data visualization best practices
- Include appropriate context and explanations
