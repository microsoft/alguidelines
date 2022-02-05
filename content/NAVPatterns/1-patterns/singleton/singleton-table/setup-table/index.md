+++
title = "Setup Table"
weight = 1070
+++
_By Abhishek Ghosh, at Microsoft Development Center Copenhagen_

[![ ][image0]][anchor0]

This is the first and most well-known of the two usual applications of the **Singleton Table** pattern in Dynamics NAV.

**Problem:** the developer needs to store information about the operating setup or environment in the database, in a way that can be persisted across sessions.

**Solution:** The information is stored in a table with one record only. The user is subsequently able to modify, but not add or delete records in the table.

The implementation of the pattern involves several considerations:

* Suffixing the table name with Setup (ex: General Ledger Setup).
* Defining a suitable primary key
* Creating a page where the user can view and edit a record, but not add new records or delete an existing one
* Optionally, updating the Company - Initialize codeunit.

**Defining a Primary Key**

Since this kind of tables is a collection of several environment or setup parameters, the primary key does not refer to any business attributes for this kind of tables. However, for maintaining the integrity of the database, it is necessary to define a primary key.

So, the most common implementation is to have a field "Primary Key" of Code\[10\]. This is populated with a blank value when the record is inserted. This field is not added to the page, so that the user cannot be modify it later.

**Creating a Page**

The **CardPage** type is most suitable for representing this kind of tables. In addition, the **InsertAllowed** and **DeleteAllowed** properties in the page should be set to false to prevent the user from adding or deleting records in the table.

In the **OnOpenPage** trigger, the following code should be added to insert a record when the user opens the page for the first time, if a record does not exist already.

```al
OnOpenPage()
    RESET;
    IF NOT GET THEN BEGIN
        INIT;
        INSERT;
    END;
```

The following diagram describes the flow of the program, once the user tries to access the setup information. The user opens the page. If the record containing setup information already exists, then the page opens on the existing record. Else, a new empty record is created and the page opens on it.

[![ ][image1]][anchor1]

**Company-Initialize Codeunit**

The Company-Initialize codeunit (codeunit 2) is executed when a new company is created. We recommended that you add records to the single-record tables in this codeunit. If some of the fields are expected to have default values, they can also be populated here.

**NAV Usages**

Several Setup tables in NAV implement this pattern. Some of those are:

* Table 98 General Ledger Setup
* Table 311 Sales & Receivables Setup
* Table 312 Purchases & Payables Setup
* Table 313 Inventory Setup
* Table 242 Source Code Setup

**Variation:** While most tables just insert a record with empty primary key in codeunit 2, table 242 ("Source Code Setup") offers an example of inserting default values into all fields of the table (method "InitSourceCodeSetup"). This practice, wherever feasible, is likely to reduce the effort during implementation.

**Related resources:** [Considerations on optimizing the Singleton Table, by Søren Klemmensen][anchor2].



[anchor0]: Setup-Table.png
[anchor1]: 6675.NAVSetupTablePattern2.png
[anchor2]: http://www.klemmensen.ca/Blog/Post/35/Initialize-Setup-Tables


[image0]: Setup-Table.png
[image1]: 6675.NAVSetupTablePattern2.png
