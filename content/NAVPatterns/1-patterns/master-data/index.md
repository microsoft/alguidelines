+++
title = "Master Data"
weight = 780
+++
_By [Soren Klemmensen][anchor0], [_Partner-Ready-Software_ ][anchor1] & [360 Visibility][anchor2]_

# Abstract

The goal of this design pattern is to outline a standard way of creating master data, such as customers, vendors or items, in NAV.

# Description

This pattern creates a standard master data entity, listing all necessary prerequisites, attributes and methods to generate a basic version of the data entity.

It is being used for creating a master data entity and can roughly be divided into 2 categories: Business and Product

Examples of Master Data tables are:

Business Data:

* Table 18: Customer
* Table 23: Vendor
* Table 270: Bank Account
* Table 5050: Contact

Product Data:

* Table 15 G/L Account
* Table 27: Item
* Table 156: Resource
* Table 5600: Fixed Asset
* Table 5718: Nonstock Item
* Table 5913: Loaner

Master Data has attributes such as Location, Countries & Item Vendors. These tables are closely related to Master Data tables and are called Supplemental and Subsidiary tables.

Supplemental:

* Table 9: Country/Region
* Table 14: Location

Subsidiary:

* Table 99: Item Vendor

Master Data is among other used in journals, posting routines and historic data like ledger entries.

**Naming & Conventions**

Table & Card Page

* Singular

* Examples: Customer, Vendor & Item

List Page

* Plural if Editable = TRUE. 

* Singular + "List" if Editable = FALSE.

# Example

The data entity has a single primary key field with the following properties:

|
----------|---------
Field No. | 1
Name      | "No."
Date Type | Code 20

Each master data entity has a field which gives a name tag to the data entity carrying the natural name of the entity. This field is called Name if the data entity refers to a living person or an organization, such as a customer or a vendor; it is called Description instead if the data entity does not refer to a person or organization, such as an item. The field has the following properties:

|
----------|---------
Name      | Name or Description
Date Type | Text 50

An added benefit of using the Name or Description field naming is that this will be part of the dropdown when looking up based on the table relation.

The table properties of a master data table contain the following entries:

|
------------------|---------
LookupPageID      | A ListType Page referring to the table which is not editable.
DrillDownPageID   | The same page as defined in the LookupPageID
DataCaptionFields | The primary key plus the field which provides the primary name tag for the record. This will typically be the Name or Description field defined earlier.


# NAV Specific Example

We will create an Example table here with a Card and List Page as described above.

First we create the Table with No. as the primary key.

[![ ][image0]][anchor3]

Than we create a non-editable Page called Example List

[![ ][image1]][anchor4]

Than we create an Example Card Page with the following definition

[![ ][image2]][anchor5]

Now we can set the properties of the 2 pages.

First the Example Card Page Properties

[![ ][image3]][anchor6]

Than the Example List Page Properties

[![ ][image4]][anchor7]

Last but not least we set the Table Properties

[![ ][image5]][anchor8]

# NAV Usages 

You can see this pattern used in the following tables & pages:

Business Data:

* Table 18: Customer, Page 21 Customer Card & Page 22 Customer List
* Table 23: Vendor, Page 26 Vendor Card & Page 27 Vendor List
* Table 270: Bank Account, Page 370 Bank Account Card & Page 371 Bank Account List
* Table 5050: Contact, Page 5050 Contact Card & Page 5052 Contact List

Product Data:

* Table 15 G/L Account, Page 17 G/L Account Card & Page 18 G/L Account List
* Table 27: Item, Page 30 Item Card & Page 31 Item List
* Table 156: Resource, Page 76 Resource Card & Page 77 Resource List
* Table 5600: Fixed Asset, Page 5600 Fixed Asset Card & Page 5601 Fixed Asset List
* Table 5718: Nonstock Item, Page 5725 Nonstock Item Card & Page 5726 Nonstock Item List
* Table 5913: Loaner, Page 5922 Loaner Card & Page 5923 Loaner List

# Consequences

This pattern should not be used when this is not Master Data.

# References

Patterns that are typically used in connection with the Master Data Pattern could be the **"No. Series", "Address Integration"** and/or the **"Entity State"** design patterns., Master Data are central to almost everything we do, so most patterns connect in one way or another to the Master Data Pattern.



[anchor0]: http://mvp.microsoft.com/en-us/mvp/Soren%20Klemmensen-5001002 "Soren Klemmensen"
[anchor1]: http://partner-ready-software.com/ "Partner-Ready-Software"
[anchor2]: http://www.360visibility.com/ "360 Visibility"
[anchor3]: Table.PNG
[anchor4]: PageListDefinition.PNG
[anchor5]: PageCardDefinition.PNG
[anchor6]: PageCardPropertiesV2.PNG
[anchor7]: PageListPropertiesV2.PNG
[anchor8]: TablePropertiesV2.PNG


[image0]: Table.PNG
[image1]: PageListDefinition.PNG
[image2]: PageCardDefinition.PNG
[image3]: PageCardPropertiesV2.PNG
[image4]: PageListPropertiesV2.PNG
[image5]: TablePropertiesV2.PNG
