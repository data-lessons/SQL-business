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
- "Use a `VIEW` to store the result of a query as though it was a new table."
---

## COUNT and GROUP BY

Aggregation allows us to combine results by grouping records based on value, it is useful for calculating combined values in groups.  

Let’s go to the **invoice_info** table and find out how many invoices there are.
Using the wildcard * simply counts the number of records (rows):

    SELECT COUNT(*)
    FROM invoice_info;

We can also find out how many total bottles sold:

    SELECT COUNT(*), SUM(Bottles_Sold)
    FROM invoice_info;

There are many other aggregate functions included in SQL, for example:
`MAX`, `MIN`, and `AVG`.

Now, let's see how many invoices were there for each Store. We do this
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
> Item_Description, Category_id, max Bottle_Retail_Price 
> of the most expensive soda in each category.  
> 
>> ## Solution
>>
>> ```
>> SELECT Item_Description, Category_id, MAX(Bottle_Retail_Price)
>> FROM item_info
>> GROUP BY Category_id;
>> 
>> ```
> {: .solution}
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

You can make the query more readable by using `AS` in your query to rename a column. 
For example, in the above query, we can call the `COUNT(*)` by another name, like
`num_invoices`. 

In the previous episode, we have seen the keyword `WHERE`, allowing us to
filter the results according to some criteria. SQL offers a mechanism to
filter the results based on **aggregate functions**, through the `HAVING` keyword.

For example, we can request to only return information
about stores that has more than 1000 invoices. 

    SELECT Store_id, COUNT(*) AS num_invoices
    FROM invoice_info
    WHERE Bottles_sold > 1
    GROUP BY Store_id
    HAVING num_invoices > 1000
    ORDER BY num_invoices;

The `HAVING` keyword works exactly like the `WHERE` keyword, but uses
aggregated columns instead of database fields to filter.

Note that `HAVING` comes *after* `GROUP BY`. One way to
think about this is: the data are retrieved (`SELECT`), which can be filtered
(`WHERE`), then combined in groups (`GROUP BY`); finally, we can filter again based on some
of these groups (`HAVING`).

> ## Challenge
>
> What sodas were sold more than 100000 bottles in the whole database?  
> In another word, write a query that returns item_id and total bottles sold in invoice_info table
> Where the total bottles sold is more than 100000
> 
>> ## Solution
>>
>> ```
>> SELECT Item_id, SUM(Bottles_Sold) AS ct
>> FROM invoice_info
>> GROUP BY Item_id
>> HAVING ct > 100000;
>> 
>> ```
> {: .solution}
{: .challenge}

## What About NULL?

Real-world data is never complete --- there are always holes. Databases represent these holes using a special value called null. null is not zero, False, or the empty string; it is a one-of-a-kind value that means "nothing here". Dealing with null requires a few special tricks and some careful thinking.

To start, let's have a look at the store_info table. Stores with Store_id 5226 and 5249 have no County_id --- or rather, its County_id is null:

To find the Stores that does not have a County, note that you cannot do `== NULL`. In stead, you can use `IS NULL` statement. 

```
SELECT * FROM store_info 
WHERE COunty_id IS NULL;
```

Another case is when we use a "negative" query.  Let's count all the Stores in County_id = 82:

    SELECT COUNT(*) 
    FROM store_info 
    WHERE County_id = 82;

Now let's count all the Stores in County_id != 82:

    SELECT COUNT(*) 
    FROM store_info 
    WHERE County_id != 82;

But if we compare those two numbers with the total:

    SELECT COUNT(*)
    FROM store_info;

We'll see that they don't add up to the total! That's because SQL
doesn't automatically include NULL values in a negative conditional
statement.  So if we are querying "not x", then SQL divides our data
into three categories: 'x', 'not NULL, not x' and NULL; then,
returns the 'not NULL, not x' group. Sometimes this may be what we want -
but sometimes we may want the missing values included as well! In that
case, we'd need to change our query to:

> ## Challenge
>
> How do you count all the Stores not in County_id 82 to include the missing values?
> 
>> ## Solution
>>
>> ```
>> SELECT COUNT(*) 
>> FROM store_info 
>> WHERE County_id != 82 OR County_id IS NULL;
>> 
>> ```
> {: .solution}
{: .challenge}

