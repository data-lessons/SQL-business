---
title: "Basic Queries"
teaching: 30
exercises: 5
questions:
- "How do I write a basic query in SQL?"
objectives:
- "Write and build queries."
- "Filter data given various criteria."
- "Sort the results of a query."
keypoints:
- "It is useful to apply conventions when writing SQL queries to aid readability."
- "Use logical connectors such as AND or OR to create more complex queries."
- "Calculations using mathematical symbols can also be performed on SQL queries."
- "Adding comments in SQL helps keep complex queries understandable."
---

## Select statements

Let's start by using the **item_info** table. It includes information about all kinds of soda. 

| Attributes          | Data Type      | Description                                                |
|---------------------|:---------------|------------------------------------------------------------|
| Item_id             | INTEGER        | Unique id for each item (soda)                             |
| Category            | VARCHAR(20)    | category of soda                                           |
| Item_Description    | TEXT           | Name of the item (soda)                                    |
| Pack                | INTEGER        | Number of bottles that the soda usually sells for          |
| Bottle_Volume_ml    | DOUBLE         | Volumn of the soda in ml                                   |
| Bottle_Cost         | DOUBLE         | Cost of one bottle                                         |
| Bottle_Retail_Price | DOUBLE         | Retile price for one bottle                                |

Let’s write an SQL query that selects only the Item_Description column from the
item_info table. 

    SELECT Item_Description 
    FROM item_info;

We have capitalized the words SELECT and FROM because they are SQL keywords.
SQL is case insensitive, but it helps for readability, and is good style.

If we want more information, we can just add a new column to the list of fields,
right after SELECT:

    SELECT Item_Description, Bottle_Volume_ml, Bottle_Cost  
    FROM item_info;

Or we can select all of the columns in a table using the wildcard *

    SELECT *
    FROM item_info;

### Limiting results

Sometimes you don't want to see all the results you just want to get a sense of
of what's being returned. In that case you can use the LIMIT command. In particular
you would want to do this if you were working with large databases.

    SELECT *
    FROM item_info
    LIMIT 10; 

### Unique values

If we want only the unique values so that we can quickly see what categories have
been sampled we use `DISTINCT` 

    SELECT DISTINCT Category
    FROM item_info;

Well, we can only see the unique ids, and that does not make any sense right? 
Don't worry, you will learn how to return category names later.  

If we select more than one column, then the distinct pairs of values are
returned

    SELECT DISTINCT Category, Pack
    FROM item_info;

### Calculated values

We can also do calculations with the values in a query.
For example, if we wanted to look at the volume of soda in liters, instead of milliliters  

    SELECT Item_Description, Bottle_Volume_ml/1000 AS Bottle_Volume_L
    FROM item_info;

Note that you can use "AS" to give an alias to a column name. 

When we run the query, the expression `Bottle_Volume_ml/1000` is evaluated for each
row and appended to that row, in a new column. Since 'Bottle_Volume_ml' is already a 'DOUBLE',
after it is divided by an `INTEGER`, the result will still be 'DOUBLE'. 
If 'Bottle_Volume_ml' is an 'INTEGER' and we want the result to still be 'DOUBLE', we need to divide it by 1000.0  

Expressions can use any fields, any arithmetic operators (`+`, `-`, `*`, and `/`) and a variety of built-in
functions. For example, we could round the values.

    SELECT Item_Description, ROUND(Bottle_Volume_ml/1000,1) AS Bottle_Volume_L
    FROM item_info;

> ## Challenge
>
> - We have the cost and price of each soda, write a query that returns the profit margin of each soda, 
    give it alias as 'Margin' and round it to 2 decimal places.  
{: .challenge}

## Filtering

Databases can also filter data – selecting only the data meeting certain
criteria.  For example, let’s say we only want data for an energy drink called
_Nozomi Power Injection_.  We need to add a `WHERE` clause to our query: <br>
Note that we can use `=` or `==` for equal, `!=` or `<>` for not equal.  

