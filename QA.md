**What are your risk areas? Identify and describe them.**



**QA Process:**
**Describe your QA process and include the SQL queries used to execute it.**


--displays what visitor ID's repeat in all_sessions
with repeats as(
SELECT "fullVisitorId" as repeat_id, count("fullVisitorId") as no_of_repeats
FROM all_sessions
group by "fullVisitorId"
having count("fullVisitorId") >1
)
select all_sessions.*
from all_sessions
join repeats
on repeats.repeat_id = all_sessions."fullVisitorId"
order by all_sessions."fullVisitorId"


--displays what quantity of skus were sold in regards to all_sessions vs sales_by_sku vs sales_report
select 
all_sessions."productSKU" as all_sessions_sku, 
count(all_sessions."productSKU")as count1, 
sales_by_sku."productSKU" as sales_by_sku_sku, 
count(sales_by_sku."productSKU") as count2,
sales_report."productSKU" as sales_report_sku,
count(sales_report."productSKU") as count3,
case
when count(all_sessions."productSKU") = count(sales_by_sku."productSKU") and count(all_sessions."productSKU") = count(sales_report."productSKU") then 'SAME'
else 'DIFFERENT!'
end as number_compare
from all_sessions
join sales_by_sku
on sales_by_sku."productSKU" = all_sessions."productSKU"
join sales_report
on sales_report."productSKU" = all_sessions."productSKU"
group by all_sessions."productSKU", sales_by_sku."productSKU", sales_report."productSKU"


--confirms that the V2 name change doesn't occur until the all_sessions table, as the sales_report table still uses the v1 names
select 
	sales_report."productSKU" as sales_report_sku, 
	sales_report."name" as sales_report_name,
	products."SKU" as products_sku, 
	products."name" as products_name
from sales_report
join products
on products."SKU" = sales_report."productSKU"
where sales_report."name" != products."name"


--confirms that multiplying the price by quantity does not equal the transaction revenue in all_sessions table
-select 
("productPrice" / 1000000) as normal_price, 
"productSKU", 
"productQuantity", 
("totalTransactionRevenue" / 1000000) as trans_total,
(("productPrice" * "productQuantity") / 1000000) as math_price
from all_sessions
where"productQuantity" is not null and "productPrice" > 0 and "totalTransactionRevenue" is not null
order by "productSKU"


--confirms in the all_sesions table that while there is one instance that a "visitId" has a duplicate value where "totalTransactionRevenue" is not null, the value of "totalTransactionRevenue" is the same, meaning that one can assume everywhere else there would be duplicate "visitId" values, the "totalTransactionRevenue" column would have the same value too, were it filled out in this database.
select 
distinct("visitId"), 
count("visitId"),
"totalTransactionRevenue"
FROM all_sessions
where "totalTransactionRevenue" is not null
group by "visitId", "totalTransactionRevenue"
order by count desc

--*****I must assume that the value in "totalTransactionRevenue" is totaled with variables I don't have access to******

--displays the "v2ProductCategory" values and counts from all_sessions table that don't follow the standard 'Home/%' naming convention
select "v2ProductCategory", count("v2ProductCategory")
FROM all_sessions
where "v2ProductCategory" not like 'Home/%'
group by "v2ProductCategory"

--displays repeat SKUs within all_sessions tab that have alternating pricing or naming, making it unrelialible for total revenue assumptions.
with updated_price_list as(
select ("productPrice" / 1000000) as prod_price, "productSKU", "v2ProductName", COUNT("productSKU") OVER (PARTITION BY "productSKU") as repeats
from all_sessions
group by 1, 2, 3
order by "productSKU"
)
select *
from updated_price_list
where prod_price > 0 and repeats > 1
