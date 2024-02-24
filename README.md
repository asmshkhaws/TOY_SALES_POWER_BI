# Toy Sales Dashboard

### Dashboard Link : https://app.powerbi.com/view?r=eyJrIjoiN2Y5YWM5ZWItODUyNC00YWY5LWFmMDYtMGQ5MDZkMmUxN2IwIiwidCI6ImRmODY3OWNkLWE4MGUtNDVkOC05OWFjLWM4M2VkN2ZmOTVhMCJ9

## Problem Statement

This dashboard is designed for Toy Stores looking to uncover which of their stores are driving the most business and which product is most popular. This dashboard will help managers make better decisions about sales strategies thus shaping future sales approaches.

# STEPS:
### 1. Steps for Modelling the Datasets

- Step 1 : Open power query editor Load data folder into Power BI Desktop, datasets are csv files.
- Step 2 : Rename query to "Source" and uncheck "Enable Load" for this query.
- Step 3 : Now "Reference" the Source query and connect the "Dates" Table, filter 1st column(Date) for (>=2022) as dates in sales table are (>=2022).
- Step 4 : Similarly "Reference" the "Source" query and connect the "Inventory, Products, Sales, Stores" Tables.
- Step 5 : Goto Sales

    Merge Queries -- Select Products from drawdown -- select "Product_ID" in both tables (Join kind: Left Outer) -- OK
        
    From Products just add columns "Product_Cost", "Product_Price" to Sales Table.  
- Step 6 : Create 3 new columns: 

        "Total_Cost"    (Units * Product_Cost)
        "Total_Revenue" (Units * Product_Price)
        "Total_Profit"  (Total_Revenue - Total_Cost)
        
    Remove column "Product_Cost" & "Product_Price" from Sales Table.
- Step 7 : Close & Apply Power Query Editor.
- Step 8 : Goto Model View, Power BI was intelligent enough to create relationship between tables based on common items. However Dates and Sales table are not connected, so create relationship by dragging date from Sales table and drop it on Dates Table.


### 2. Steps for Building Our DAX Measure Table (_Measures_01).

- Step 1 : In this project we have created separate tables for DAX Measure.
- Step 2 : Home -- Enter Data -- Name (_Measures_01) -- Load.
- Step 3 : Create New Measures
```
        Cost = SUM(Sales[Total Cost])
        Profit = SUM(Sales[Total Profit])
        Revenue = SUM(Sales[Total Revenue])
        Units = SUM(Sales[Units])
```
- Step 4 : Hide column01


### 3. Steps for Building a Sales Performance Page
           
- Step 1 : Add Cards for displaying YTD Revenue & Previous YTD Revenue:
            
    (a) Home -- Enter Data -- Name (_Measures_02) -- Load.

    (b) Create New measures
           
           YTD Revenue = TOTALYTD([Revenue], Dates[Date])
           Previous YTD Revenue = CALCULATE([YTD Revenue], SAMEPERIODLASTYEAR(Dates[Date]))
           
    (c) Hide column01
    
    (d) Add YTD Revenue & Previous YTD Revenue to cards and format accordingly

- Step 2 : In Dates[Date] table we have date upto Dec 2023, but in Sales[Date] data is upto Sep 2023 so we need equate date in both table, to achieve this: 

    (a) Add new column in Dates table
        
        Sales Data Cap = IF(Dates[Date]<=MAX(Sales[Date]),1,0)
    (b) Drag "Sales Data Cap" and drop to filter on all pages "Sales Data Cap is 1" lock and hide it.
- Step 3 : Add Cards for displaying YOY Difference

    (a) Create New measure in (_Measures_02), Convert it to % 

        YOY Difference = DIVIDE([YTD Revenue],[Previous YTD Revenue])-1
    
    (b) Add YOY Difference to card and format accordingly

- Step 4 : Add Line Chart1:

    (a) Add Line Chart: [X-Month_Short], [Y- Running Total Revenue], [Legend-Year]

- Step 5 : Add Line Chart2:

    (a) Create New measure in (_Measures_02)
        
        Running Total Revenue = CALCULATE([Revenue],DATESBETWEEN(Dates[Date],STARTOFYEAR(Dates[Date]),MAX(Dates[Date])))

    (b) Add Line Chart: [X-Month_Short], [Y- Running Total Revenue], [Legend-Year]
  
    (c) Create New measure in (_Measures_02)

        Goal = 8500000
  
    (d) Add constant line of goal in line chart: 
    
    Add further analysis to visual -- constant line -- fx -- field value -- Goal --OK

