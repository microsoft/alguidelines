+++
title = "Surrogate keys using Autoincrement Pattern"
weight = 630
+++
_By [Soren Klemmensen][anchor0], [_Partner-Ready-Software_ ][anchor1] & [360 Visibility][anchor2]_

## **Abstract**

This Pattern is meant to create generic & reusable links between tables. The goal is to have an easy generic way to link a generically designed sub table to a record on a main table which can be used for other links too.

To minimize the impact of customizations and to keep modules as generic and reusable as possible the idea of the Implementation of surrogate keys using AutoIncrement pattern is:

* To create a generic and reusable auto generated link (A Surrogate Key), Immune to natural key data & requirement changes, on a main table with minimum impact on the table. 
* To create generic and reusable sub tables that effortless can be reused anywhere in the application.

## **Description**

Over years of development many things are repeated across different implementation and even inside the same application. A typical example could be adding comments to an area just as it is done in Microsoft Dynamics NAV multiple times. There can be reasons for doing this again and again, but not only does this need to be maintained and upgraded over the years, but all the implementations of comments also needs to be tested separately. If a standard and generic comment could be developed and a generic way of connecting it to a main table this could be resolved. This is exactly what this pattern is trying accomplish.

[![ ][image0]][anchor3]

_Figure 1: Table structure for linking a Document Header and Line Table with a Document Comment Table._

__

_[![ ][image1]][anchor4]_

_Figure 2: Table Structure for linking a Master Data Table with a Master Data Comment Table_

__

A typical way of linking a table to master data or to a document has been to use the primary key of the table being linked to. This causes some issues as the linked table now is designed specifically for the main table and it functionality cannot be reused. In case of renames the linked table needs to be renamed too which is costly in processing. Code also needs to be added on the delete trigger of the table to ensure that the attached records get removed if needed which increases the over all footprint of any change.

_[![ ][image2]][anchor5]_

_Figure 3: A Generic Way of creating a Comment table and linking it in a generic way to the main table no matter what this table might be. The Unique Record Identifier on the main tables is an Integer with AutoIncrement set to yes._

__

It is recommended using this pattern in all tables which need sub tables unless specific reasons exists for not doing this.

## **Usage**

**Step 1**: Create a generic Unique Record Identifier in the main table

The Pattern is implemented by adding a Field (I have called it Unique Record Identifier for this article) in a table (the main Table) where links are needed to be established to. Set the Property Data Type to Integer, Editable to No & AutoIncrement to Yes.

**Step 2**: Create a generic link-able sub table.

Create a new table (Sub Table) which you would like to be reusable with a primary key containing

* Table No. of Data Type Integer
* Unique Record Identifier of Data Type Integer
* A 3 field for allowing multiple entries to exist per record in the main table if so needed. This could be a Line No. of Data Type Integer or a Code field of Data Type Code(10) and so on.

The important part here is that the 2 or 3 first fields in the tables primary key is the Table No. and the Unique Record Identifier. If a 3rd field was added to allow for multiple entries to exist per record in the main table this should also be included in the primary key.

Make sure to also add any other fields containing the information you wish to store as needed to the table.

The sub table will be immune to renames from the main table as the main tables primary key is not used in the primary key of the sub table. The Insert, Modify and Rename triggers are not causing any issues and only the delete trigger will need to be considered. This can be dealt with generically from the OnDatabaseDelete trigger in Codeunit 1\. I would recommend to use a Hook Pattern for this.

**Step 3**: Create a page showing the sub table information.

Now create a page showing the data in the sub table. 

**Step 4**: Add an Action or factbox.

Create an action or factbox on the pages showing the main table data linking to the subpage with Table ID filtered to a constant of the Table No. of the main table and the Unique Record Identifier of the sub table filtering to the Unique Record Identifier of the main table.

**Step 5**: Create a Hook for Function OnDatabaseDelete in Codeunit 1 ApplicationManagement if one doesn't already exist. See the Hook pattern.

**Step 6**: Create a code to delete records in the Sub table if a main table record is deleted if this is required. This function needs to be called from the Hook created in Step 5\.

## **NAV Specific Example**

Let's assume we would like to create comment for a larger number of very different tables in the system.

**Update the Customer table (Step 1)**: Go to table 18 Customer and add Field 50000 Unique Record Identifier. Set the Property Data Type to Integer, Editable to No & AutoIncrement to Yes. Save the changes. 

_[![ ][image3]][anchor6]_

__

_[![ ][image4]][anchor7]_

__

**Create Comment table (Step 2)**: Create a new table called Comment. Add 3 fields Table No., Unique Record Identifier & Line No. all of Data Type Integer. Make these 3 fields into the primary key for the table. Add a 4 field called Comment with Data Type Text(80). Save the changes.

