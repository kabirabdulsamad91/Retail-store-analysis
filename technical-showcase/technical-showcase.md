# Technical showcase

The techniques behind the Global Electronics Retailer analysis, with the formulas I used. Back to the [main README](README.md).


## What I used and where

| Skill | Where I used it |
| --- | --- |
| Power Query | Cleaned the five source tables, fixed date formats, set data types, added a channel flag and delivery days |
| Data modeling | Star schema with a Date table and relationships between Sales, Stores, Products, Customers and Exchange Rates |
| DAX | Sales, cost, profit, margin, year-on-year growth, sales per sq m, medians, gap to median, label shares |
| Excel formulas | Verdict labels (nested IF with AND), medians, tolerance bands, lookups, counts |
| PivotTables | Store, category and subcategory scorecards, online against physical, store-by-category feasibility check |
| Power BI | Four report pages with slicers, conditional formatting, bubble charts and tables |

## Power Query

- Set the correct data type for every column and standardized inconsistent date formats.
- Added a **Channel** column: a store with no square meters is Online, otherwise Physical.
- Added a **Delivery Days** column: the days between order date and delivery date (online orders only).
- Loaded the cleaned tables into the data model.

## Data model

![Data model](c:\Users\HP\Pictures\model.png)

One central **Sales** table connected to **Stores**, **Products**, **Customers** and a **Date** table, each as one-to-many with a single filter direction.

**Date table (DAX):**

```dax
Date =
ADDCOLUMNS(
    CALENDAR(DATE(2016, 1, 1), DATE(2019, 12, 31)),
    "Year", YEAR([Date]),
    "Month Number", MONTH([Date]),
    "Month", FORMAT([Date], "MMM"),
    "Quarter Number", QUARTER([Date]),
    "Quarter", "Q" & QUARTER([Date]),
    "Weekday Number", WEEKDAY([Date], 2),
    "Day", FORMAT([Date], "ddd")
)
```

## DAX measures

**Sales, cost and profit.** Price and cost sit in the Products table, so each row is multiplied by its product's price and cost:

```dax
Total Sales = SUMX(Sales, Sales[Quantity] * RELATED(Products[Unit Price USD]))
Total Cost = SUMX(Sales, Sales[Quantity] * RELATED(Products[Unit Cost USD]))
Total Profit = [Total Sales] - [Total Cost]
Profit Margin % = DIVIDE([Total Profit], [Total Sales])
```

**Time intelligence.** `DIVIDE` avoids errors when the previous year is zero or blank:

```dax
Sales PY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))
Sales YoY % = DIVIDE([Total Sales] - [Sales PY], [Sales PY])
```

**Benchmarks.** `ALL` keeps the medians fixed when a slicer filters the report:

```dax
Median Sales per Sq M =
CALCULATE(MEDIAN(StoreScorecard[Sales per Sq M]), ALL(StoreScorecard))

Median Avg Growth =
CALCULATE(MEDIAN(StoreScorecard[Average YoY]), ALL(StoreScorecard))
```

**Gap to median sales.** For each store below the median, the extra sales it would make at the median sales per square meter:

```dax
Gap to Median Sales =
VAR Med = [Median Sales per Sq M]
VAR Gap =
    SUMX(
        StoreScorecard,
        MAX(0, Med - StoreScorecard[Sales per Sq M]) * StoreScorecard[Square Meters]
    )
RETURN IF(Gap = 0, BLANK(), Gap)
```

**Share of sales by verdict:**

```dax
Invest Share =
DIVIDE(
    CALCULATE([Total Sales], SubcategoryScorecard[Label] = "Invest"),
    CALCULATE([Total Sales], ALL(SubcategoryScorecard))
)
```

**Online measures:**

```dax
Online Sales = CALCULATE([Total Sales], Stores[Channel] = "Online")
Online Share = DIVIDE([Online Sales], [Total Sales])
Avg Delivery Days = CALCULATE(AVERAGE(Sales[Delivery Days]), Stores[Channel] = "Online")
```

**Calculated columns:**

```dax
Delivery Days =
IF(ISBLANK(Sales[Delivery Date]), BLANK(),
   DATEDIFF(Sales[Order Date], Sales[Delivery Date], DAY))

Color = SWITCH(SubcategoryScorecard[Label],
    "Invest", "#0B6E67", "Maintain", "#5E6E80", "Fix", "#C46A12",
    "Watch", "#8A7421", "Cut", "#A93A28", "#9AA8B6")
```

## Excel formulas

The scorecards use these, with benchmarks in their own cells instead of typed numbers.

```excel
Median benchmark:      =MEDIAN(B2:B33)
Tolerance band:        =0.1*(MAX(B2:B33)-MIN(B2:B33))
Margin level:          =IF(B2>=$J$2-$J$5,"High","Low")
Growth level:          =IF(C2>$J$3+$J$6,"High",IF(C2<$J$3-$J$6,"Low",IF(D2>=$J$4,"High","Low")))
Count per label:       =COUNTIF(G2:G33,"Invest")
Store size lookup:     =VLOOKUP(A2,StoreInfo!A:D,3,FALSE)
```

**Verdict label** (nested IF with AND):

```excel
=IF(AND(E2="High",F2="High"),"Invest",
 IF(AND(E2="High",F2="Low"),IF(D2<=-0.1,"Cut",IF(D2<0,"Watch","Maintain")),
 IF(AND(E2="Low",F2="High"),"Fix",
 IF(D2<=-0.1,"Cut","Watch"))))
```

The `-0.1` is the rule that a Cut needs a 2019 decline of 10% or more.

## PivotTables

- Sales and profit by store and year, with the Year filter from the Date table.
- Year-on-year growth by store, category and subcategory, with Year in the columns so each year is compared fairly.
- Online against physical sales, margin and delivery days by year.
- Refrigerator sales by model, to find where the 2019 decline came from.
- Store-by-category sales in 2019, to test whether the data was thick enough for growth rates (it was not).

![Power Pivot](c:\Users\HP\Pictures\powerpivot.png)
![Power Query](c:\Users\HP\Pictures\powerquery.png)
