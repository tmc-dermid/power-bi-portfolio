# Advanced DAX

This file contains selected examples of advanced DAX measures and techniques.

## Store 5 Profit (KEEPFILTERS)
Calculates profit for Store 5 while preserving existing filters applied to the store.

```DAX
Store 5 Profit (KEEPFILTERS) =
CALCULATE(
    [Profit],
    KEEPFILTERS('Store Lookup'[store_id] = 5)
)
```


## Number of Employees (CROSSFILTER)
Counts employees while temporarily changing the relationship between the sales and employee tables to allow filters to flow in both directions.

```DAX
Number of Employees (CROSSFILTER) =
CALCULATE(
    COUNTROWS('Employee Lookup'),
    CROSSFILTER(
        'Sales by Store'[staff_id],
        'Employee Lookup'[staff_id],
        Both
    )
)
```


## % of Store Sales
Calculates the percentage of customer sales for the current store by removing the store filter to compare it with total sales across all stores.

```DAX
% of Store Sales = 
VAR AllStoreSales =
    CALCULATE(
        [Customer Sales],
        REMOVEFILTERS('Store Lookup'[store_id])
    )

VAR Ratio =
    DIVIDE(
        [Customer Sales],
        AllStoreSales,
        "-"
    )

RETURN
    Ratio
```


## Sales by Employee Name (CONCATENATEX)
Creates a dynamic text value that combines the selected employee's name with their percentage of customer sales using CONCATENATEX.

```DAX
Sales by Employee Name (CONCATENATEX) = 
IF (
    HASONEVALUE('Employee Lookup'[first_name]),
    "Employee: " &
    CONCATENATEX(
        VALUES('Employee Lookup'[first_name]),
        'Employee Lookup'[first_name] & " - " & FORMAT([% of Customer Sales], "Percent"),
        ", ",
        'Employee Lookup'[first_name],
        ASC
    ),
    "Select a Single Employee"
)
```


## Customer Sales YoY % Change
Calculates the year-over-year percentage change in customer sales by comparing current sales with sales from the same period in the previous year.

```DAX
Customer Sales YoY % Change = 
VAR LastYearSales =
    CALCULATE(
        [Customer Sales],
        SAMEPERIODLASTYEAR('Calendar'[Transaction_Date])
    )

VAR Ratio =
    DIVIDE(
        [Customer Sales] - LastYearSales,
        LastYearSales,
        "-"
    )

RETURN
    Ratio
```


## MTD Sales (4-5-4)
Calculates month-to-date customer sales based on a 4-5-4 fiscal calendar and returns a result only when a single fiscal month is selected.

```DAX
MTD Sales (4-5-4) = 
VAR MaxDate = MAX('4-5-4 Calendar'[Date])
VAR MaxPeriod = MAX('4-5-4 Calendar'[FiscalMonthYear])
VAR Output =
    IF(
        HASONEVALUE('4-5-4 Calendar'[FiscalMonthYear]),
        CALCULATE(
            [Customer Sales],
            '4-5-4 Calendar'[Date] <= MaxDate,
            '4-5-4 Calendar'[FiscalMonthYear] = MaxPeriod
        ),
        "-"
    )

RETURN
    Output
    
```


## Top 5 Products by Profit (RANKX)
Ranks products by profit and returns results for the top five products.

```DAX
Top 5 Products by Profit (RANKX) = 
VAR ProfitRank =
    IF(
        HASONEVALUE('Product Lookup'[product_category]),
        RANKX(
            ALL('Product Lookup'),
            [Customer Sales] - [Cost]
        )
    )

VAR Top5Products =
    IF(
        ProfitRank <= 5,
        [Profit]
    )

RETURN
    Top5Products
```