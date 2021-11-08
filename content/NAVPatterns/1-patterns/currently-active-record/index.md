+++
title = "Currently Active Record"
weight = 450
+++
Authors: Henrik Langbak and Kim Ginnerup, Bording Data

## Abstract

Date controlled data is expensive to find in the database. This pattern describes how using a view with a sub-select and a linked table object will minimize the returned dataset.  
A side effect is reduced and simplified code, increased performance and a more scalable solution that is almost independent of the amount of records in the table.

## Description

There is no way in NAV to get a set of records from the database, which all have the newest starting date, that is less than or equal to today's date. Having an ending date on the record will help, but it introduces some other problems. In Dynamics NAV this is normally done by reading too many records, either at the SQL Server level or in the middle tier and throw away the ones you do not need. That is a waste of resources:

* The SQL Server is reading too many records

* There would be too much data sent over the network.  
(If the SQL Server and the NAV Service tier are on different machines.)

* The NAV Service Tier receives and throws away data.

### Ending Date Problem

Ending Date may introduce some problems of its own.

If your design requires to have one and only one active record per key in a dataset, then Ending Date introduces the possibility for overlapping or holes in the timeline.  
Ending Date creates a dependency between two records. Changing a Starting Date, requires you to update the previous record. Changing the Ending Date requires you to update the next record.  
If you add a record in between you will have to update both the before and the after record.

The pattern we describe here will work whether there is an Ending Date or Not.

The pattern is also relevant for other types than date. The pattern is usable whenever you have dependencies between rows in a table. 

Use the pattern whenever you read a set of data containing a Starting Date and you need to implement a loop to throw away unwanted records. An example could be Codeunit 7000 "Sales Price Calc. Mgt.". In this codeunit there are many loop constructs to find prices and discounts.

## Usage

In the following example, we have a fictive table containing: Code, Starting Date and Price. The Primary Key consist of Code, Starting Date. The Database is the Demo Database, and the Company is Cronus.  

[![ ][image0]][anchor0]  


### 1. Create the view

You will need to create the view before you define the Table Object.  
You will need to create a view for every company in the database. 
```sql
    CREATE VIEW [dbo].[CRONUS$PriceView]  
    AS  
    SELECT [Code], [Starting Date], [Price]  
    FROM dbo.[CRONUS$Price] AS A  
    WHERE [Starting Date] =  
    (SELECT MAX([Starting Date])  
    FROM dbo.[CRONUS$Price] AS B  
    WHERE B.[Code] = A.[Code] AND  
    B.[Starting Date] <= GETDATE())
```

Test the view to ensure that you get the correct result. It is much easier to test now than later.

### 2. Create the Table object

Remember to set the link table property before you save it.

### 3. Implement the code

IF PriceView.FINDSET THEN // You have them

### 4. Create a deployment codeunit

Create a SQL Deployment codeunit to manage your views.  
The codeunit needs to Create or Alter the views for all companies.  
To see an example of how to talk to SQL Server using .NET see waldo's blog here:  
[http://dynamicsuser.net/blogs/waldo/archive/2011/07/19/net-interop-calling-stored-procedures-on-sql-server-example-1.aspx][anchor1]  

### 5. Deployment

You need to deploy in three steps:

1. Delete the table objects referencing the views 
2. Deploy and run the deployment codeunit
3. Deploy the new table objects that reference the views 

### General precaution

If you later want to change the view, you need to follow these rules:

\* If you add columns, you need to add them to the view first and then add them to the Table Object.
\* If you want to remove columns from the view, you need to delete the Table Object, then change the view and last recreate the Table Object without the new columns.

### Code example that accomplish the same but without using the pattern

This following example will give you the same result but the performance will deteriorate as time goes by and you get more and more old data.
```al
Price.SETCURRENTKEY(Code,"Starting Date"); 
Price.SETFILTER("Starting Date",'..%1', TODAY  
IF Price.FINDSET THEN BEGIN  
    REPEAT
        Price.SETRANGE(Code, Price.Code);
        Price.FINDLAST;
        Price.SETRANGE(Code);
        PriceTemp := Price;
        PriceTemp.INSERT;
    UNTIL Price.NEXT = 0;
END;   
// PriceTemp will contain the Prices
```

### Comparison

The above NAV example is for a very simple date controlled solution and is provided to give an idea of what the pattern changes seen from a NAV development point of view. But consider the following:

* The table has a more complex key.  
This will require setting and clearing more filters
* You need to read from more than one table.  
Say you need to apply discount from a separate table.  
This may give several lines in PriceTemp. 
* If the Code field is controlled by a Type field.  
The Code field reference keys in different tables

All three examples above can be implemented directly in the view. By using the pattern, it will still only require a single line of NAV code.

Using the pattern will only issue one SQL call and thereby one trip to the server.  
The NAV Example will require an unknown number of SQL calls and thereby an unknown number of trips to the server. The number of SQL calls is dependent on the number of distinct Code values.  
The NAV example will require SQL Server to read all data older than or equal to TODAY, but only return one row per Code. Over time, as old data piles up in the system, the NAV code will perform slower because the SQL statements will be slower.  
The Pattern makes a scalable solution with a predictable performance. The performance will not deteriorate at the same rate as the NAV code example.

## NAV Usages 

The pattern does not exist in NAV (yet J). We have used it several times in our code for an Add-On.

## Ideas for improvement

  
Query Object should be able to handle sub-selects and Unions. A simple solution could be to allow the NAV developer to specify the actual Select statement inside the query Object in clear text. Opening up for writing your own queries and map the projection to the Query-defined fields will make the query Object very versatile and remove the pressure from Microsoft trying to create all the different permutations that a select statement can have. Microsoft and others have all tried to create wizards that can create SQL select statement. They all end up having a clear text option.

An alternative would be better support for linked table objects, specifically views. The current implementation is very fragile. 

The pattern only supports fetching data for a given date (normally today). This is because you cannot control the where-clause of the sub-select. 

## Related Topics

The idea of having a linked table object pointing to a view could be a pattern of its own.



[anchor0]: 6545.Table.png
[anchor1]: http://dynamicsuser.net/blogs/waldo/archive/2011/07/19/net-interop-calling-stored-procedures-on-sql-server-example-1.aspx


[image0]: 6545.Table.png
