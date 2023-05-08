# Practice Prerequisites

Following prerequisites and setup must be complete for successful completion of the exercise: 
- Download the Power BI Content: Go to https://github.com/viktorradu/daxfieldguide/blob/main/assets.zip  and download the Assets.zip file. Create a folder called DAX on the C drive of your local machine. Copy all contents from the archive to the DAX folder you just created (C:\DAX).  
- Download and install Power BI Desktop. Use the Microsoft App Store to download and install Power BI Desktop app.  


# Dataset
The practice dataset contains a star schema reflecting sales across multiple stock items, dates, and locations. The existing model is sufficient to support every practice problem solution, so there is no need to add or modify any relationships.
Please review the data model to familiarize yourself with the existing tables and relationships.
Most exercises refer to sales transactions stored in the FactSales table. The sale amount is stored in the Amount column.

# Practice Problems

## DAX basics 1
Create a calculated column in the Product table. 

**Column name**: Tax per Unit

**Calculation**: Unit Price multiplied by Tax Rate
Test | Result
---|---
Card visualizing the Sum of the new column | 4.53K
Same card with a slicer by Color set to Red | 392

**Explore**: Open Data page and review the new column.

<br/>

<details>
<summary>View Hint</summary>

1. Select the Product table in the Data pane.
2. Click New column in the Modeling ribbon menu
3. Type the column expression in the Formula bar
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Tax per Unit = 'Product'[Unit Price] * 'Product'[Tax Rate]
```
</details>

<br/>

## DAX basics 2
**Measure name:** Sales Count

**Calculation:** Count the number of Sales transactions in the current context
Test | Result
-|-
Card with no filters on the page	| 228K
Card with a slicer by Color set to Red |	5215

<br/>

<details>
<summary>View Hint</summary>

1. Select the .Exercise Measures table in the Data pane.
2. Click New measure in the Modeling ribbon menu
3. Type the column expression in the Formula bar. Use COUNTROWS() function.
</details>

<br/>

<details>
<summary>View Solution</summary>

``` DAX
Sales Count = COUNTROWS(FactSale)
```
</details>
<br/>

## DAX basics 3
**Measure name:** All Sales Count

**Calculation:** Count the number of Sales transactions ignoring all filters applied in the current context
Test | Result
-|-
Card with no filters on the page | 228K
Card with a slicer by Color set to Red | 228K

<br/>

<details>
<summary>View Hint</summary>

1. Select the .Exercise Measures table in the Data pane.
2. Click New measure in the Modeling ribbon menu
3. Type the column expression in the Formula bar. Use COUNTROWS() and ALL() functions
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
All Sales Count = COUNTROWS(ALL('FactSale'))
```
</details>
<br/>

## DAX basics 4
**Measure name:** Average Unit Price

**Calculation:** Average Unit Price from the Product table
Test | Result
-|-
Card with no filters on the page | 45
Card with a slicer by Color set to Red | 162


**Explore:** Visualize Average Unit Price by Color as a Stacked Column Chart 

<details>
<summary>View Hint</summary>

1. Select the .Exercise Measures table in the Data pane.
2. Click New measure in the Modeling ribbon menu.
3. Type the column expression in the Formula bar. Use AVERAGE() function.<br/>
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Average Unit Price = AVERAGE(FactSale[Unit Price])
```

</details>
<br/>

## CALCULATE 1
**Measure name:** Sales Gray

**Calculation:** Total sales of Gray products
Test | Result
-|-
Card with no filters on the page  |13.96M
Card with a slicer by Size set to M	|3.31M
Table containing Color, Amount, Sales Grey	|The Sales Gray column shows 13.96M in every row including Total

<br/>

<details>
<summary>View Hint</summary>
Use CALCULATE() function. Specify the aggregation expression as the first argument and the filter by Color as the second argument.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales Gray = CALCULATE(sum(FactSale[Amount]), 'Product'[Color] = "Gray")
```
</details>
<br/>

## CALCULATE 2
**Measure name:** Sales Gray LM

**Calculation:** Total sales of Gray products of size L or M.
Test | Result
-|-
Card with no filters on the page|6.86M
Card with a slicer by Size set to M|6.86M
Table containing Size, Amount, Sales Gray LM|The Sales Gray LM column shows 6,855,536 in every row including Total

<br/>

