# Practice Prerequisites

Following prerequisites and setup must be complete for successful completion of the exercise: 
- Download and install Power BI Desktop. Use the Microsoft App Store to download and install Power BI Desktop app. 
- Download the Power BI practice file: Go to https://github.com/viktorradu/daxfieldguide/ and download the practice.pbix file.  


# Dataset
The practice dataset contains a star schema reflecting sales across multiple products, dates, and locations. The existing model is sufficient to support every practice problem described below, so there is no need to add or modify any relationships.
Please review the data model to familiarize yourself with the existing tables and relationships.
Most exercises refer to sales transactions stored in the FactSales table. The sale amount is stored in the Amount column.

# Practice Problems

## DAX basics 1
Create a calculated column in the Product table. 

**Column name**: Tax per Unit

**Calculation**: Unit Price multiplied by Tax Rate. 
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
Tax per Unit = 'Product'[Unit Price] * 'Product'[Tax Rate] / 100
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

**Calculation:** Average Unit Price from the FactSale table
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
Card with a slicer by Color set to Blue |20.84%
Table containing Color, Amount, and Sales Ratio to All | The Sales All Colors shows different values for each row. Gray is 3.44% and Red is 2.74%.

<br/>

<details>
<summary>View Hint</summary>

1. Capture sales SUM in the current context.
2. Capture sales SUM for ALL fact records
3. Divide sales in current context by the sales fo all records
4. Change the measure Format to %
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

**Calculation:** Total sales from the beginning of the current year to the current date. Base this calculation on the MonthEnd column. Make sure a nonblank value is only returned when the total sales in the current context are not blank.
Test | Result
-|-
Card with no filters on the page|53.67M
Card with a slicer by Date set to dates from 1/31/2013 to 8/31/2015|85.12M
Line and Stacked Column Chart built for MonthEnd, Amount, and Sales YTD|<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/time-intelligence-1.png" />

<br/>

<details>
<summary>View Hint</summary>

1. Use TOALYTD() or DATESYTD() with a CALCULATE()
2. Use the IF() and ISBLANK() functions to make sure the expression returns BLANK() when the total of sales is blank.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales YTD = IF(
    ISBLANK(SUM(FactSale[Amount])), 
    BLANK(), 
    TOTALYTD(SUM(FactSale[Amount]), 'Date'[MonthEnd])
)
```
</details>
<br/>

## Time intelligence 2
**Measure name:** Sales MoM Delta

**Calculation:** Difference between the total sales for the current month and the previous month. Base this calculation on the MonthEnd column. Make sure a nonblank value is only returned when the total sales in the current context are not blank.
Test | Result
-|-
Card with a slicer by Date set to dates from 1/31/2013 to 8/31/2015|-3.37M
Line and Stacked Column Chart built for MonthEnd, Amount, and Sales MoM Delta|<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/time-intelligence-2.png" />

<br/>

<details>
<summary>View Hint</summary>

1. Calculate total sales in the current context.
2. Calculate sales in previous month using the CALCULATE() and PREVIOUSMONTH() functions.
3. Subtract previous month amount from the current context amount.
4. Use the IF() and ISBLANK() functions to make sure the expression returns BLANK() when the total of sales is blank.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales MoM delta = 
var currentMonthend = MAX('Date'[MonthEnd])
var currentPeriod = CALCULATE(SUM(FactSale[Amount]), 'Date'[MonthEnd] = currentMonthend)
var prevPeriod = CALCULATE(SUM(FactSale[Amount]), 'Date'[MonthEnd] =  EOMONTH(currentMonthend, -1))
var result = IF(ISBLANK(currentPeriod), BLANK(), currentPeriod - prevPeriod)
return result
```
</details>
<br/>

## Time intelligence 3
**Measure name:** Sales L3M Average

**Calculation:** Average of the total sales over the current and two previous months. Base this calculation on the MonthEnd column.
Test | Result
-|-
Card with a slicer by Date set to dates from 1/31/2013 to 8/31/2015|133.10K
Line and Stacked Column Chart built for MonthEnd, Amount, and Sales L3M Average|<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/time-intelligence-3.png" />

