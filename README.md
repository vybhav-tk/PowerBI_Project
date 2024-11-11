## Maven Market Power BI Dashboard

![image](https://github.com/user-attachments/assets/4ed53398-843d-48d1-b67e-91773eeb85f4)


### Introduction
This repository contains a Power BI dashboard that provides insightful analysis of Maven Market's sales, customers, products, and store performance. The dashboard is designed to help stakeholders make data-driven decisions by visualizing key performance indicators (KPIs), trends, and patterns within the company's data.

A detailed walkthrough of the project is presented here, including data preparation, modeling, DAX calculations, and report building.

### Data Sources
The project uses the following data files:

- MavenMarket_Customers.csv
- MavenMarket_Products.csv
- MavenMarket_Stores.csv
- MavenMarket_Regions.csv
- MavenMarket_Calendar.csv
- MavenMarket_Returns.csv
- MavenMarket_Transactions_1997.csv
- MavenMarket_Transactions_1998.csv

These files contain information on customers, products, stores, regions, calendar dates, returns, and transactions for the years 1997 and 1998.

### Project Overview

The project is divided into four main parts:

1. Connecting and Shaping the Data: Importing data into Power BI and performing data transformations.
2. Creating the Data Model: Establishing relationships between tables to form a coherent data model.
3. Adding DAX Measures: Creating calculated columns and measures using DAX for analysis.
4. Building the Report: Designing the dashboard with interactive visuals and KPIs.

### Part 1: Connecting and Shaping the Data
In this section, we import the data and perform necessary transformations to prepare it for analysis.

#### Importing and Transforming Data:

- Customers Table:
  - Import MavenMarket_Customers.csv and name the table "Customers".
  - Promote headers and confirm data types (e.g., customer_id as Whole Number).
  - Add Calculated Columns:
    - full_name: Concatenate first_name and last_name.
    - birth_year: Extract the year from birthdate.
    - has_children: Conditional column; "N" if total_children is 0, else "Y".

- Products Table:
  - Import MavenMarket_Products.csv and name the table "Products".
  - Confirm data types and use statistics tools to find distinct brands and product names.
  - Add Calculated Columns:
    - discount_price: Calculate 90% of the product_retail_price and round to two decimal places.
  - Data Cleaning:
    - Replace null values with zeros in recyclable and low-fat columns.

- Stores and Regions Tables:
  - Import MavenMarket_Stores.csv as "Stores" and MavenMarket_Regions.csv as "Regions".
  - Add full_address and area_code columns in "Stores" for enhanced geolocation analysis.

- Calendar Table:
  - Import MavenMarket_Calendar.csv as "Calendar".
  - Use date tools to add columns like Start of Week, Name of Day, Quarter of Year, etc.

- Returns and Transactions Data:
  - Import MavenMarket_Returns.csv as "Return_Data".
  - Combine transaction files from a folder into "Transaction_Data".
  - Confirm data spans from 1/1/1997 to 12/30/1998.

- Disable Auto Refresh:
  - Exclude the two data tables from "Include in Report Refresh" to optimize performance.


### Part 2: Creating the Data Model
In this part, we establish relationships between tables to form a robust data model.

- Organize Tables:
  - Arrange lookup tables (Customers, Products, Stores, Regions, Calendar) above data tables (Transaction_Data, Return_Data).

- Establish Relationships:
  - Connect "Transaction_Data" to "Customers", "Products", and "Stores" using primary and foreign keys.
  - Create relationships between "Transaction_Data" and "Calendar" using transaction_date (active) and stock_date (inactive).
  - Connect "Return_Data" to "Products", "Calendar", and "Stores".
  - Link "Stores" to "Regions" to form a snowflake schema.

- Verify Relationship Properties:
  - Ensure all relationships have one-to-many cardinality.
  - Confirm filters are one-way, flowing from lookup tables to data tables.
  - Avoid direct relationships between data tables to maintain model integrity.

- Hide Unnecessary Columns:
  - Hide foreign keys in data tables and region_id in the "Stores" table from the report view to declutter the model.

- Data Formatting:
  - Update date formats and set currency formats for price-related fields.
  - Categorize geographical fields for accurate mapping.

We utilize the following concepts here:

- Data Modeling: Structuring data relationships to reflect real-world entities and interactions.
- Cardinality and Filter Direction: Understanding one-to-many relationships and how filters propagate through the model.
- Snowflake Schema: An extension of the star schema with additional layers of lookup tables.

### Part 3: Adding DAX Measures
This section focuses on enhancing the model with calculated columns and measures using DAX (Data Analysis Expressions).

- Calculated Columns:
  - Calendar Table:
    - Weekend: "Y" if the day is Saturday or Sunday, else "N".
    - End of Month: Last date of the current month.
  - Customers Table:
    - Current Age: Calculate age using birthdate and TODAY().
    - Priority: "High" if the customer owns a home and has a Golden membership, else "Standard".
    - Short_Country: First three uppercase letters of customer_country.
    - House Number: Extract characters before the first space in customer_address.
  - Products Table:
    - Price_Tier: "High" if product_retail_price > $3, "Mid" if > $1, else "Low".
  - Stores Table:
    - Years_Since_Remodel: Years since last remodel date using TODAY().

- Measures:
  - Quantity and Transactions:
    - Quantity Sold: Sum of quantities from "Transaction_Data".
    - Quantity Returned: Sum from "Return_Data".
    - Total Transactions and Total Returns: Count of rows in respective tables.
    - Return Rate: Ratio of quantity returned to quantity sold.
  - Time-Based Calculations:
    - Weekend Transactions: Transactions occurring on weekends.
    - % Weekend Transactions: Percentage of weekend transactions.
    - YTD Revenue: Year-to-date total revenue.
    - 60-Day Revenue: Running total over 60 days.
    - Last Month Metrics: Transactions, Revenue, Profit, and Returns from the previous month.
    - Revenue Target: 5% increase over the previous month's revenue.
  - Financial Metrics:
    - Total Revenue: Sum of transaction quantities multiplied by retail price.
    - Total Cost: Sum of transaction quantities multiplied by product cost.
    - Total Profit: Difference between total revenue and total cost.
    - Profit Margin: Total profit divided by total revenue.
  - Other Metrics:
    - Unique Products: Count of distinct product names.
  - Spot Checks:
    - Validate measures by comparing calculated results with expected values.

We utilize the following concepts here:
- DAX Calculated Columns vs. Measures: Calculated Columns are computed row by row during data refresh; useful for category assignments. Measures are calculated on the fly based on filter context; ideal for aggregations and KPIs.
- Filter Context: The subset of data considered in a calculation, influenced by slicers, rows, and columns in visuals.
- Time Intelligence Functions: Functions like TOTALYTD and DATEADD that simplify time-based calculations.

### Part 4: Building the Report
The final part involves creating an interactive dashboard with visuals to represent the data insights.

- Matrix Visual:
  - Display Total Transactions, Total Profit, Profit Margin, and Return Rate by product_brand.
  - Apply conditional formatting:
    - Data bars for Total Transactions.
    - Color scales for Profit Margin (white to green) and Return Rate (white to red).
  - Apply a Top N filter to show the top 30 brands and sort by Total Transactions.
- KPI Cards:
  - Create cards for Total Transactions, Total Profit, and Total Returns, each with a trend axis and target goals based on last month's metrics.
  - Customize titles and formatting; for returns, set color coding where a lower value is better.
- Map Visual:
  - Plot Total Transactions by store_city.
  - Add a slicer for store_country with a "Select All" option for flexibility.
  - Change slicer orientation to horizontal for better aesthetics.
- Treemap Visual:
  - Display Total Transactions by store_country, with store_state and store_city enabling drill-down functionality.
- Column Chart:
  - Show Total Revenue by week for 1998.
  - Update the title to "Weekly Revenue Trending".
- Gauge Chart:
  - Illustrate Total Revenue against the Revenue Target for the latest month.
  - Update the title to "Revenue vs. Target" and adjust data labels.
- Interactivity:
  - Adjust visual interactions to prevent the treemap from filtering when interacting with the matrix.
- Bookmarks and Notes:
  - Create bookmarks highlighting key insights (e.g., "Portland hits 1,000 sales in December").
  - Add a "Notes" page with text boxes describing these insights.
  - Insert buttons linking to the bookmarks for easy navigation.

The Maven Market Power BI Dashboard provides a comprehensive view of the company's performance across various dimensions. By connecting and shaping the data, creating a robust data model, adding insightful DAX measures, and building an interactive report, we've transformed raw data into actionable insights.
This project demonstrates technical aspects in Power BI and also emphasizes the importance of data-driven storytelling in business analytics.
