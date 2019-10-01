---
layout: lesson
root: .
title: Solutions to SQL exercises
---

##### WARNING

This file may not be always up to date with regards to the exact exercises
instructions and the naming of the columns and tables in the database. Check
before you run the workshop!


**Basic Queries**
**EXERCISE**

We have the cost and price of each soda, write a query that returns the profit margin of each soda, give it alias as ‘Margin’ and round it to 2 decimal places. 

**SOLUTION**

	SELECT *, ROUND((Bottle_Retail_Price - Bottle_Cost)/Bottle_Cost,2) AS Margin 
	FROM item_info;

**EXERCISE**

Produce a table listing the data for all soda that cost less than $2 and contains the word “lime” in the name, telling us the name, cost, and volume (in litters).

**SOLUTION**

	SELECT Item_Description, Bottle_Cost, Bottle_Volume_ml/1000 AS Bottle_Volume_l 
    FROM item_info
    WHERE Bottle_Cost < 2
    AND Item_Description LIKE "%lime%";  

**EXERCISE**

Write a query that returns Item_Description, Bottle_Cost, volume and retail price of the soda, sorted firstly with retail price in descending order, then with volume in ascending order

**SOLUTION**

	SELECT Item_Description, Bottle_Cost, Bottle_Volume_ml, Bottle_Retail_Price 
    FROM item_info
    ORDER BY Bottle_Retail_Price DESC, Bottle_Volume_ml; 


**EXERCISE**

Let’s try to combine what we’ve learned so far in a single query. Using the item_info table write a query to display the three data fields, Item_Description, Bottle_Volume_ml and Retail Price for 6 packs (give it an alias Six_Pack_Price), for all sodas that are usually sold in 6 packs, ordered firstly by Six_Pack_Price, then alphabetically by the Item_Description.  

**SOLUTION**

	SELECT Item_Description, Bottle_Volume_ml, Bottle_Retail_Price * 6 AS Six_Pack_Price
    FROM item_info
    WHERE Pack = 6
    ORDER BY Six_Pack_Price, Item_Description;

**Aggregation**
**EXERCISE**

What is the most expensive soda in each category? Use the item_info table, write queries that return: Item_Description, Category, max Bottle_Retail_Price

**SOLUTION**

	SELECT Item_Description, Category, MAX(Bottle_Retail_Price)
    FROM item_info
    GROUP BY Category;  


**EXERCISE**

What sodas were sold more than 100000 bottles in the whole database?  

**SOLUTION**

	SELECT Item_id, SUM(Bottles_Sold) AS ct
    FROM invoice_info
    GROUP BY Item_id
    HAVING ct > 100000;

**Join**
**EXERCISE**

Write a query that returns the Store_id, Store_Name, County_Name and City_Name of every stores

**SOLUTION**

	SELECT Store_id, Store_Name, County_Name, City_Name
    FROM Store_info
    JOIN County USING (County_id);

**EXERCISE**

Think about this query, what does each SELECT statement do? How was the full outer join achieved?

**SOLUTION**

	First select statement selects "in A but not in B" and "both in A and in B" 
		This is different from SELECT * FROM A; because it identifies what's in A but not in B  
	Second select statement selects "in B but not in A"  

**EXERCISE**

Find the item name that was never sold

**SOLUTION**

	SELECT Item_Description 
    FROM item_info as ite
    LEFT JOIN invoice_info as inv
    ON ite.item_id = inv.item_id
    WHERE invoice_id IS NULL;

**EXERCISE**

Which 5 monthes have the largest sales in dollar from 2013 to 2017? 

**SOLUTION**

	SELECT strftime('%Y-%m', Date) as month, sum(Bottles_sold*Bottle_Cost) AS Dollar_Sale
    FROM invoice_info as inv
    INNER JOIN item_info as ite ON inv.item_id = ite.item_id 
    WHERE month BETWEEN '2013-01-01' AND '2016-12-31'
    GROUP BY month
    ORDER BY Dollar_Sale DESC;

**EXERCISE**

How many bottles of each energy drink were sold in 2015?

**SOLUTION**

	SELECT item_info.Item_Description, IFNULL(sub.Totle_Bottles, 0) AS Totle_Bottles
    FROM item_info
    LEFT JOIN 
        (SELECT item_id, SUM(Bottles_Sold) as Totle_Bottles
        FROM invoice_info
        WHERE Date BETWEEN "2015-01-01" AND "2015-12-31"
        GROUP BY Item_id) as sub
    Using (item_id)
    WHERE Category = "Energy Drink"
    ORDER BY Totle_Bottles DESC;

**EXERCISE**

Find the soda that generated most profit after 2015 in Davenport (City_Name = "DAVENPORT"). Sort by total profit. <br>
> A few soda's "Bottle_Retail_Price" is empty, replace those with Bottle_Cost * 1.8, shich is average profit margin.

**SOLUTION**

	SELECT Item_id, Item_Description, SUM((IFNULL(Bottle_Retail_Price, Bottle_Cost*1.8) - Bottle_Cost) * Bottles_Sold) AS Profit
    FROM invoice_info
    INNER JOIN item_info USING (item_id)
    INNER JOIN store_info USING (Store_id)
    INNER JOIN county USING (County_id)
    WHERE Date > "2015-01-01" AND City_Name = "DAVENPORT"
    GROUP BY item_id
    ORDER BY Profit DESC;
