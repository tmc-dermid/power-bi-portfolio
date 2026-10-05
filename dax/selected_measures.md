# Selected DAX Measures

This file contains selected DAX measures used in the Power BI report.

## Total Revenue
Calculates total revenue based on the order quantity and related product price.

```DAX
Total Revenue =
SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] *
    RELATED('Product Lookup'[ProductPrice])
)
```


## Return Rate
Calculates the percentage of sold quantities that were returned.

```DAX
Return Rate =
DIVIDE(
    [Quantity Returned],
    [Quantity Sold],
    "No Sales"
)
```


## Previous Month Profit
Calculates the total profit for the previous month based on the current date context.

```DAX
Previous Month Profit =
CALCULATE(
    [Total Profit],
    DATEADD(
        'Calendar Lookup'[Date],
        -1,
        MONTH
    )
)
```


## High Ticket Orders
Calculates the number of orders for products priced above the overall average product price.

```DAX
High Ticket Orders =
CALCULATE(
    [Total Orders],
    FILTER(
        'Product Lookup',
        'Product Lookup'[ProductPrice] > [Overall Average Price]
    )
)
```


## All Orders
Calculates the total number of orders regardless of filters applied to the Sales Data table.

```DAX
All Orders =
CALCULATE(
    [Total Orders],
    ALL('Sales Data')
)
```


## 10-Day Rolling Revenue
Calculates revenue over a rolling 10-day period based on the current date context.

```DAX
10-Day Rolling Revenue =
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(
        'Calendar Lookup'[Date],
        MAX('Calendar Lookup'[Date]),
        -10,
        DAY
    )
)
```


## YTD Revenue
Calculates cumulative revenue from the beginning of the year through the current date context.

```DAX
YTD Revenue =
CALCULATE(
    [Total Revenue],
    DATESYTD(
        'Calendar Lookup'[Date]
    )
)
```