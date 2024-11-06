**Question 1:** 

It looks like there may be products missing from the products table. Is it possible to learn what the names and SKU numbers are from the all_sessions table? Make sure to only include those that are missing. 

**SQL Queries:**

~~~sql
select
	distinct(alls."v2ProductName"),
	alls."productSKU"
from all_sessions alls
left join products p
on p."SKU" = alls."productSKU"
where p."SKU" is null
~~~

**Question 2:**

It looks like some products may have multiple skus for the same name, write a query that counts how many exist for both the all_sessions and products tables

**SQL Queries:**

~~~sql
--product names that share multiple skus in the all_sessions table
select
	trim(leading ' ' from "v2ProductName") as prod_name,
	count("productSKU") as no_of_skus
from all_sessions
group by prod_name
order by prod_name
~~~
~~~sql
--product names that share multiple skus in the products table
select
	trim(leading ' ' from name) as prod_name,
	count("SKU") as no_of_skus
from products
group by prod_name
order by prod_name
~~~

**Question 3:**

If the products table is compared to the all_sessions table, it seems that there's a V2 name on the all_sessions table that exists for some SKUS that show on both tables. Additionally, some products are entirely new on the all_sessions table, that don't exist on the products table. Write a query that shows the SKU column, Product Name column, and a new third column that informs if the product name displayed is the original name, the updated V2 name, or if it's a new product that only exists on the all_sessions table.

**SQL Queries:**
~~~sql
select 
	CASE
	when all_sessions."productSKU" is not null and all_sessions."v2ProductName" is not null then all_sessions."productSKU"
	else products."SKU"
	end as current_sku,
	CASE
	when all_sessions."productSKU" is not null and all_sessions."v2ProductName" is not null then all_sessions."v2ProductName"
	else products."name"
	end as current_product_name,
	CASE
	when products."SKU" is null and products."name" is null then 'NEW'
	when all_sessions."productSKU" is not null and all_sessions."v2ProductName" is not null then 'Updated to V2'
	else 'Original'
	end as version_type
from all_sessions
full outer join products
on products."SKU" = all_sessions."productSKU"
group by all_sessions."productSKU", products."name", products."SKU", all_sessions."v2ProductName"
order by current_sku
~~~