[//]: <> (![alt text](../img/np.gif){:height="100px"} <br>) 

    SELECT * 
    FROM item_info
    WHERE Item_Description = "Nozomi Power Injection";

We can do the same thing with numbers.
Here, we only want the soda with more than 500ml:

    SELECT * FROM item_info
    WHERE Bottle_Volume_ml > 500;

What if your favorite singer's name is 'Honoka' and you want to buy soda that contains 'Honoka' in the name? Use `LIKE` statement:

    SELECT * 
    FROM item_info
    WHERE Item_Description LIKE "%Honoka%";

You can use `%` as wild card to search. That means, all soda with name like "aaHonokaaaaa" or "00Honokaxxxx" will be returned. 
You can use `_` as wild card for one character. For example, if you replace "%Honoka%" with "Honoka_", only names such as "Honokaa", "Honokax" will be returned. 

If we used the `TEXT` data type for the year the `WHERE` clause should
be `Bottle_Volume_ml > '500'`. We can use more sophisticated conditions by combining tests
with `AND` and `OR`.  
For example, suppose we want all the energy drink (category id = 'Energy Drink') with volume larger than 500ml:

    SELECT * FROM item_info
    WHERE Bottle_Volume_ml > 500 AND Category = 'Energy Drink';  

Note that the parentheses are not needed, but again, they help with
readability.  They also ensure that the computer combines `AND` and `OR`
in the way that we intend.

If we wanted to get data for 3 categories of soda, which have
category id of `Energy Drink`, `Blueberry Soda`, and `Cherry Soda`, we could combine the tests using OR:

    ELECT * FROM item_info
    WHERE Category = 'Energy Drink' OR Category = 'Blueberry Soda' OR Category = 'Cherry Soda';  

This looks messy, right? We can do the same thing by using 'IN': 

    SELECT * FROM item_info 
    WHERE Category IN (`Energy Drink`, `Blueberry Soda`, `Cherry Soda`); 

> ## Challenge
>
> - Produce a table listing the data for all soda that cost less than $2 and
> contains the word "lime" in the name, telling us the name, cost, and volume
> (in litters). 
{: .challenge}

## Sorting

We can also sort the results of our queries by using `ORDER BY`.
For simplicity, let’s go back to the **item_info** table and alphabetize it by soda name.

Now let's order it by name (ascending by default).

    SELECT *
    FROM item_info
    ORDER BY Item_Description;

We could alternately use `DESC` to get descending order.

    SELECT *
    FROM item_info
    ORDER BY Item_Description DESC; 

We can also sort on several fields at once.
To truly be alphabetical, we might want to order by genus then species.

    SELECT *
    FROM item_info
    ORDER BY Item_Description, Bottle_Cost;

> ## Challenge
>
> - Write a query that returns Item_Description, Bottle_Cost, volume and retail price
> of the soda, sorted firstly with retail price in descending order, then with volume in ascending order.  
{: .challenge}

## Dealing with dates  
Firstly, we take a look at the `invoice_info` table  
```
SELECT * FROM invoice_info;
```
The date in sqlite3 can be stored as multiple string formats: 
```
    YYYY-MM-DD
    YYYY-MM-DD HH:MM
    YYYY-MM-DD HH:MM:SS
    YYYY-MM-DD HH:MM:SS.SSS
    YYYY-MM-DDTHH:MM
    YYYY-MM-DDTHH:MM:SS
    YYYY-MM-DDTHH:MM:SS.SSS
    HH:MM
    HH:MM:SS
    HH:MM:SS.SSS
    now
    DDDDDDDDDD 
```
In our database, the date were stored as "YYYY-MM-DD" format. We can extract the year/month/date with `strftime` function. For example: 
```
Select *, strftime('%Y', Date) AS YEAR
FROM invoice_info; 
```
For month and day, simply replace `%Y` with `%m` or `%d`. You can also do `%Y-%m` to get the year and month. 
You can get all invoice after 2017 by: 
```
SELECT * FROM invoice_info
WHERE Date >= "2017-01-01";
```
You can get a invoice from a range of time by:  
```
SELECT * FROM invoice_info
WHERE Date BETWEEN "2017-01-01" AND "2017-02-27"; 
```
Note that `BETWEEN` is inclusive, that is, invoices at 2017-01-01 and 2017-02-27 will be returned  
If you are interested at more cool things you can do with dates, heres the [Documentation](https://www.sqlite.org/lang_datefunc.html)

## Order of execution

Another note for ordering. We don’t actually have to display a column to sort by
it.  For example, let’s say we want the soda with Bottle_Retail_Price under $3 and sort by their id, but
we only want to see Item_Description and Bottle_Cost.

    SELECT Item_Description, Bottle_Cost
    FROM item_info
    WHERE Bottle_Retail_Price < 3
    ORDER BY item_id;

We can do this because sorting occurs earlier in the computational pipeline than
field selection.

The computer is basically doing this:

1. Filtering rows according to WHERE
2. Sorting results according to ORDER BY
3. Displaying requested columns or expressions.

Clauses are written in a fixed order: `SELECT`, `FROM`, `WHERE`, then `ORDER
BY`. It is possible to write a query as a single line, but for readability,
we recommend to put each clause on its own line.

> ## Challenge
>
> - Let's try to combine what we've learned so far in a single
> query.  Using the **item_info** table write a query to display the three data fields,
> `Item_Description`, `Bottle_Volume_ml` and Retail Price for 6 packs (give it an alias `Six_Pack_Price`), for
> all sodas that are usually sold in 6 packs, ordered firstly by `Six_Pack_Price`, then alphabetically by the `Item_Description`.
> - Write the query as a single line, then put each clause on its own line, and
> see how more legible the query becomes!  
{: .challenge}