- Step 4 : Add Card for displaying Goal

    (a) Add Goal to card and format as currency
- Step 5 : Add Card for displaying Progress towards Goal

    (a) Create New measure in (_Measures_02)
        
        Progress Towards Goal = DIVIDE([YTD Revenue],[Goal])

    (b) Add Progress Towards Goal to card and format as percentage
- Step 6 : Add 3 Stacked Bar Charts:

    (a) Bar Chart (YTD Revenue vs Product Category):   [X-Axis: YTD Revenue], [Y-Axis: Product Category]

    (b) Bar Chart (YOY Difference vs Product Category): [X-Axis: YOY Difference], [Y-Axis: Product Category]
  
    (c) Bar Chart (YOY Difference vs Store Location): [X-Axis: YOY Difference], [Y-Axis: Store Location]

At Last format complete page for colours and gradients accordingly
### 4. Steps for Building a Product Performance Page
           
- Step 1 : Add Stacked Bar Chart (YTD Revenue by Product)
            
    (a) Bar Chart :   [X-Axis: YTD Revenue], [Y-Axis: Product Name]

- Step 2 : Add Stacked Bar Chart (YTD Profit by Product) 

    (a) Create New measure in (_Measures_02)
        
        YTD Profit = TOTALYTD([Profit],Dates[Date])
    (b)  Bar Chart :   [X-Axis: YTD Profit], [Y-Axis: Product Name]

    (c) Visual -- Bars -- fx -- Gradient -- YTD Profit -- OK

- Step 3 : Add Stacked Bar Chart (YTD Unit Sold by Product) 

    (a) Create New measure in (_Measures_02)
        
        YTD Units Sold = TOTALYTD([Units],Dates[Date])
    (b)  Bar Chart :   [X-Axis: YTD Units Sold], [Y-Axis: Product Name]

    (c) Visual -- Bars -- fx -- Gradient -- YTD Profit -- OK
    
- Step 4 : Add Table:

    (a) Add Product_Name, YTD Revenue, Previous YTD Revenue, YOY Difference in Columns

    (b) After adding YOY Difference, we observe that some Products were introduced this year, there is no previous year data for them so to avoid that we create new updated YOY Differnce measure in (_Measure_02)
        
        YOY Difference 2 = IF([Previous YTD Revenue]=0, BLANK(),[YOY Difference])
    (c) Remove YOY Differnce & add YOY Differnce 2 in Column of table.

    (d) Rename all column and format them.

- Step 5 : Add Cluster Column Chart (YOY Difference in Revenue by Product):

    (a) Column Chart : [X- Product Name], [Y- YOY Difference 2]

    (b) Format Bar colour condition according to YOY Difference 2
  
    (c) Add Zoom Slider, Format your visual -- Zoom Slider -- On
  
### 5. Steps for Building a Store Performance Page
           
- Step 1 : Add Stacked Bar Chart (YTD Revenue by Store)
            
    (a) Bar Chart :   [X-Axis: YTD Revenue], [Y-Axis: Store Name]

- Step 2 : Add Stacked Bar Chart (YOY Difference in Revenue by Store) 

    (a)  Bar Chart :   [X-Axis: YOY Difference 2], [Y-Axis: Store Name]

    (b) Visual -- Bars -- fx -- Gradient -- YOY Difference 2 -- OK

- Step 3 : Add Map (Revenue and YOY Difference by Store Location)

    (a) [Location: Store City], [BubbleSize: YTD Revenue]
        
    (b)  Visual -- Bars -- fx -- Gradient -- YOY Difference 2 -- OK

    (c) Add New Worksheet to use as Tooltip (Store Tooltip) (Hide it)

    Visualization -- Format Page -- Page Information -- Allow use as ToolTip

    Format your Report page -- Canvas Setting -- Height (500) -- Width (910)

    (d) Add cards:-- Store Name, Store Location, YOY Difference, Previous YTD Revenue, YTD Revenue

    (e) Add Bar Chart:-- Top 10 Products by YTD Revenue,  Filter -- Filter Type (Top N)-- Top 10 -- Value (YTD Revenue), Format Bar Color Condition by YTD Revenue

    (f) Add Bar Chart:-- Bottom 5 Products by YOY Revenue Growth, Filter -- Filter Type (Top N)-- Bottom 5 -- Value (YOY Difference 2), Format Bar Color Condition by YOY Difference 2

    (g) Goto Map in Store Page -- General -- Tooltips -- Page (Store Tooltip)

