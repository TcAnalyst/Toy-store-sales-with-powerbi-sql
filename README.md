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

### Analysis by products;

1. Which product categories drive the biggest profits?
```sql
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
```
In general, the toy product category records the highest profit with $1.08m which accounts for 26.89%, electronics category accounts for the second biggest profit, $1m(25%), while the sports & outdoor category records the lowest profit of $500k.

2. is this the same across store locations?

Across store locations they are not the same as the Electronics category accounts for the biggest profit in airport and commercial areas, while the Toy category accounts for the biggest profits in the downtown and residential areas.

```sql
WITH GeneralProfit AS (
    SELECT
        stores.Store_Location,
        products.Product_Category,
        SUM((products.Product_Price - products.Product_Cost) * sales.Units) AS TotalProfit
    FROM
        sales
    JOIN stores ON sales.Store_ID = stores.Store_ID
    JOIN products ON sales.Product_ID = products.Product_ID
    GROUP BY
        stores.Store_Location,
        products.Product_Category
),
RankedItems AS (
    SELECT
        Store_Location,
        Product_Category,
        TotalProfit,
        ROW_NUMBER() OVER (PARTITION BY Store_Location ORDER BY TotalProfit DESC) AS RN
    FROM
        GeneralProfit
)
SELECT
    Store_Location,
    Product_Category,
    TotalProfit
FROM
    RankedItems
WHERE
    RN = 1;
```
3. What are the top 5 products that drive the biggest profits?
```sql
WITH GeneralProfit AS (
    SELECT
        products.Product_Name,
        products.Product_Category,
        SUM(sales.Units * (products.Product_Price - products.Product_Cost)) AS TotalProfit
    FROM
        products
    JOIN sales ON products.Product_ID = sales.Product_ID
    GROUP BY
        products.Product_Name,
        products.Product_Category)
SELECT TOP 5
    Product_Name,
    Product_Category,
    TotalProfit
FROM
    GeneralProfit
ORDER BY
    TotalProfit DESC;
```
the colourbuds, Action figure, lego bricks, deck of cards and glass marbles are the top 5 products with the highest profits of 835k, 348k, 298k, 252k and 190k respectively.

4. What are the top 5 most sold products?
```sql
select TOP 5  Product_Name, Product_Category, sum(sales.units) TotalSaleCount
from products
join sales
on products.Product_ID=sales.Product_ID
group by Product_Name,Product_Category
order by TotalSaleCount desc;
```
in terms of units of total products sold, the colourbuds are the highest sold products accounting for 104k (23.5%) units sold, playdoh can although not in the top 5 highest profit products recorded 103k (23.2%) units sold. The barrel o’slime, deck of cards and magic sand make up the third, fourth and fifth most sold products with 91k, 84k and 61k units sold.

### Analysis by store and location;

1. Which store location drive the biggest profits?
```sql
with GeneralProfit as
(select   stores.Store_ID,stores.Store_Name, stores.Store_Location, products.Product_Category, sales.Units,
 products.Product_Price-products.Product_Cost Categoryprofits, 
 (products.Product_Price-products.Product_Cost)*sales.Units TotalProfit
from sales
join stores on sales.Store_ID = stores.Store_ID
join products on sales.Product_ID=products.Product_ID)
select store_location, SUM(TotalProfit) Profits
from GeneralProfit
group by store_location
order by Profits desc;
```
stores located at downtown areas recorded the highest profits with over $2m in profits while stores in airport areas drive the lowest profit, $378k.

3. Which stores drive the biggest profits ?
```sql
with GeneralProfit as(
 select   stores.Store_Name, stores.Store_City, stores.Store_Location, products.Product_Category, sales.Units,
  products.Product_Price-products.Product_Cost Categoryprofits, 
  (products.Product_Price-products.Product_Cost)*sales.Units TotalProfit
 from sales
  join stores on sales.Store_ID = stores.Store_ID
  join products on sales.Product_ID=products.Product_ID)
 select top 5 Store_Name, Store_City City , SUM(TotalProfit) Profits
 from GeneralProfit
  group by Store_Name,Store_City
  order by Profits desc;
```
with over 50 stores under the maven toy company, the cludad de mwxico 2, the guadalajara 3, the cludad de mexico 1, monterrey 2 and toluca stores recorded the highest profits of $170k, $121k, $111k, $107k and $105k respectively. the cuernavaca 1 and the la paz 1 stores recorded the lowest profit and second lowest profit, both driving $57k profits each.

### **Analysis by seasonal trend and pattern;**
Sales and profit peak between March and July. Sales made are between 109K and 112k during this period with the highest sales recorded in April (112k units of products sold). Profits made was between $387k and $406k in that same period with the highest profit recorded in March ($406k profits made). Lowest sale was recorded in October with 48k units sale while the lowest profit was also recorded in the same month with $179k.

Quarterly, sales peaked at the second quarter and lowest in the fourth quarter.

### Analysis by year;
Sales increase as the year progressed in 2017 but wasnt the case in 2018. march recorded one of the least sales in 2017 (39k unit sales) but was the highest selling month in 2018 (69k unit sales). In general sales were high towards the end of the year in 2017, but highly concentrated in between the year in 2018 (february to July).

### Analysis on the inventory;

1. How much money is tied up in inventory at the toy stores?
```sql
with MT as (select  Stock_On_Hand , products.Product_Cost , Stock_On_Hand * products.Product_Cost MoneyTiedUp
from inventory
left join products on products.Product_ID=inventory.Product_ID
where Stock_On_Hand>0)
select sum(MoneyTiedup)
from MT;
```
the highlighted query in the frame above returns the amount stored up in the inventory which is a little above $300k
-- How long will it last?
```sql
with TotalUnitSales as (select Date_, sum(units) units
from sales
group by Date_)
select avg(units) AverageSalePerDay
from TotalUnitSales;
```

-- total stock on hand
```sql
select sum(stock_on_hand)
from inventory;
```
the average sale per day across all stores is 1709 units sales while the number of products in the inventory is 29742 units of product.

Since 1709 units sales are made every day and there are 29742 units in the inventory, we can assume that the products can last for the next 15–17 days before it is finished.

### SUMMARY

1. The downtown area generates more revenue than other areas.
2. The toys category records the highest selling and profitable category.
3. Revenue reduces during the beginning of the year.
4. Electronics category accounts for the biggest profit in airport and commercial areas, while the Toy category accounts for the biggest profits in the downtown and residential areas.

### RECOMMENDATIONS

investment of more capital across all stores in downtown areas will attract massive profits for the business.
factors affecting sales during the January and February should be looked into.

thank you for your time!
