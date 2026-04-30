# Airing List Report - Power BI Implementation

## Business Context
Report tracking media content airing with cost calculations aggregated at Rights Number level.

## Data Model (Star Schema)

### Fact Table: Fact_Airing
- Airing_ID (Key)
- Rights_Number (Foreign Key)
- Program_ID (Foreign Key)
- Date_ID (Foreign Key)
- Airing_Date
- Duration_Minutes
- Viewers_Count
- Revenue

### Dimension Tables
- Dim_Rights (Rights_Number, Contract_ID, Cost_Per_Unit, Start_Date, End_Date)
- Dim_Program (Program_ID, Program_Name, Genre, Language)
- Dim_Date (Date_ID, Date, Month, Quarter, Year)

## DAX Measures

### Cost Calculation
```dax
-- Calculate cost only when contract exists
Total Cost = 
CALCULATE(
    SUM(Fact_Airing[Duration_Minutes]) * AVERAGE(Dim_Rights[Cost_Per_Unit]),
    FILTER(
        Dim_Rights,
        Dim_Rights[Contract_ID] <> BLANK()
    )
)

-- Alternative: Handle no contract = no cost
Cost with Contract Check = 
VAR HasContract = 
    CALCULATE(
        COUNTROWS(Dim_Rights),
        Dim_Rights[Contract_ID] <> BLANK()
    )
RETURN
    IF(
        HasContract > 0,
        SUM(Fact_Airing[Duration_Minutes]) * AVERAGE(Dim_Rights[Cost_Per_Unit]),
        0
    )
```

### Performance Metrics
```dax
-- Cost per viewer
Cost Per Viewer = 
DIVIDE(
    [Total Cost],
    SUM(Fact_Airing[Viewers_Count])
)

-- Revenue per airing
Revenue Per Airing = 
DIVIDE(
    SUM(Fact_Airing[Revenue]),
    COUNTROWS(Fact_Airing)
)

-- Profit margin
Profit Margin = 
DIVIDE(
    SUM(Fact_Airing[Revenue]) - [Total Cost],
    SUM(Fact_Airing[Revenue])
)
```

### Time Intelligence
```dax
-- Cost by month
Cost MTD = 
CALCULATE(
    [Total Cost],
    DATESMTODAY(Dim_Date[Date])
)

-- Cost vs previous month
Cost vs Prev Month = 
[Total Cost] - CALCULATE(
    [Total Cost],
    DATEADD(Dim_Date[Date], -1, MONTH)
)

-- Year-to-date cost
Cost YTD = 
CALCULATE(
    [Total Cost],
    DATESYTD(Dim_Date[Date])
)
```

## Power Query Transformations

### Rights Number Aggregation
```powerquery
// Group by Rights Number and calculate totals
let
    Source = Fact_Airing_Table,
    Grouped = Table.Group(
        Source,
        {"Rights_Number"},
        {
            {"Total_Duration", each List.Sum([Duration_Minutes]), type number},
            {"Total_Viewers", each List.Sum([Viewers_Count]), type number},
            {"Airing_Count", each Table.RowCount(_), type number}
        }
    ),
    JoinedRights = Table.NestedJoin(
        Grouped,
        {"Rights_Number"},
        Dim_Rights,
        {"Rights_Number"},
        "Rights",
        JoinKind.LeftOuter
    ),
    ExpandedRights = Table.ExpandTableColumn(
        JoinedRights,
        "Rights",
        {"Contract_ID", "Cost_Per_Unit"},
        {"Contract_ID", "Cost_Per_Unit"}
    ),
    AddedCost = Table.AddColumn(
        ExpandedRights,
        "Calculated_Cost",
        each if [Contract_ID] <> null 
             then [Total_Duration] * [Cost_Per_Unit] 
             else 0,
        type number
    )
in
    AddedCost
```

### Date Dimension Creation
```powerquery
let
    StartDate = #date(2020, 1, 1),
    EndDate = DateTime.LocalNow(),
    Dates = List.Dates(StartDate, Duration.Days(EndDate - StartDate), #duration(1, 0, 0, 0)),
    Table = Table.FromList(Dates, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    Renamed = Table.RenameColumns(Table, {{"Column1", "Date"}}),
    AddedYear = Table.AddColumn(Renamed, "Year", each Date.Year([Date]), type number),
    AddedQuarter = Table.AddColumn(AddedYear, "Quarter", each Date.QuarterOfYear([Date]), type number),
    AddedMonth = Table.AddColumn(AddedQuarter, "Month", each Date.Month([Date]), type number),
    AddedMonthName = Table.AddColumn(AddedMonth, "MonthName", each Date.ToText([Date], "MMMM"), type text),
    AddedDayOfWeek = Table.AddColumn(AddedMonthName, "DayOfWeek", each Date.DayOfWeek([Date]), type number)
in
    AddedDayOfWeek
```

## Report Visuals

### Key Visuals
1. **Cost by Rights Number** - Bar chart showing top costs
2. **Cost Trend** - Line chart over time
3. **Cost vs Revenue** - Combo chart
4. **Rights Contract Status** - Donut chart (Contract vs No Contract)
5. **Top Programs by Cost** - Table visual

### Slicers
- Date Range
- Program Genre
- Language
- Contract Status

## Optimization Tips
- Use DirectQuery for large fact tables
- Implement incremental refresh for date dimensions
- Use calculated columns for static values
- Use measures for dynamic calculations
- Set proper data types in Power Query
- Remove unnecessary columns early in transformation
- Use star schema for optimal performance