<details>
<summary>View Hint</summary>
Use CALCULATE() function. Reuse the Sales Gray measure and add filters by Size.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales Gray LM = CALCULATE([Sales Gray], 'Product'[Size] = "L" || 'Product'[Size] = "M")
```
</details>
<br/>

## CALCULATE 3

**Measure name:** Sales Gray LM Filtered

**Calculation:** Results of Sales Gray measure for products of selected sizes only for sizes L and M 
Test | Result
-|-
Card with no filters on the page | 6.86M
Card with a slicer by Size set to M | 3.31M
Card with a slicer by Size set to S | (Blank)
Table containing Size, Amount, Sales Gray LM Filtered	 | The Sales Gray LM Filtered column shows blanks except <br/>~3.5M for Size L <br/>~3.3M for Size M <br/>~6,8M in the Total row

<br/>

<details>
<summary>View Hint</summary>
Use CALCULATE() function. Reuse the Sales Gray measure and add filters by Size. Use the KEEPFILTERS function to preserve the Size filter context set by slicers.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales Gray LM Filtered = CALCULATE([Sales Gray], KEEPFILTERS('Product'[Size] = "L" || 'Product'[Size] == "M"))
```
</details>
<br/>

## ALL 1

**Measure name:** Sales All Colors

**Calculation:** Total sales for all colors
Test | Result
-|-
Card with no filters on the page	|405.73M
Card with a slicer by Color set to Blue |405.73M
Card with a slicer by Size set to M | 14.12M
Table containing Color, Amount, Sales All Colors | The Sales All Colors shows 405,729,746 in every row
	
<br/>

<details>
<summary>View Hint</summary>
Use CALCULATE() and ALL() functions.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales All Colors = CALCULATE(SUM(FactSale[Amount]), ALL('Product'[Color]))
```
</details>
<br/>

## ALL 2

**Measure name:** Sales All Selected Colors

**Calculation:** Total sales for all colors selected by external slicers and filters. Visual's own filter by Color should be ignored.
Test | Result
-|-
Card with no filters on the page	|405.73M
Card with a slicer by Color set to Blue |84.56M
Card with a slicer by Size set to M | 14.12M
Table containing Color, Amount, Sales All Colors | The Sales All Colors shows 405,729,746 in every row
Table containing Color, Amount, Sales All Selected Colors with a slicer set to Blue and Gray (hold Ctrl shen selecting multiple values) | The Sales All Selected Colors shows 98,527,252 in every row

<br/>

<details>
<summary>View Hint</summary>
Use CALCULATE() and ALLSELECTED() functions.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales All Selected Colors = CALCULATE(SUM(FactSale[Amount]), ALLSELECTED('Product'[Color]))
```
</details>
<br/>

## ALL 3

**Measure name:** Sales Ratio to All

**Calculation:** A ratio of total sales in the current context to the total sales in the data model

Test | Result
-|-
Card with no filters on the page	|100%
Card with a slicer by Color set to Blue |84.56M
Card with a slicer by Size set to M | 14.12M
Table containing Color, Amount, and Sales All Colors | The Sales All Colors shows 405,729,746 in every row
Table containing Color, Amount, and Sales All Selected Colors with a slicer set to Blue and Gray (hold Ctrl shen selecting multiple values) | The Sales All Selected Colors shows 98,527,252 in every row

<br/>

<details>
<summary>View Hint</summary>

