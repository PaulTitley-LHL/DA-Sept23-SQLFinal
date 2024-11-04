Answer the following questions and provide the SQL queries used to find the answer.

**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

**SQL Queries:**

select country, sum("totalTransactionRevenue"/1000000)
FROM all_sessions
where "totalTransactionRevenue" > 0
group by country
order by sum desc

select city, sum("totalTransactionRevenue"/1000000)
FROM all_sessions
where "totalTransactionRevenue" > 0
group by city
order by sum desc

**Answer:**
The United States is the highest revenue country, and technically "not available in demo dataset"  is the highest revenue "city" that we have recorded, but the "not available in demo dataset" value is unclear whether it represents multiple cities, or just one. Therefore going on the data only, we must say that San Francisco is the highest performing city.


**Question 2: What is the average number of products ordered from visitors in each city and country?**

**SQL Queries:**

--country query
select country, avg("productQuantity") as "Average Number of Products", count("productQuantity") as "Orders with Quantities Listed"
FROM all_sessions
where "productQuantity" is not null
group by country

--city query
select country, city, avg("productQuantity") as "Average Number of Products", count("productQuantity") as "Orders with Quantities Listed"
FROM all_sessions
where "productQuantity" is not null
group by city, country

--city query with only named cities
select city, avg("productQuantity") as "Average Number of Products", count("productQuantity") as "Orders with Quantities Listed"
FROM all_sessions
where "productQuantity" is not null and city != 'not available in demo dataset' and city != '(not set)'
group by city

**Answer:**

While the countries are clear as to what the average quantity of products ordered are for each, cities are a little less clear, as many countries don't list the city the order is from for those rows which have a value in the "productQuantity" column. Since many cities in several countries use the "not available in demo dataset" value, it is important to include the country column within the cities query, to establish as best as possible what the city average is, since otherwise they'd all get lumped together by the GROUP function.

Alternatively, you could use the third query to exclude cities that aren't officially named to remove the country column requirement, but this data would be arguably less accurate for reporting average quantities.

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

**SQL Queries:**

--what country orders the most diverse amount of categories?
with cat_by_country as(
select country, "v2ProductCategory", count("v2ProductCategory")
FROM all_sessions
where country != '(not set)' and "v2ProductCategory" like 'Home/%'
group by country, "v2ProductCategory"
order by count desc, country
)
select country, count("v2ProductCategory") as "Number of Different Categories"
from cat_by_country
group by country
order by 2 desc

--what city orders the most diverse amount of categories?
with cat_by_city as(
select country, city, "v2ProductCategory", count("v2ProductCategory")
FROM all_sessions
where country != '(not set)' and "v2ProductCategory" like 'Home/%' and city != 'not available in demo dataset' and city != '(not set)'
group by country, city, "v2ProductCategory"
order by count desc
)
select country, city, count("v2ProductCategory") as "Number of Different Categories"
from cat_by_city
group by country, city
order by 3 desc

--what is the most popular product category for each country?
with popular_cat as(
select country, "v2ProductCategory", count("v2ProductCategory"), rank() over (partition by country order by count("v2ProductCategory") desc)
FROM all_sessions
where country != '(not set)' and "v2ProductCategory" like 'Home/%'
group by country, "v2ProductCategory"
order by rank, count desc
)
select *
from popular_cat
where rank = 1

--what is the most popular category for each city?
with popular_cat as(
select country, city, "v2ProductCategory", count("v2ProductCategory"), rank() over (partition by city order by count("v2ProductCategory") desc)
FROM all_sessions
where country != '(not set)' and "v2ProductCategory" like 'Home/%' and city != 'not available in demo dataset' and city != '(not set)'
group by country, city, "v2ProductCategory"
order by rank, count desc
)
select *
from popular_cat
where rank = 1

--what is the most popular category?
select "v2ProductCategory", count("v2ProductCategory")
from all_sessions
where country != '(not set)' and "v2ProductCategory" like 'Home/%' and city != 'not available in demo dataset' and city != '(not set)'
group by "v2ProductCategory"
order by count desc

**Answer:**

Based on the above queries, we can discern that the United States orders the most diverse amount of categories, specifically with Mountain View being the leading city. One could discern that since 4 of the top 5 leading cities are based in California, the large amount of Google branded products being offered, and Mountain View leading in the ranking of cities, that this database is likely based on the transactions of Google branded products. With their headquarters being based in Mountain View, it makes sense that the cities in California tend to be the popular shoppers within this database.




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

**SQL Queries:**

--what is the most popular product based on country?
with top_product_country as(
select country, "productSKU", "v2ProductName", count("productSKU"), rank() over (partition by country order by count("productSKU") desc)
from all_sessions
where country != '(not set)'
group by country, city, "productSKU", "v2ProductName"
order by count desc
)
select *
from top_product_country
where rank = 1

--what is the most popular product based on city?
with top_product_city as (
select country, city, "productSKU", "v2ProductName", count("productSKU"), rank() over (partition by city order by count("productSKU") desc)
FROM all_sessions
where country != '(not set)' and city != 'not available in demo dataset' and city != '(not set)'
group by country, city, "productSKU", "v2ProductName"
order by count desc
)
select *
from top_product_city
where rank = 1

**Answer:**

While the most popular product by country appears to be the "Google Men's 100% Cotton Short Sleeve Hero Tee White" appearing over 80 times within the United States alone, when organized by city, Mountain View seems to take their security seriously the "Nest® Cam Indoor Security Camera - USA" having the highest amount of orders for that specific product. Just like the categories from question 3, the biggest pattern to take away from these queries is that it's predominantly Google branded products leading the list of the top selling products.


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

**SQL Queries:**

--Displays the countries that aren't listed with a numerical value in the "totalTransactionRevenue" column, but have SKUs/Categories listed within transactions
with cat_by_country as(
select country, "v2ProductCategory", count("v2ProductCategory")
FROM all_sessions
where country != '(not set)' and "v2ProductCategory" like 'Home/%'
group by country, "v2ProductCategory"
order by count desc, country
),
country_revenue as (
select country, sum("totalTransactionRevenue"/1000000)
FROM all_sessions
where country != '(not set)'
group by country
order by sum
)
select cat_by_country.country
from cat_by_country
full outer join country_revenue
on country_revenue.country = cat_by_country.country
where country_revenue.sum is null
group by cat_by_country.country

**Answer:**

The query listed is to show that we have an incomplete picture of the total transaction revenues for each country/city, and so it's difficult to say what the full impact would be on a country/city basis. With so many countries missing the values in the "totalTransactionRevenue" column, The best we can do here would make an educated guess. We've established the company is very likely Google, which we know is a company with global reach. We can establish that they do business with all the countries from the query, but we can't discern to what extent. Based on the previous queries from question 3, the best educated guess I could make is that even if we had all of the "totalTransactionRevenue" rows filled, it's still likely that the highest impact econmoically speaking would come from the United States.







