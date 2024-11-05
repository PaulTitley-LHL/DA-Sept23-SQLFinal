**What issues will you address by cleaning the data?**


**Queries:
Below, provide the SQL queries you used to clean your data.**

--where filter that removes country and city values that don't apply
where country != '(not set)' and city != 'not available in demo dataset' and city != '(not set)'

--filter that edits the leading space character from products name
trim(leading ' ' from products."name")