[![ ][image5]][anchor8]

**Create the Comments page (Step 3)**: Start the page wizard and create a Page based on table comment created above as a List only showing the Comment field. Set AutoSplitKey property to Yes on the page.

__

_[![ ][image6]][anchor9]_

__

**Update the Customer Card (Step 4)**: Add an action to the Customer Card to open the Comments. Go to Actions and create an action called Smart Comment. Populate the properties RunObject, RunPageView and RunPageLink as see in the picture below.

[![ ][image7]][anchor10]

__

**Create a hook OnDatabaseDelete (Step 5)**:

Create a codeunit 50000 called "ApplicationManagement Hook" with one function called OnAfterOnDatabaseDelete taking the parameter RecRef of Data Type RecordRef. Add it as a global variable to Codeunit 1 ApplicationManagement and call the function as the last line in OnDatabaseDelete. Please read about the hook pattern before implementing it.

[![ ][image8]][anchor11]

__

**Create the code needed to delete comments linked to a deleted record (Step6)**:

Create a DeleteComments function taking the RecRef of Data Type RecordRef and add the code as shown blow.

[![ ][image9]][anchor12]

__

It is assumed in this example for simplicity that the Field 50000 is reserved across the entire application for the Unique Record Identifier as defined in Step 1\.

The comments are now fully working if we look away from the fact that we did not deal with a few things specific to Sales orders like RecreateSalesLines function, Archiving and Copy Document. All of which can easily be dealt with.

These comments are now completely reusable everywhere else in the system. Sales Document is a perfect example as the primary keys of the Sales Header and the Sales line are both different from the customer and normally we would not be able to use the comments header or the line. All that needs to be done is adding the Field 50000 Unique Record Identifier to the Sales Header & Sales Line (Disregarding the posted documents in this example) and creating the actions on the Page Actions with the needed filters. Deletion is already handled of comments no matter what the main table might be.

Looking at the main table the Unique Record Identifier is also completely reusable for any other linking needed.

Other ideas for use of this pattern could be. An Error table, Tags, Dynamically defined fields and Generic fact boxes. Only the imagination sets limits for its use.

Upgrade wise there can be an impact if data is moved out of tables to be reinserted again because of a change to a database structure. This will cause the Unique Record Identifier to change, unless steps are taken to avoid this, and the links will need to be reestablished.

Other risks could be if Transfer Fields are used and data is being copied unintentionally.

## **NAV Usages**

This is a new pattern not currently used in Microsoft Dynamics NAV.

## **Ideas for improvement**

The reason for doing it this way is that you now can reuse your code again and again with only creating the same link on other tables instead of building it from scratch each time reducing testing needed and improving the quality of the overall product.

## **Consequences (When it should not be used)**

This can be used on any table when linking anything to it that can be considered a generic module which can be reused. That said it should not be used in cases where there is a risk of tables growing so much that performance could be impacted. There are ways to reduce performance impact when using high volume tables, but that is outside the scope of this pattern.

## **Related Topics** 

This is related to the hook pattern as far as they both try to reduce the footprint changes have on the standard application, by creating reusable ways to interact with the standard code. That said the hook pattern is more about hooking the functionality to existing while this pattern is more about creating reusable ways of creating functionality.

__



[anchor0]: http://mvp.microsoft.com/en-us/mvp/Soren%20Klemmensen-5001002 "Soren Klemmensen"
[anchor1]: http://partner-ready-software.com/ "Partner-Ready-Software"
[anchor2]: http://www.360visibility.com/ "360 Visibility"
[anchor3]: 0458.Figure-1.PNG
[anchor4]: 0638.Figure-2.PNG
[anchor5]: 0333.Figure-3.PNG
[anchor6]: 1488.Example-Figure-1.png
[anchor7]: 4682.Example-Figure-2.png
[anchor8]: 4532.Example-Figure-3.png
[anchor9]: 2068.Example-Figure-4.png
[anchor10]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/0552.Example-Figure-5.png
[anchor11]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/4477.Example-Figure-6.png
[anchor12]: /cfs-file.ashx/__key/communityserver-wikis-components-files/00-00-00-00-42/1884.Example-Figure-7.png


[image0]: 0458.Figure-1.PNG
[image1]: 0638.Figure-2.PNG
[image2]: 0333.Figure-3.PNG
[image3]: 1488.Example-Figure-1.png
[image4]: 4682.Example-Figure-2.png
[image5]: 4532.Example-Figure-3.png
[image6]: 2068.Example-Figure-4.png
[image7]: 0552.Example-Figure-5.png
[image8]: 4477.Example-Figure-6.png
[image9]: 1884.Example-Figure-7.png
