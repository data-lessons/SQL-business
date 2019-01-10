---
title: "Introduction to SQL"
teaching: 20
exercises: 5
questions:
- "What is a relational database and why should I use it?"
- "What is SQL?"
objectives:
- "Limitations of excel"
- "What is SQL"
- "What is relational database and Database Management Systems (DBMS)"
- "Describe why relational databases are useful"
keypoints:
- "SQL allows us to select and group subsets of data, do math and other calculations, and combine data."
- "A relational database is made up of tables which are related to each other by shared keys."
- "Different database management systems (DBMS) use slightly different vocabulary, but they are all based on the same ideas."
---

## Setup

_Note: this should have been done by participants before the start of the workshop._

See [Setup](../setup/) for
instructions on how to download the data, and also how to install jupyter notebook  

## What is SQL?

SQL stands for Structured Query Language. SQL allows us to interact with relational databases through queries. 
These queries can allow you to perform a number of actions such as: insert, update and delete information in a database. 

## Questions

> ## Think about this situation
>
> Imagine if you are a owner of a convenience store, and you are trying to record your soda purchases. <br>
> In each invoice, it contains the following information: <br>
> Invoice id, Date, Category, Soda name, Volume, Cost, Retail Price, Vendor, Vendor phone number, Number of bottle purchased <br>
> How would you store the data?  
{: .challenge}

## Traditional File Approach 
If you store all these invoice information in one Excel file, 
What problem could raise from this approach?  
* Data redundancy:
<br>
Imagine if you consistently purchased some Big Dog Cola from LCDM Beverage vendor every day for 5 days, 
Notice these columns: Category, Soda_name, Volume, Cost, Retail_Price, Vendor, Vendor number
With traditional file approach, you have to record the exact same information in these columns 5 times.
<br>
![excel1](../img/00_1.png){:width="700px"}
<br>

* Data inconsistency:
<br>
Imagine if the vendor changed its phone number. Then multiple changes has to be made. 
There are only 5 rows here so it might be easy to change everything. However, if the data size gets large, mistakes are likely to occur.
<br>
![excel2](../img/00_2.png){:width="700px"}
<br>

If you thought about storing these information in few different Excel files, <b>great idea! You are on the right track </b><br>
However, if you want information from all files at the same time, how do you combine them? If each file contains thousands of rows, Ah... <br>

* Slow with large sets of data:
<br>
The largest table that Excel can handle is 1,048,576 * 16,384. Excel can be very slow when working with large number of data. Moreover, in the real word, you can easily get more than 1 million rows of data...   
![excel3](../img/tuxue.png){:height="100px" width="100px"}

## Goals

To sum up, these are frequent used data operations: 

* select subsets of the data (rows and columns)
* group subsets of data
* do math and other calculations
* combine data across spreadsheets

In addition, we don't want to do this manually! Instead of searching 
for the right pieces of data ourselves, or clicking between spreadsheets, 
or manually sorting columns, we want to make the computer do the work.  

In particular, we want to use a tool where it's easy to repeat our analysis 
in case our data changes. We also want to do all this searching without 
actually modifying our source data.  

Putting our data into a <b>relational database</b> and using SQL will help us achieve these goals.  

# Databases

> ## Definition: *Relational Database*
>
> A relational database stores data in *relations* made up of *records* with *fields*.
> The relations are usually represented as *tables*;
> each record is usually shown as a row, and the fields as columns.
> In most cases, each record will have a unique identifier, called a *key*,
> which is stored as one of its fields.
> Records may also contain keys that refer to records in other tables,
> which enables us to combine information from two or more sources. <br>
> ![db](../img/db.png){:height="200px"}
{: .callout}

## Why use relational databases

Using a relational database serves several purposes.

* It keeps your data separate from your analysis.
    * This means there's no risk of accidentally changing data when you analyze it.
    * If we get new data we can just rerun the query.
* It's fast, even for large amounts of data.
* It improves quality control of data entry (type constraints and use of forms in MS Access, Filemaker, Oracle Application Express etc.)
* The concepts of relational database querying are core to understanding how to do similar things using programming languages such as R or Python.
* Many big companies are using Relational Database to store financial data. As business students, we can learn how to pool useful data by ourselves. 

## Database Management Systems (DBMS)

There are a number of different database management systems for working with
relational data. We're going to use SQLite today, but basically everything we
teach you will apply to the other database systems as well (e.g. MySQL,
PostgreSQL, MS Access, MS SQL Server, Oracle Database and Filemaker Pro). The 
only things that will differ are the details of exactly how to import and 
export data and the data types.  

Look at the [popularity of database](https://db-engines.com/en/ranking) <br>
![alt text](../img/dbms.png){:height="170px"} <br>
Top 4 are all Relational Database Management Systems (RDBMS). More and more companies choose to use relational database. 

## Database Design

* Every row-column combination contains a single *atomic* value, i.e., not
   containing parts we might want to work with separately.
* One field per type of information
* No redundant information
    * Split into separate tables with one table per class of information
    * Needs an identifier in common between tables – shared column - to
       reconnect (known as a *foreign key*).
We will explain this later with our database.  

To summarize: 

* Relational databases store data in tables with fields (columns) and records
  (rows)
* Each row is uniquely identified by a Primary Key  
* Queries let us look up data or make calculations based on columns