- Step 4 : Add Matrix (Stores low on Top Revenue Product Inventory)

    (a) Home -- Enter Data -- Name (_Measures_03) -- Load.

    (b) Create New measures

        Inventory in Hand = SUM(Inventory[Stock_On_Hand])
     
    (c) Hide column01
    
    (d) Matrix Table : [Rows:Store Name, Product Name], [Value: Inventory in Hand]
  
- Step 5 : To Know many days of Inventory is left Add Matrix (Stores low on Top Revenue Product Inventory)
  
    (a) To calculate max date (i.e. 30 Sept 2023), create measure

        Max Sales Date = CALCULATE(MAX(Sales[Date]),ALLSELECTED(Sales))

    (b) To calculate avg daily units sold in last 30 days, Create measure and add to the matrix table

        Avg Daiy Units Sold = CALCULATE(AVERAGEX(ALLSELECTED(Dates[Date]),[Units]),DATESBETWEEN(Dates[Date],[Max Sales Date]-30, [Max Sales Date]))
     
    (c) To calculate days of inventory left, Create measure and add to the matrix table

        Days of Inventory Left = DIVIDE([Inventory in Hand],[Avg Daiy Units Sold])

    
- Step 6 : To find Inventory left for Top 5 product ranked on Revenue
     
    (a) To rank product based on revenue, create measure

        Rank YTD Revenue = RANKX(ALLSELECTED(Products[Product_Name]),[YTD Revenue])
 
    (b) To Filter Rank Matrix, Add this measure to filter tab of table and select “Filter Rank Matrix is 1”

        Filter Rank Matrix = IF([Rank YTD Revenue]<=5,1,0)
    (c) Format the Matrix Table
    
    (d) To Filter out blanks in Days of Inventory edit its measure
    
    Filter "Days of Inventory Left is less than 1 and is not Blank"

        Days of Inventory Left = IF([Avg Daiy Units Sold]=0,BLANK()),
                                 DIVIDE([Inventory in Hand],[Avg Daiy Units Sold])
 
    (e) Format your Visual -- Row Header -- Option -- Off (Stepped layout) 
 
### 6. Steps for Building a Goal Projection Page        
           
- Step 1 : Add Filter
            
    (a) Apply filter in this pages by dragging (Year) to filter on this pages “Year is 2023”


- Step 2 : Add Table so that we can create Forecast Chart based on New Table Values (later delete it):

    (a)  Drag  and drop Dates[Date], Uncheck Date Hierarchy

    (b) Home -- Enter Data -- Name (_Measures_04) -- Load.
    Create New Measure

        YTD Day Count = TOTALYTD(DISTINCTCOUNT(Sales[Date]),Dates[Date])
    
    (c) Calculate Remaining Days

        Remaining Days = TOTALYTD(DISTINCTCOUNT(Dates[Date]),Dates[Date])-[YTD Day Count]

    (d) Drag and drop YTD Day Count, Remaining Days, Revenue, Running Total Revenue

    (e) Calculate Average Daily Revenue

        Avg Daily Revenue = DIVIDE([Running Total Revenue],[YTD Day Count])
    
    (f) Now we require last average revenue (i.e. 25,502 @ 30/09/2023)
        
        Avg Daily Revenue (Today) = CALCULATE([Avg Daily Revenue],ALLSELECTED(Dates[Date]))

    (g) Remaining Year Forecast:

        Remaining Days Forecast = [Remaining Days]*[Avg Daily Revenue (Today)]
    
    (h) Total Running Total + Forecast (i.e. From 30 sept onwards what should be projected revenue)

        Running Total + Forecast = [Running Total Revenue]+[Remaining Days Forecast]


