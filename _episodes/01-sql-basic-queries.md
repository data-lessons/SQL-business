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

## Writing my first query

Let's start by using the **item_info** table. It includes information about all kinds of soda. 

| Attributes          | Data Type      | Description                                                |
|---------------------|:---------------|------------------------------------------------------------|
| Item_id             | INTEGER        | Unique id for each item (soda)                             |
| Category_id         | VARCHAR(20)    | category id of the soda                                    |
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

    SELECT DISTINCT Category_id
    FROM item_info;

Well, we can only see the unique ids, and that does not make any sense right? 
Don't worry, you will learn how to return category names later.  

If we select more than one column, then the distinct pairs of values are
returned

    SELECT DISTINCT Category_id, Pack
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

    SELECT Item_Description, Bottle_Volume_ml, ROUND((Bottle_Retail_Price - Bottle_Cost)/Bottle_Cost,2) AS Margin
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
![alt text](../img/np.gif){:height="100px"} <br>

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
For example, suppose we want all the energy drink (category id = 'C0006') with volume larger than 500ml:

    ELECT * FROM item_info
    WHERE Bottle_Volume_ml > 500 AND Category_id = 'C0006';  

Note that the parentheses are not needed, but again, they help with
readability.  They also ensure that the computer combines `AND` and `OR`
in the way that we intend.

If we wanted to get data for 3 categories of soda, which have
category id of `C0006`, `C0001`, and `C0002`, we could combine the tests using OR:

    ELECT * FROM item_info
    WHERE Category_id = 'C0006' OR Category_id = 'C0001' OR Category_id = 'C0002';  

This looks messy, right? We can do the same thing by using 'IN': 

    SELECT * FROM item_info 
    WHERE Category_id IN ('C0006', 'C0001', 'C0002'); 

> ## Challenge
>
> - Produce a table listing the data for all individuals in Plot 1 
> that weighed more than 75 grams, telling us the date, species id code, and weight
> (in kg). 
{: .challenge}

## Building more complex queries

Now, lets combine the above queries to get data for the 3 _Dipodomys_ species from
the year 2000 on.  This time, let’s use IN as one way to make the query easier
to understand.  It is equivalent to saying `WHERE (species_id = 'DM') OR (species_id
= 'DO') OR (species_id = 'DS')`, but reads more neatly:

    SELECT *
    FROM surveys
    WHERE (year >= 2000) AND (species_id IN ('DM', 'DO', 'DS'));

We started with something simple, then added more clauses one by one, testing
their effects as we went along.  For complex queries, this is a good strategy,
to make sure you are getting what you want.  Sometimes it might help to take a
subset of the data that you can easily see in a temporary database to practice
your queries on before working on a larger or more complicated database.

When the queries become more complex, it can be useful to add comments. In SQL,
comments are started by `--`, and end at the end of the line. For example, a
commented version of the above query can be written as:

    -- Get post 2000 data on Dipodomys' species
    -- These are in the surveys table, and we are interested in all columns
    SELECT * FROM surveys
    -- Sampling year is in the column `year`, and we want to include 2000
    WHERE (year >= 2000)
    -- Dipodomys' species have the `species_id` DM, DO, and DS
    AND (species_id IN ('DM', 'DO', 'DS'));

Although SQL queries often read like plain English, it is *always* useful to add
comments; this is especially true of more complex queries.

## Sorting

We can also sort the results of our queries by using `ORDER BY`.
For simplicity, let’s go back to the **species** table and alphabetize it by taxa.

First, let's look at what's in the **species** table. It's a table of the species_id and the full genus, species and taxa information for each species_id. Having this in a separate table is nice, because we didn't need to include all
this information in our main **surveys** table.

    SELECT *
    FROM species;

Now let's order it by taxa.

    SELECT *
    FROM species
    ORDER BY taxa ASC;

The keyword `ASC` tells us to order it in Ascending order.
We could alternately use `DESC` to get descending order.

    SELECT *
    FROM species
    ORDER BY taxa DESC;

`ASC` is the default.

We can also sort on several fields at once.
To truly be alphabetical, we might want to order by genus then species.

    SELECT *
    FROM species
    ORDER BY genus ASC, species ASC;

> ## Challenge
>
> - Write a query that returns year, species_id, and weight in kg from
> the surveys table, sorted with the largest weights at the top.
{: .challenge}

## Order of execution

Another note for ordering. We don’t actually have to display a column to sort by
it.  For example, let’s say we want to order the birds by their species ID, but
we only want to see genus and species.

    SELECT genus, species
    FROM species
    WHERE taxa = 'Bird'
    ORDER BY species_id ASC;

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
> query.  Using the surveys table write a query to display the three date fields,
> `species_id`, and weight in kilograms (rounded to two decimal places), for
> individuals captured in 1999, ordered alphabetically by the `species_id`.
> - Write the query as a single line, then put each clause on its own line, and
> see how more legible the query becomes!
{: .challenge}

