+++
title = "No Series"
weight = 870
tags = ["C/AL"]
categories = ["Pattern"]
+++
_By Bogdana Botez, at Microsoft Development Center Copenhagen_

## Abstract

Number series assignment is widely used in Microsoft Dynamics NAV to automatically assign unique numbers to data entries.

## Description

Documents and entities of any type (invoices, orders, customers, inventory items etc) are usually assigned an unique number, which can be later referenced whenever that particular entry needs to be consulted. When a new data entry is created, NAV implements the possibility to auto-assign a new number for this entry. For example, whenever a new sales order is created, it can be auto-numbered. This number has a specific format which is set up previously by the NAV administrator. For example, sales orders could have numbers between SO00001 and SO99999\. When all the numbers in this series have been used, NAV will show an error and the administrator has to either extend the current number series, or create a new series to be used.

## Usage

The number series is implemented at the table level. Each time a new record is inserted, the user can either:

* type a new number (if allowed by the setup), or
* have an auto-generated number created.

### Number Series definition

From the implementation point of view, a number series is a record in the table 308 - "No. Series".

[![ ][image0]][anchor0]  

The most relevant fields are:
|
----|----|----
Code | Code 10 | Used to identify the number series in further places where it will be used.
Default Nos. | Boolean | The boolean **Default Nos.** decides whether this number series can use automatic numbering. If false, then the user is expected to manually look up the No. field and select it from the number series.
Manual Nos. | Boolean | If the **Manual Nos.** is Yes, then the used will be allowed to manually type the value of the next number ("No.") field.
Date Order | Boolean | **Date Order** is used to decide whether or not the numbers from the range are assigned chronologically.

### Number Series sequence

The sequence definition is found in table 309 - No. Series Line. Contains the explicit definition of the series, for example a series called BANK, could start with BANK001 and increase incrementally by one, in the sequence BANK001, BANK002, BANK003, .... Until the last number BANK999\. When hitting the number BANK990, the user will receive a warning that the series is about to be exhausted and it must either be increased, or a new series should be assigned.

[![ ][image1]][anchor1]

[![ ][image2]][anchor2]

The most relevant fields are:

|
----|----|----
Series Code | Code 10 | Links it to the number series defined in table 308\.
Starting No. | Integer | This is the first number in the sequence, for a book indexing application it can be BOOK0001\.
Ending No. | Integer | The last number in the sequence, for example BOOK5000\.
Warning No. | Integer | When this number is reached, the user will see a warning message stating that the number series is running out of assignable numbers.
Increment-by No. | Integer | The value for incrementing the numeric part of the series.
Last No. Used | Code 20 | The last number from the sequence that was assigned.
Last Date Used | Date | Stating when the last number was assigned.

### Add the default number series to the setup

Various series of numbers can be defined as seen above. For example, a library can have a number series for indexing rental of each of the following: books, movies, music, video. The books series can be split by domain, for example a series BOOKSCIFI can have BOOK0001...BOOK1500, then BOOKTECH can use the sequence BOOK1501...BOOK4000 and so on.

The default number series for a certain application area is typically stored in the domain setup table. For example, NAV Demo Database stores the default Item number series in the Inventory Setup table 313\. This means that you will need to add the Number Series fields in your setup table and ensure that before the customer starts using the solution, the number series is created and added to the setup defaults.

### How to use the Number Series

The table which will host the number from the number series, needs the following fields:

|
----|----|----
No. | Code 20 | Contains the auto-generated sequential number.
No. Series | Code 10 | The number series definition, which decides what the next No. will be.

And the code to make the number series alive:

**OnInsert**

```al
OnInsert()

IF "No." = '' THEN 
    NoSeriesMgt.InitSeries(DefaultNoSeriesCode,OldNoSeriesCode,NewDate,NewNo,NewNoSeriesCode);
```

**Field "No."**

```al
No. - OnValidate()

IF "No." <> xRec."No." THEN BEGIN // Validate that "No." corresponds to the current No. Series rules 
    NoSeriesMgt.TestManual(DefaultNoSeriesCode);
    "No. Series" := '';
END;
```

**AssistEdit**

```al
AssistEdit() : Boolean

IF "No." = '' THEN 
    NoSeriesMgt.InitSeries(DefaultNoSeriesCode,OldNoSeriesCode,NewDate,NewNo,NewNoSeriesCode);
```

Where:

* **DefaultNoSeriesCode** is typically found in the application domain setup table.
* **OldNoSeriesCode** is typically the previous value of the "No. Series" code, which is found in **xRec."No. Series"**
* **NewDate** is typically 0D (empty date)
* NewNo and **NewNoSeriesCode** are the current values found in **"No."** and **"No. Series"**

## NAV Usages

See below an example of how NAV implements the number series pattern.

### Sales and Receivables

The setup table 311 (Sales & Receivables Setup) contains the default number series to be used in the whole application domain. In the demo data, some of the series defined here are: Customer Nos., Quote Nos., Order Nos., Invoice Nos., Posted Invoice Nos., Credit Memo Nos., Posted Credit Memo Nos., etc. Those number series are dimensioned for the needs of a typical small to medium sized company, however, various business have different patterns (for example, posting an unusual high number of invoices). In this case, the number series should be adjusted accordingly to make space for the customized company needs.

The default number series defined in the setup is then used in the individual tables. For example, table 18 - Customer, has

### Field "No."

```al
{ 1 ; ;No. ;Code20 ;AltSearchField=Search Name;

OnValidate=

BEGIN
    IF "No." <> xRec."No." THEN BEGIN
        SalesSetup.GET;

        NoSeriesMgt.TestManual(SalesSetup."Customer Nos.");

        "No. Series" := '';
    END;

    IF "Invoice Disc. Code" = '' THEN
        "Invoice Disc. Code" := "No.";
END;
} 
```

### Field "No. Series"

```al
{ 107 ; ;No. Series ;Code10 ;TableRelation="No. Series"; Editable=No }
```

### AssistEdit

```al
PROCEDURE AssistEdit@2(OldCust@1000 : Record 18) : Boolean;
VAR
    Cust@1001 : Record 18;
BEGIN
    WITH Cust DO BEGIN
        Cust := Rec; 
        SalesSetup.GET; 

        SalesSetup.TESTFIELD("Customer Nos."); 

        IF NoSeriesMgt.SelectSeries(SalesSetup."Customer Nos.",OldCust."No. Series","No. Series") THEN BEGIN
            NoSeriesMgt.SetSeries("No."); 
            Rec := Cust; 
            EXIT(TRUE); 
        END; 
    END;
END;
```

### OnInsert

```al
OnInsert=  
    BEGIN 
        IF "No." = '' THEN BEGIN 
            SalesSetup.GET; 

            SalesSetup.TESTFIELD("Customer Nos.");

            NoSeriesMgt.InitSeries(SalesSetup."Customer Nos.",xRec."No. Series",0D,"No.","No. Series"); 
        END;
    ...
    END
```

To run the AssistEdit procedure, include this code on the No. - OnAssistEdit() trigger of the Page:

### No. - OnAssistEdit() 

```al
IF AssistEdit(xRec) THEN
    CurrPage.UPDATE;
```

{{< youtube 1lG9rY_dmM4>}}



[anchor0]: 5661.1.png
[anchor1]: 1452.2.png
[anchor2]: 3527.3.png
[anchor3]: https://www.youtube.com/watch?v=1lG9rY_dmM4&list=PLhZ3P-LY7CqmVszuvtJLujFyHpsVN0U_w&index=8


[image0]: 5661.1.png
[image1]: 1452.2.png
[image2]: 3527.3.png
