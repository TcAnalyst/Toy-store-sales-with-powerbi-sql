# Mexico Toy Sales-Maven Toy stores: A Descriptive analysis of maven toy stores across all store location

## Introduction
Mexico Toy Sales — Maven Toy Store. Sales & inventory data for a chain of toy stores in Mexico called Maven Toys, including information about products, stores, daily sales transactions, and current inventory levels at each location.

### Dataset overview

The dataset contains four tables: sales table, inventory table, stores table and the products table and sales data from January 2017 to September 2018. The sales table contains the sales data with 829,262 rows of unique transactions, the inventory table contains the stock-on-hand data. The stores table contains details of all 50 toy stores across 29 Mexico cities. The product table contains product ids, name, cost of products and selling price.

### Objective of the analysis
To provide insight about general sales and profit of the business based on location, seasonal trend pattern and product performance also providing recommendation for the business.

### Recommended Analysis

1. Which product categories drive the biggest profits? Is this the same across store locations?

2. Can you find any seasonal trends or patterns in the sales data?

3. How much money is tied up in inventory at the toy stores? How long will it last?

### TOOLS USED: MICROSOFT SQL AND POWER BI

### Data importation and cleaning

- imported data using the SQL server import and export window
- checked for duplicate rows to ensure data accuracy checked for null values to ensure data integrity.
- utilized the UPDATE, SET, REPLACE and TRIM commands to clean the Product price and product cost columns.
- made use of the ALTER and SET commands to change the data type of Product price and product cost columns.

## DATA EXPLORATION AND ANALYSIS
  ![Screenshot (121)](https://github.com/TcAnalyst/Toy-store-sales-with-powerbi-sql/assets/142181097/57c53c30-31db-43bb-84d7-07f33ddb3f95)
-sales report dashboard-

Analysis by products;

1. Which product categories drive the biggest profits?
-sql

with GeneralProfit as
(select  Product_Category, Product_Price-Product_Cost Categoryprofits, sales.units, 
sales.units*(Product_Price-Product_Cost) TotalProfit
from products
join sales
on products.Product_ID=sales.Product_ID)
select Product_Category, SUM(TotalProfit) Profits
from GeneralProfit
group by Product_Category
order by Profits desc;
-