1. Capture sales SUM in the current context.
2. Capture sales SUM for ALL fact records
3. Divide sales in current context by the sales fo all records
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales Ratio to All = 
var colorSales = SUM(FactSale[Amount])
var totalSales = CALCULATE(SUM(FactSale[Amount]), ALL(FactSale))
var r = DIVIDE(colorSales, totalSales)
return r
```
</details>
<br/>

## Time intelligence 1
**Measure name:** Sales YTD

**Calculation:** Total sales from the beginning of the current year to the current date. Base this calculation on the MonthEnd column.
Test | Result
-|-
Card with no filters on the page|53.67M
Card with a slicer by Date set to dates from 1/31/2013 to 8/31/2015|85.12M
Line and Stacked Column Chart built for MonthEnd, Amount, and Sales YTD|<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/time-intelligence-1.png" />

<br/>

## Time intelligence 2
**Measure name:** Sales MoM Delta

**Calculation:** Difference between the total sales for the current month and the previous month. Base this calculation on the MonthEnd column.
Test | Result
-|-
Card with a slicer by Date set to dates from 1/31/2013 to 8/31/2015|8.80M
Line and Stacked Column Chart built for MonthEnd, Amount, and Sales MoM Delta|<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/time-intelligence-2.png" />

<br/>

## Time intelligence 3
**Measure name:** Sales L3M Average

**Calculation:** Average of the total sales over the current and two previous months. Base this calculation on the MonthEnd column.
Test | Result
-|-
Card with a slicer by Date set to dates from 1/31/2013 to 8/31/2015|10.47M
Line and Stacked Column Chart built for MonthEnd, Amount, and Sales L3M Average|<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/time-intelligence-3.png" />

<br/>

## Cumulative total 1
**Measure name:** Cumulative by Category

**Calculation:** Total sales of current Sales Territory and all previous sales territories when ordered by Territory Priority column.
Test | Result
-|-
Card with a slicer by Color set to Gray|14M
Card with a slicer by Color set to Gray and a slicer by Sales Territory set to Great Lakes|9M
Line and Stacked Column Chart built for Sales Territory, Amount, and Cumulative by Category sorted by Sales Territory |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/cumulative-total-1.png" />

<br/>


## Cumulative total 2
**Measure name:** Cumulative by Value

**Calculation:** Total sales of current Sales Territory and all sales territories with lower sales.
Test | Result
-|-
Card with a slicer by Color set to Gray|14M
Card with a slicer by Color set to Gray and a slicer by Sales Territory set to Great Lakes|11M
Line and Stacked Column Chart built for Sales Territory, Amount, and Cumulative by Category sorted by Sales Territory |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/cumulative-total-2.png" />

<br/>

## Cumulative total 3
**Measure name:** Cumulative Contribution

**Calculation:** Ratio of the current Sales Territory cumulative sales by value (exercise 2) and total sales across all territories.
Test | Result
-|-
Card with a slicer by Color set to Gray|100%
Card with a slicer by Color set to Gray and a slicer by Sales Territory set to Great Lakes|77%
Line and Stacked Column Chart built for Sales Territory, Amount, and Cumulative by Category sorted by Sales Territory |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/cumulative-total-3.png" />

<br/>

## Virtual Relationships 1
**Measure name:** Same City Ratio

**Calculation:** Ratio of total sales in the currently selected city (asuming there is onle one selected) to the sales of all products manufactured in the currently selected city. Use the existing inactive relationship beween Product and City table.
Test | Result
-|-
Table visualizing City Key, City, and Same City Ratio |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/virtual-relationships-1.png" />

<br/>

## Virtual Relationships 2
**Measure name:** Same City Ratio Treatas

**Calculation:** Ratio of total sales in the currently selected city (asuming there is onle one selected) to the sales of all products manufactured in the currently selected city. Use TREATAS function to apply a City table filter to the Product table.
Test | Result
-|-
Table visualizing City Key, City, and Same City Ratio |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/virtual-relationships-2.png" />

<br/>

## Disconnected Table
**Measure name:** Histogram

**Calculation:** Count of sales tansactions that fall within the currently selected bin (column Bins[Value]) by Quantity. Count of bins used for the histogram should be determined by the selection in the Bins slicer.
Test | Result
-|-
Column Chart visualizing Value and Histogram with a slicer by Value set to 0-4 |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/disconnected-table-1.png" />
Column Chart visualizing Value and Histogram with a slicer by Value set to 0-10 |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/disconnected-table-2.png" />

<br/>

## Linear Regression
**Measure 1 name:** Regression Actual

**Calculation:** Total sales filtered by the MonthEnd column from Regression Date table.

Test | Result
-|-
card with no filters on the page | 405.73M
card with a slicer by 'Regression Date'[MonthEnd] column set to 2/10/2014 - 2/10/2016 | 247.27M


**Measure 2 name:** Regression

**Calculation:** Prediction Regression Actual value based on linear regression.
Test | Result
-|-
Line Chart visualizing Date[MonthEnd], Regression Actual, and Regression with a slicer by 'Regression Date'[MonthEnd] set to 12/6/2013 - 3/30/2016 |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/linear-regression-1.png" />
Line Chart visualizing Date[MonthEnd], Regression Actual, and Regression with a slicer by 'Regression Date'[MonthEnd] set to 12/24/2014 - 3/30/2016 |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/linear-regression-2.png" />

<br/>