<br/>

<details>
<summary>View Hint</summary>

1. Capture the value of the currently selected monthend
2. Use the DATESINPERIOD function to calculate the list of monthends in the last 3 months.
3. Use the CALCULATE() function to determinte the total sales providing the list of dates as the second argument.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Sales L3M Average = 
var currentMonthend = SELECTEDVALUE('Date'[MonthEnd])
var last3months = DATESINPERIOD('Date'[MonthEnd], currentMonthend, -3, MONTH)
var result = CALCULATE(SUM(FactSale[Amount]), last3months) / 3
return result
```
</details>
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

<details>
<summary>View Hint</summary>

1. Capture the Territory Priority value selected in the current context
2. CALCULATE the total sales where Territory Priority is less then or equal to the selected value. Make sure you include all territories by removing all filters from the City table using the ALL function. 
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Cumulative by Category = 
var currentCategory = MAX(City[Territory Priority])
var result = CALCULATE(SUM(FactSale[Amount]), ALL(City),City[Territory Priority] <= currentCategory)
return result
```
</details>
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

<details>
<summary>View Hint</summary>

1. Capture the currently selected territory
2. Build a summary table of all sales territories and the corresponding sales. Use SUMMARIZE() and ALL() function to do this.
3. Add a rank column to the summary table using ADDCOLUMNS() and RANKX() functions.
4. Find the current selected territory rank from teh summary table using FILTER() and MAXX() functions.
5. Calculate total sales for the territories in the summary table that have a rank that is same as or below the currently selected territory
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Cumulative by Value = 
var currentTerritory = SELECTEDVALUE(City[Sales Territory])
var categories = SUMMARIZE(ALL(City), City[Sales Territory], "Sales", SUM(FactSale[Amount]))
var rankedCategories = ADDCOLUMNS(categories, "Rank", RANKX(categories, [Sales], [Sales], DESC))
var currentCategoryRank = MAXX(FILTER(rankedCategories, City[Sales Territory] = currentTerritory), [Rank])
var result = SUMX(FILTER(rankedCategories, [Rank] <= currentCategoryRank || ISBLANK(currentCategoryRank)), [Sales])
return result
```
</details>
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

<details>
<summary>View Hint</summary>

1. Capture the total sales across all territories.
2. Divide the Cumulative by Value measure by the total sales.
3. Apply the % format to the measure.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Cumulative Contribution = 
var totalSales = CALCULATE([Cumulative by Value], ALL(City))
var result = DIVIDE( [Cumulative by Value], totalSales)
return result
```
</details>
<br/>

## Virtual Relationships 1
**Measure name:** Same City Ratio

**Calculation:** Ratio of total sales in the currently selected city (assuming there is only one selected) to the sales of all products manufactured in the currently selected city. Use the existing inactive relationship beween Product and City table.
Test | Result
-|-
Table visualizing City Key, City, and Same City Ratio |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/virtual-relationships-1.png" />

<br/>

<details>
<summary>View Hint</summary>

