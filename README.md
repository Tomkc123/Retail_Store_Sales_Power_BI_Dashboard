Retail Sales Peformance Power BI Dashboard

<img width="1684" height="1142" alt="image" src="https://github.com/user-attachments/assets/76cac5aa-1ce7-4401-ac81-055fb77663c3" />

The objective of this project was to transform a messy, raw retail dataset into a executive-ready dashboard. By transforming and cleaning the raw dataset CSV, the project bridges the gap between raw data and strategic business choices.


Data Transformation and cleaning (Power Query): 

1. Extracted over 12,000 raw retail transactional records from a CSV data source grabbed from Kaggle.

2. Change the data types into suitable formats.

3. To resolve incomplete column values (i.e. Price Per Unit) without losing valuable transactions by writing conditional M-Code as follows:
   
   e.g. Back-calculated missing unit pricing using total spent and quantities:
   
         Unit Price = 
         if [Price Per Unit] <> null then [Price Per Unit] 
         
         else if [Total Spent] <> null and [Quantity] <> null then [Total Spent] / [Quantity] 
         else null

          Revenue = 
          if [Total Spent] <> null then [Total Spent]
          
          else if [Quantity] <> null and [Price Per Unit] <> null then [Quantity] * [Price Per Unit] 
          
          else null
          
4. Drop unnecessary columns
   
5. Filtered out rows where Revenue column values are null, the final dataset contains 11,971 rows



Data Modeling and Star Schema:

Created two dimension tables (Products, Customers) and one fact table Sales Transaction. Also created a Dates table using DAX expression.
  
The table dynamically scans the minimum and maximum dates present in the transactional dataset using `CALENDAR(StartDate, EndDate)`, ensuring

zero missing dates in the continuous time series. Also established one-to-many relationships between these tables. 

Created one more table named _Measures to structure all the measures used in the dashboard.




Data Visualization:

  
Page 1: Sales Overview 

KPI cards: A multi-row card contains total revenue, total orders, total units sold, average transaction value, average units per transaction and YoY% growth.

To calculate YoY% growth, the following time-intelligence DAX measures were built:

1. Total Revenue = SUM('Sales Transaction'[Revenue])

2. Previous Year Revenue = 
   VAR CurrentYearSelected = MAX('Dates'[Year])
   
   RETURN
   
   CALCULATE(
       [Total Revenue],
       ALL('Dates'[Year]),
       'Dates'[Year] = CurrentYearSelected - 1
   )

3. YoY% growth = 
   DIVIDE(
   [Total Revenue] - [Previous Year Revenue],
   [Previous Year Revenue]
)
  
Sales Trend and Revenue Patterns:

In the bottom left side of line chart, the business maintained consistent performance from 2022 through 2024, with revenue hovering near the $0.5M mark annually and volume tracking closely alongside it. This indicates solid market stability and steady baseline demand. There is a dramatic drop-
off in both revenue and units sold in 2025.

On the right side of the line chart, it experiences a massive upward revenue trend starting on Thursday and reaching its absolute weekly peak
on **Friday** at over **$232K**. This suggests that consumers are aggressively buying retail products right before the weekend.
Moreover, following a strong Sunday, sales drop to the lowest point of the entire week on **Monday**.


Page 2: Product Performance & Key Drivers:

<img width="1682" height="1126" alt="image" src="https://github.com/user-attachments/assets/cc38ed91-4c88-4b80-bac8-6f66eea4bd6f" />


1. Total Units Sold and Total Revenue by Category:

   **High-Margin/High-Volume:** **Butchers** occupies the premium top-tier quadrant—driving elite volume and elite revenue simultaneously.
   **Patisserie** sits isolated in the bottom-left quadrant (around **7,900 units / $182,000 revenue**). It suffers from low customer volume and poor cash generation, meaning it requires operational restructuring or marketing intervention. 
   
   **High-Volume/Lower-Margin:** **Furniture** and **Food** sit far to the right (high volume) but lag lower on the vertical    
   revenue scale, meaning they take up significant warehouse handling costs without returning maximum top-line revenue margins.

2. Total Revenue by Category and Location:

   Across nearly all product lines, sales split almost perfectly down the middle (~50% In-store / ~50% Online).    
   For example, Computers and Electric Accessories** skews noticeably toward **Online channels (54%)**. And **Furniture** skews toward **In-  store channels (51%)**, confirming that customers still prefer to physically inspect and experience furniture pieces before committing to a 
purchase.

3.  Category Matrix Tables:

    The Matrix visual (bottom) is created with an inline row hierarchy structuring data from high-level Category down to granular Product Name. Clicking on any data marker (blue circle) within the *Total Units Sold and Total Revenue by Category* scatter plot instantly cross filter the matrix table below. For example, selecting the Butchers data node in the scatter plot automatically isolates the matrix rows to only display transactional metrics, order volumes for users to seamlessly transition from macro trends to micro SKU-level details with a single click.

5. What-if Revenue Prediction:

   Include a illistrative Text Box paired with a Single Value KPI Card (Bottom-Right near right side) with Dynamic Unit Price Increase % Slider (Top).
   
  **Business Insights:** By adjusting the operational slider to model a **5% unit price increase**, the dashboard dynamically calculates an
  aggressive **$77,604 revenue optimization runway**.This simulation forecasts a baseline revenue shift pushing totals from **$1,552,071 up to   a projected $1,629,675**. This enables leadership to run risk-free margin stress tests before implementing real-world price increases.


  
