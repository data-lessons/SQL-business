---
title: "SQL Aggregation"
teaching: 50
exercises: 10
questions:
- "How can I summarize my data by aggregating, filtering, or ordering query results?"
- "How to save query result?"
objectives:
- "Apply aggregation to group records in SQL."
- "Filter and order results of a query based on aggregate functions."
- "Save a query to make a new table."
- "Apply filters to find missing values in SQL."
keypoints:
- "Use the `GROUP BY` keyword to aggregate data."
- "Functions like `MIN`, `MAX`, `AVERAGE`, `SUM`, `COUNT`, etc. operate on aggregated data."
- "Aliases can help shorten long queries. To write clear and readible queries, use the `AS` keyword when creating aliases."
- "Use the `HAVING` keyword to filter on aggregate properties."
- "Use a `VIEW` to access the result of a query as though it was a new table."
---

## COUNT and GROUP BY

Aggregation allows us to combine results by grouping records based on value, also it is useful for
calculating combined values in groups.

Let’s go to the **invoice_info** table and find out how many invoices there are.
Using the wildcard * simply counts the number of records (rows):

    SELECT COUNT(*)
    FROM invoice_info;

We can also find out how many total bottles sold:

    SELECT COUNT(*), SUM(Bottles_Sold)
    FROM invoice_info;

There are many other aggregate functions included in SQL, for example:
`MAX`, `MIN`, and `AVG`.

Now, let's see how many invoices were for each Store. We do this
using a `GROUP BY` clause

    SELECT Store_id, COUNT(*)
    FROM invoice_info
    GROUP BY Store_id;

`GROUP BY` tells SQL what field or fields we want to use to aggregate the data.
If we want to group by multiple fields, we give `GROUP BY` a comma separated list.

> ## Challenge
>
> What is the most expensive soda in each category? 
> Use the item_info table, write queries that return:
> Item_Description, Category, max Bottle_Retail_Price 
>
{: .challenge}

## Ordering Aggregated Results

We can order the results of our aggregation by a specific column, including
the aggregated column.  Let’s count the number of individuals of each
species captured, ordered by the count:

    SELECT Store_id, COUNT(*)
    FROM invoice_info
    GROUP BY Store_id
    ORDER BY COUNT(*);

## The `HAVING` keyword

In the previous episode, we have seen the keyword `WHERE`, allowing to
filter the results according to some criteria. SQL offers a mechanism to
filter the results based on **aggregate functions**, through the `HAVING` keyword.

If you use `AS` in your query to rename a column, `HAVING` you can use this
information to make the query more readable. For example, in the above
query, we can call the `COUNT(*)` by another name, like
`num_invoices`. 

For example, we can request to only return information
about stores that has more than 1000 invoices. 

    SELECT Store_id, COUNT(*) AS num_invoice
    FROM invoice_info
    GROUP BY Store_id
    HAVING num_invoices > 1000
    ORDER BY num_invoices;

The `HAVING` keyword works exactly like the `WHERE` keyword, but uses
aggregate functions instead of database fields to filter.

Note that in both queries, `HAVING` comes *after* `GROUP BY`. One way to
think about this is: the data are retrieved (`SELECT`), which can be filtered
(`WHERE`), then joined in groups (`GROUP BY`); finally, we can filter again based on some
of these groups (`HAVING`).

> ## Challenge
>
> What sodas were sold more than 100000 bottles from 2012 to 2017?  
> In another word, write a query that returns item_id in invoice_info table
> Where the total bottles sold is more than 100000
{: .challenge}

## Saving Queries for Future Use

It is not uncommon to repeat the same operation more than once, for example
for monitoring or reporting purposes. SQL comes with a very powerful mechanism
to do this by creating views. Views are a form of query that is saved in the database,
and can be used to look at, filter, and even update information. One way to
think of views is as a table, that can read, aggregate, and filter information
from several places before showing it to you.

Creating a view from a query requires to add `CREATE VIEW viewname AS`
before the query itself. For example, imagine that my project only covers
the data gathered during the May of 2017.  That
query would look like:

    SELECT * FROM invoice_info
    WHERE Date BETWEEN "2017-05-01" AND "2017-05-31"
    ORDER BY Date;

But we don't want to have to type that every time we want to ask a
question about that particular subset of data. Hence, we can benefit from a view:

    CREATE VIEW May_2017 AS
    SELECT * FROM invoice_info
    WHERE Date BETWEEN "2017-05-01" AND "2017-05-31"
    ORDER BY Date;

Now if you execute the query with `pd.read_sql()`, what happened? It does not work! <br>
WHY? 
![alt text](../img/q.png){:height="100px"} <br>
It is saving something to database, instead of returing the query result. So nothing can be store to the dataframe.<br>
Instead, what you can do is to create a cursor object, and execute the statement:  
```
q2 = '''CREATE VIEW May_2017 AS
    SELECT * FROM invoice_info
    WHERE Date BETWEEN "2017-05-01" AND "2017-05-31"
    ORDER BY Date;'''
c = conn.cursor()
c.execute(q2)
```  

Now, you have successfully created the view. `May_2017` view is almost like a table in the database. You can do something like this:  
```
SELECT * FROM May_2017;
```

## What About NULL?
Let's try the following two queries:  

```
SELECT COUNT(*) FROM item_info;
```
```
SELECT COUNT(Category) FROM item_info;
```

Why did they return different result? <br>
You probablly noticed from previous exercises that there are one category called "None". Yes, there are few sodas that does not have a category. 

When we count the weight field specifically, SQL ignores the records with data
missing in that field.  So here is one example where NULLs can be tricky:
`COUNT(*)` and `COUNT(field)` can return different values.

To find the soda that does not have a category, you can use `IS NULL` statement. Note that you cannot do `== NULL`.  
```
SELECT * FROM item_info 
WHERE Category IS NULL;
```

Another case is when we use a "negative" query.  Let's count all the
soda with category "Blueberry Soda":

    SELECT COUNT(*) 
    FROM item_info 
    WHERE Category == "Blueberry Soda";

Now let's count all the soda with categories other than "C0001":

    SELECT COUNT(*) 
    FROM item_info 
    WHERE Category != "Blueberry Soda";

But if we compare those two numbers with the total:

    SELECT COUNT(*)
    FROM item_info;

We'll see that they don't add up to the total! That's because SQL
doesn't automatically include NULL values in a negative conditional
statement.  So if we are quering "not x", then SQL divides our data
into three categories: 'x', 'not NULL, not x' and NULL; then,
returns the 'not NULL, not x' group. Sometimes this may be what we want -
but sometimes we may want the missing values included as well! In that
case, we'd need to change our query to:

    SELECT COUNT(*)
    FROM item_info 
    WHERE Category != "Blueberry Soda" OR Category IS NULL;
