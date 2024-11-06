**What issues will you address by cleaning the data?**


**Queries:
Below, provide the SQL queries you used to clean your data.**

~~~sql
--where filter that removes country and city values that don't apply
where country != '(not set)' and city != 'not available in demo dataset' and city != '(not set)'
~~~
~~~sql
--filter that edits the leading space character from products name
trim(leading ' ' from products."name")
~~~
~~~sql
--filter that converts the price value down to normal increments.
("totalTransactionRevenue" / 1000000) as trans_total
("productPrice" / 1000000) as normal_prod_price
~~~
~~~sql
--filtering product categories that don't follow the regular naming convention
where "v2ProductCategory" not like 'Home/%'
~~~
