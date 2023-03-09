## Mexico Toy Sales

The purpose of this project was to analyze patterns and trends set forth by this fictional dataset provided by Maven Analytic's Data Playground. Additionally, I also answered some of the suggested analysis questions provided by Maven, which you will see below. 

!!!!GIVE SUMMARY OF SQL COMMANDS USED!!!!

----------

### Which products drive the most profit? 

Due to the nature of the way the provided tables, where we were given sale price and product cost, I knew in order to understand which products were driving the most profit, I would have to create a new column in the products table that calculated profit. 

``` SQL
SELECT *, Product_Price - Product_Cost as Product_Profit
FROM [Mexico_Toys].[dbo].[products]
ORDER BY Product_Profit DESC

ALTER TABLE [Mexico_Toys].[dbo].[products]
ADD Product_Profit as (Product_Price - Product_Cost)
```
Then I wasnted to limit the results to the top 5 most profitable toys, and foudn that most profitable toys included a mini basketball hoop, a plush pony, and an etch-a-sketch. 

``` SQL
SELECT TOP (5) *
FROM [Mexico_Toys].[dbo].[products]
order by product_profit desc 
```
Following, I took a slight derivative and attempted to find the most profitable toy categories instead of the specific toy products and found the total profit of the category of toys. The toy category had the most profit, followed by sports & outdoors, then arts & crafts. 

``` SQL
SELECT TOP (5) Product_Category, SUM(Product_Profit) as Total_Category_Profit
FROM [Mexico_Toys].[dbo].[products]
GROUP BY Product_Category
order by Total_Category_Profit desc
```

Just to continue this analysis, I changed it slightly to find the categories with the most profit on average instead of total and found that electronics had the most, then sports & outdoors again, and toys in third. 

``` SQL 
SELECT TOP (5) Product_Category, AVG(Product_Profit) as AVG_Category_Profit
FROM [Mexico_Toys].[dbo].[products]
GROUP BY Product_Category
order by AVG_Category_Profit desc
```
----------
### Which are the most popular toys and categories at each location? 

First in order to start assessing this problem, tables will have to be joined together. I decdided to create a temporary table with the joining of multiple tables due to needing to use this table throughout the analysis. Starting off, I wanted to make sure that the sales table did not have any null values in the Store_ID column so that there wouldn't be any hiccups in the joining of the tables, which there were none, which was exactly what I wanted.  

``` SQL
select *
FROM [Mexico_Toys].[dbo].[sales]
where Store_ID is null
```
Then, I started making the table. I used the `DROP TABLE IF EXISTS` function while running this statement to ensure that when I was making changed that it would allow me to keep changing the statement without hitting hurdles. 

``` SQL
DROP TABLE IF EXISTS #store_sale_prod
CREATE TABLE #store_sale_prod
(
    Store_ID INT,
    Store_Name nvarchar(255),
    Store_City nvarchar(255), 
    Store_Location nvarchar(255), 
    Sale_ID INT,
    Date DATETIME,
    Units INT,
    Product_ID INT,
    Product_Name nvarchar(255),
    Product_Category nvarchar(255), 
    Product_Profit MONEY
)

INSERT INTO #store_sale_prod
SELECT 
stor.Store_ID, stor.Store_Name, stor.Store_City, stor.Store_Location, 
sale.Sale_ID, sale.Date, sale.Units,
prod.Product_ID, prod.Product_Name, prod.Product_Category, prod.Product_Profit
    FROM [Mexico_Toys].[dbo].[stores] stor
    JOIN [Mexico_Toys].[dbo].[sales] sale
        ON stor.Store_ID = sale.Store_ID
    JOIN [Mexico_Toys].[dbo].[products] prod
        ON sale.Product_ID = prod.Product_ID
```

Then, after creating this beautiful chaotic mess, I checked to make sure it worked. 

``` SQL
select *
from #store_sale_prod
```