1. Capture the currently selected city key
2. Calculate total sales in the current city
3. Calculate the sales amount with the relationship between Manufacture City Key and City Key activated
4. Divide total sales in the current city by the sales from step 3
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Same City Ratio = 
var _city = SELECTEDVALUE(City[City Key])
var salesOfAll = sum(FactSale[Amount])
var salesOfManufactured = CALCULATE(SUM(FactSale[Amount]), USERELATIONSHIP('Product'[Manufacture City Key], City[City Key]), 'Product'[Manufacture City Key] = _city)
var result = DIVIDE(salesOfAll, salesOfManufactured)
return result
```
</details>
<br/>

## Virtual Relationships 2
**Measure name:** Same City Ratio Treatas

**Calculation:** Ratio of total sales in the currently selected city (assuming there is only one selected) to the sales of all products manufactured in the currently selected city. Use TREATAS function to apply a City table filter to the Product table.
Test | Result
-|-
Table visualizing City Key, City, and Same City Ratio |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/virtual-relationships-2.png" />

<br/>

<details>
<summary>View Hint</summary>

1. Capture the currently selected city key
2. Calculate total sales in the current city
3. Calculate the sales amount for ALL cities while including the TREATAS filter of the Manufacture City Key by the selected city key
4. Divide total sales in the current city by the sales from step 3
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Same City Ratio Treatas = 
var _cityKey = SELECTEDVALUE(City[City Key])
var salesOfAll = sum(FactSale[Amount])
var salesOfManufactured = CALCULATE(SUM(FactSale[Amount]), All(City), TREATAS({_cityKey}, 'Product'[Manufacture City Key]))
var result = DIVIDE(salesOfAll, salesOfManufactured)
return result
```
</details>
<br/>

## Disconnected Table
**Measure name:** Histogram

**Calculation:** Count of sales tansactions that fall within the currently selected bin (column Bins[Value]) by Quantity. Count of bins used for the histogram should be determined by the selection in the Bins slicer.
Test | Result
-|-
Column Chart visualizing Value and Histogram with a slicer by Value set to 0-4 |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/disconnected-table-1.png" />
Column Chart visualizing Value and Histogram with a slicer by Value set to 0-10 |<img src="https://github.com/viktorradu/daxfieldguide/raw/main/img/disconnected-table-2.png" />

<br/>

<details>
<summary>View Hint</summary>

1. Capture the currently selected bin.
2. Calculate the total count of bins available in the current visualization.
3. Calculate the bin size. Divide the maximum sales amount by the count of bins.
4. Count the sales transactions that have Amount falling within the current bin range. Use COUNTROWS and FILTER functions.
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Histogram = 
var currentBin = SELECTEDVALUE(Bins[Value])
var totalBins = CALCULATE(max(Bins[Value]), ALLSELECTED(Bins[Value]))
var binSize = max(FactSale[Amount]) / totalBins
var result = COUNTROWS(FILTER(FactSale, FactSale[Amount] > (currentBin -1)* binSize && FactSale[Amount] <= currentBin * binSize))
return result
```
</details>
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

<details>
<summary>View Hint</summary>

Regression Actual
1. Capture the earliest monthend available in the Regression Date table in the current context.
2. Capture the latest monthend available in the Regression Date table in the current context.
3. Calculate the sales amount where the monthend from the Date table falls fithin the range from step 1 to step 2. 

Regression
1. Capture the input table that summarizes ALLSELECTED sales transactions and contains Date[Monthend] as the grouping table and Regression Actual measure as the Sales column.
2. Filter the summary table to remove any rows that have blank Sales.
3. Use LINESTX function to calculate the linear regression parameter for the summary table setting Sales as the Y value and Monthend as the X value.
4. Use the slope * X + intercept formula to calculate the regression value for the currently selected Monthend
</details>

<br/>

<details>
<summary>View Solution</summary>

```DAX
Regression Actual = 
var datesFrom = MIN('Regression Date'[MonthEnd])
var datesTo = MAX('Regression Date'[MonthEnd])
var result = CALCULATE( SUM(FactSale[Amount]), KEEPFILTERS('Date'), 'Date'[MonthEnd] >= datesFrom && 'Date'[MonthEnd] <= datesTo)
return result

Regression = 
var inputTable = SUMMARIZE(ALLSELECTED(FactSale), 'Date'[MonthEnd], "Sales", [Regression Actual])
var nonBlankInput = FILTER(inputTable, NOT(ISBLANK([Sales])))
var linReg = LINESTX(nonBlankInput, [Sales], 'Date'[MonthEnd])
var slope = MAXX(linReg, [Slope1])
var intercept = MAXX(linReg, [Intercept])
var line = slope * SELECTEDVALUE('Date'[MonthEnd]) + intercept
return line
```
</details>
<br/>