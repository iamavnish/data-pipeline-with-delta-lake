# Data Pipeline with Delta Lake

## Overview 

This is Proof of Concept for creating a Data Pipeline with Delta Lake using Medallion architecture.

## Use Case

Create a dashboard to Analyze Orders History for an online clothing store that sells a wide range of fashion brands.
The dashboard will be used to inform purchasing behavior and ensure that the company has enough inventory to meet demand for upcoming holiday season.

## Architecture

![medallion architecture](https://github.com/user-attachments/assets/1d22fda3-2c4f-4c5a-8b1f-6963e5abb876)

## Tech Stack

- Databricks Platform (Community Edition)
- Python3

## Dataset

Orders and Inventory data for Online clothing store

## Low Level Design (LLD)

********************************* Bronze Layer ***********************************
1.) Upload JSON files for Orders data to DBFS: ORDERS_RAW_PART_001.json, ORDERS_RAW_PART_002.json, ORDERS_RAW_PART_003.json, ORDERS_RAW_PART_004.json
2.) Load the JSON files into a dataframe (orders_raw_df)
3.) Create delta tabe (orders_raw) from the dataframe (orders_raw_df)
4.) Upload JSON files for Inventory data to DBFS: INVENTORY.json
5.) Load the JSON file into a dataframe (inventory_df)
6.) Create delta tabe (inventory) from the dataframe (inventory_df)

********************************* Silver Layer ***********************************
7.) Read delta table (orders_raw) into a new dataframe (orders_silver_df)
8.) Update data type of column order_date in dataframe, orders_silver_df.
9.) Drop rows with null values in the dataframe.
10.) Add a calculated column, total_order (total_order = quantity * unit_price)
11.) Create a new delta table (orders_silver)

********************************* Gold Layer *************************************
12.) Compute below KPIs and create new delta tables for each of the KPIs.
12.1) Quantity sold by Country
12.2) Sales by Category
12.3) Top 5 Popular Brands

********************************* BI *********************************************
13.) Create visuals for each of the delta tables.
14.) Create a dashboard out of the above visuals.


********************************* Bronze Layer ***********************************
15.) Update data in orders_raw table using merge. Upload JSON file to DBFS: UPDATE_ORDERS_RAW.json
16.) Load the JSON file into a dataframe (update_orders_df)
17.) Create a seperate dataframe (delta_orders_raw) out of orders_raw table.
18.) Merge two tables: orders_raw (alias to delta_orders_raw) and UpdateOrders (alias to update_orders_df)

********************************* Gold Layer *************************************
19.) Compute below KPI and create new delta table for it.
19.1) Low Stock or Out of Stock items
20.) Group all orders from orders_silver table based on brand, color, product_name and size and add qty_sold (sum(quantity)).
21.) Join the result with inventory table using inner join on brand, prodyct_name, color and size.
22.) Add calculated column qty_left_stock as (stock - qty_sold)
23.) Filter out cancelled orders
24.) Keep only items with qty_left_stock < 20
25.) Sort the result by qty_left_stock in ascending order.

********************************* BI *********************************************
13.) Create visual for the above delta table.
14.) Add the visual to dashboard.


