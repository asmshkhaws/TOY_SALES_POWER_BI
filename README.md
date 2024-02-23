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

    (a) Create New measure in (_Measures_02)

        YOY Difference = DIVIDE([YTD Revenue],[Previous YTD Revenue])-1
        Convert it to % 
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
  
    (c) Add Zoom Slider 

        Format your visual -- Zoom Slider -- On
  
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

    (a) Column Chart : [X- Product Name], [Y- YOY Difference 2]

    (b) Format Bar colour condition according to YOY Difference 2
  
    (c) Add Zoom Slider 

        Format your visual -- Zoom Slider -- On
  

    
    Age Group = 
        
        if(airline_passenger_satisfaction[Age]<=25, "0-25 (25 included)",
        
        if(airline_passenger_satisfaction[Age]<=50, "25-50 (50 included)",
        
        if(airline_passenger_satisfaction[Age]<=75, "50-75 (75 included)",
        
        "75-100 (100 included)")))
        
Snap of new calculated column ,

![Snap_1](https://user-images.githubusercontent.com/102996550/174089602-ab834a6b-62ce-4b62-8922-a1d241ec240e.jpg)

        
- Step 15 : New measure was created to find total count of customers.

Following DAX expression was written for the same,
        
        Count of Customers = COUNT(airline_passenger_satisfaction[ID])
        
A card visual was used to represent count of customers.

![Snap_Count](https://user-images.githubusercontent.com/102996550/174090154-424dc1a4-3ff7-41f8-9617-17a2fb205825.jpg)

        
 - Step 16 : New measure was created to find  % of customers,
 
 Following DAX expression was written to find % of customers,
 
         % Customers = (DIVIDE(airline_passenger_satisfaction[Count of Customers], 129880)*100)
 
 A card visual was used to represent this perecntage.
 
 Snap of % of customers who preferred business class
 
 ![Snap_Percentage](https://user-images.githubusercontent.com/102996550/174090653-da02feb4-4775-4a95-affb-a211ca985d07.jpg)

 
 - Step 17 : New measure was created to calculate total distance travelled by flights & a card visual was used to represent total distance.
 
 Following DAX expression was written to find total distance,
 
         Total Distance Travelled = SUM(airline_passenger_satisfaction[Flight Distance])
    
 A card visual was used to represent this total distance.
 
 
 ![Snap_3](https://user-images.githubusercontent.com/102996550/174091618-bf770d6c-34c6-44d4-9f5e-49583a6d5f68.jpg)
 
 - Step 18 : The report was then published to Power BI Service.
 
 
![Publish_Message](https://user-images.githubusercontent.com/102996550/174094520-3a845196-97e6-4d44-8760-34a64abc3e77.jpg)

# Snapshot of Dashboard (Power BI Service)

![dashboard_snapo](https://user-images.githubusercontent.com/102996550/174096257-11f1aae5-203d-44fc-bfca-25d37faf3237.jpg)

 
 # Report Snapshot (Power BI DESKTOP)

 
![Dashboard_upload](https://user-images.githubusercontent.com/102996550/174074051-4f08287a-0568-4fdf-8ac9-6762e0d8fa94.jpg)

# Insights

A single page report was created on Power BI Desktop & it was then published to Power BI Service.

Following inferences can be drawn from the dashboard;

### [1] Total Number of Customers = 129880

   Number of satisfied Customers (Male) = 28159 (21.68 %)

   Number of satisfied Customers (Female) = 28269 (21.76 %)

   Number of neutral/unsatisfied customers (Male) = 35822 (27.58 %)

   Number of neutral/unsatisfied customers (Female) = 37630 (28.97 %)


           thus, higher number of customers are neutral/unsatisfied.
           
### [2] Average Ratings

    a) Baggage Handling - 3.63/5
    b) Check-in Service - 3.31/5
    c) Cleanliness - 3.29/5
    d) Ease of online booking - 2.88/5
    e) Food & Drink - 3.21/5
    f) In-flight Entertainment - 3.36/5
    g) In-flight service - 3.64/5
    h) In-flight Wifi service - 2.81/5
    i) Leg room service - 3.37/5
    j) On-board service - 3.38/5
    k) Online boarding - 3.33/5
    l) Seat comfort - 3.44/5
    m) Departure & arrival convenience - 3.22/5
  
  while calculating average rating, null values have been ignored as they were not relevant for some customers. 
  
  These ratings will change if different visual filters will be applied.  
  
  ### [3] Average Delay 
  
      a) Average delay in arrival(minutes) - 15.09
      b) Average delay in departure(minutes) - 14.71
Average delay will change if different visual filters will be applied.

 ### [4] Some other insights
 
 ### Class
 
 1.1) 47.87 % customers travelled by Business class.
 
 1.2) 44.89 % customers travelled by Economy class.
 
 1.3) 7.25 % customers travelled by Economy plus class.
 
         thus, maximum customers travelled by Business class.
 
 ### Age Group
 
 2.1)  21.69 % customers belong to '0-25' age group.
 
 2.2)  52.44 % customers belong to '25-50' age group.
 
 2.3)  25.57 % customers belong to '50-75' age group.
 
 2.4)  0.31 % customers belong to '75-100' age group.
 
         thus, maximum customers belong to '25-50' age group.
         
### Customer Type

3.1) 18.31 % customers have customer type 'First time'.

3.2) 81.69 % customers have customer type 'returning'.
       
       thus, more customers have customer type 'returning'.

### Type of travel

4.1) 69.06 % customers have travel type 'Business'.

4.2) 30.94 % customers have travel type 'Personal'.

        thus, more customers have travel type 'Business'.