- Step 3 : Add Line Chart (2023 Projected Revenue based on YTD AVG Daily Revenue (vs Goal))

    (a) Add Line Chart: [X- Date], [Y- Running Total + Forecast], Add constant line of goal in line chart
    
    (b) Home -- Enter Data -- Name (_Measures_05) -- Load.

    (c) To add [Actual Revenue] + [Forecasted Revenue] (i.e. Running Total + Forecast) Y axis in Line Chart

        Projection - YTD Revenue = IF([Revenue]<>BLANK(),BLANK(),[Running Total + Forecast])

    Now add [Running Total Revenue](Measure_02) and [Projection - YTD Revenue](Measure_05) in Y axis of Line Chart.
     
    (c) To remove extra line of [Running Total Revenue](Measure_02) from chart 

        Running Total Revenue Stop = IF([Revenue]=BLANK(),BLANK(),[Running Total Revenue])

    Now interchange [Running Total Revenue Stop] with [Running Total Revenue] in Y axis
    
    (d) To add marker(dot) at Today and Max Forecast Date

    For Today

        IF(SELECTEDVALUE(Dates[Date])=CALCULATE(MAX(Sales[Date]),ALLSELECTED(Dates[Date])),[Running Total Revenue],BLANK())

    Now add [Current YTD Revenue](Measure_05) in Y axis of Line Chart.
  
    For Max Forecast Date there are two measures:

    1st to calculate max date (31/12/2023)

        Max Forecast Date = MAXX(ALLSELECTED(Dates[Date]),CALCULATE(MAX(Dates[Date])))
    2nd for Forecasted Amount

        End of Year Projection = IF(SELECTEDVALUE(Dates[Date])=[Max Forecast Date],[Running Total + Forecast],BLANK())

    Now add [End of Year Projection](Measure_05) in Y axis of Line Chart.
- Step 4 : Format Forecast Chart (2023 Projected Revenue based on YTD AVG Daily Revenue (vs Goal))
  
    (a) Rename Legends in Y axis 

- Step 5 : Adding Other Items on Dashboard
     
    (a) Add New Parameter (Slider)

    Modelling -- min(6000000) -- max(12000000) – ok

    Move Parameter value (Interactive Goal) to Measure_05

    Select Chart – add further analysis to visual – constant line -- fx – Interactive Goal

    (b) Add New Parameter2 (Slider)
    
    Modelling -- min(6000000) -- max(12000000) – ok

    Move Parameter value (Interactive Goal) to Measure_05

    Edit Avg Daily Revenue (Today)[Measure_04] to:

        Avg Daily Revenue (Interacted) = 'Parameter 2'[Parameter Value]

    Format Both Slider Sets

    (c) Add 3 Card Charts: Progress Towards Goal, Interactive Goal, Running Total + Forecast

    (d) To add to Progress Towards Projected Goal Card

        Progress Towards Projected Goal = DIVIDE([Running Total + Forecast],[Interactive Goal])
    To add conditional colour to card
        
        Card Color Progress = IF([Progress Towards Projected Goal]>=1, "#14967C", "#F6AB09")
    Change effect background fx – field value – Card Color Progress

 
 - Step 6 : The report was then published to Power BI Service.
 

# Snapshot of Model View

![Model View Snapshot](https://github.com/asmshkhaws/TOY_SALES_POWER_BI/assets/119579424/21f2a51c-5b4e-465d-bec1-c93b1aba08b8)
 

# Insights

A four pages report was created on Power BI Desktop & it was then published to Power BI Service.

Following inferences can be drawn from the dashboard;

### [1] Revenue Insights

![Toy_Sales_page-0001](https://github.com/asmshkhaws/TOY_SALES_POWER_BI/assets/119579424/a35337e0-5337-4a82-81e9-4dfe1e70bccd)
   Year To Date Revenue = $6,962,074

   Previous Year To Date Revenue = $5,320,116

   Year on Year Difference = 30.9%

   Goal = $8,500,000

   Progress Towards Goal = 81.9%

           
### [2] Goal Projection Insights
![Toy_Sales_page-0002](https://github.com/asmshkhaws/TOY_SALES_POWER_BI/assets/119579424/e9c517b3-0c83-4893-b817-9b865f927350)

Helps us in achieving Goal by providing Avg Daily Sales Revenue Set value.

  
  ### [3] Products Performance Insights 
  ![Toy_Sales_page-0003](https://github.com/asmshkhaws/TOY_SALES_POWER_BI/assets/119579424/19a135c3-bbb5-49e5-8c80-dec10261d66a)

Lego Bricks contributes to largest Revenue $1,057,576.

This year Magic Sand 

Colorbuds generates highest profit of all products i.e. $233,248.

Barrel O'Slime is best selling product, 61232 units of it were sold.

 ### [4] Store Performance Insights
 ![Store Performance](https://github.com/asmshkhaws/TOY_SALES_POWER_BI/assets/119579424/e8e7f3d8-0a5c-4477-a417-ba88a59135fe)
 
Ciudad de Mexico 3 generates highest revenue of all Stores i.e. $260,928

Culiacan 1 has phenomenal Revenue growth of 103.5% compare to last year
 
