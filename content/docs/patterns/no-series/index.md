+++
title = "No. Series"
tags = ["AL"]
categories = ["Pattern"]
+++

_Created by Microsoft, Described by Jeremy Vyska (Spare Brained Ideas)_

## Abstract

The "Number Series" system is used extensively to provide numbers to master records, documents, and other transactions through Microsoft Dynamics 365 Business Central.

## Description

At the heart of things, the Number Series engine allows users to define structure for a sequential numeric or alphanumeric string (collectively referred to as a 'number series'), then assign that structure to different parts of the system.

Typically, one creates a single number series for each _type_ of data entity.  For example, Customers or Sales Orders each could have a series defined so that all new Customers or Sales Orders get a new number automatically.

The Number Series system serves a few ancillary roles:

- maintains the usage information to know when the last number was generated and on which date 
- allows for date driven structures, so that different periods may have different structures
- allows control of if manual entries are or are not permitted
- allow for incrementing in different steps (+1 each time or +1000 each time)
- warn users as a series is running out of numbers
- control if any gaps in a series are permitted (as some regional laws do not allow skipping)

This is many roles, features, and controls for generation of a single field so the implementation of this can seem difficult at first.

{{% alert title="Note" color="info" %}}
One additional (and somewhat optional) feature in the Number Series engine allows multiple sequences per type, called **Relationships**.  For example, different numbers for Items that are finished goods versus raw materials.  This requires additional hooks on the Page.
{{% /alert %}}

## Usage in Business Central

To understand an example use in the Base App, the Customer data entity is a good choice.  

Implementation to connect the Customer "No." field to the Number Series engine is done at the table level.  The Customer table contains:

A field to contain the number (typically the primary key), which will be Code type, length of 20:

```AL
field(1; "No."; Code[20])
{
    Caption = 'No.';

    trigger OnValidate()
    begin
        [...]
    end;
}
```

A field to contain the unique ID of the Number Series, typically called "No. Series"
  
```AL  
field(107; "No. Series"; Code[20])
{
    Caption = 'No. Series';
    Editable = false;
    TableRelation = "No. Series";
}
```
{{% alert title="Note" color="warning" %}}
The **`TableRelation`** is important, and the **`Editable`** being false is advised.
{{% /alert %}}

And on the **OnInsert** trigger, code populates the **No. Series** and **No.** field.

```AL
trigger OnInsert()
var
    IsHandled: Boolean;
begin
    IsHandled := false;
    OnBeforeInsert(Rec, IsHandled);
    if IsHandled then
        exit;

    if "No." = '' then begin
        SalesSetup.Get();
        SalesSetup.TestField("Customer Nos.");
        NoSeriesMgt.InitSeries(SalesSetup."Customer Nos.", xRec."No. Series", 0D, "No.", "No. Series");
    end;
    [...]

    OnAfterOnInsert(Rec, xRec);
    end;
```

In the case of Customer, this is a Data Entity within the Sales module of the system.  The Sales module has a **Sales Setup** table where the user can specify a **No. Series** to use for Customers by default.

`SalesSetup.Get();` fetches the setup table.
`SalesSetup.TestField("Customer Nos.");` is the basic validation that the **Sales Setup** table has a non-empty **Customer Nos.** field.  If the setup field isn't populated, when the user attempts to create a new Customer, they will receive an error message.

`NoSeriesMgt.InitSeries(SalesSetup."Customer Nos.", xRec."No. Series", 0D, "No.", "No. Series");` is more parameters to a function than most expect.

The function call takes the following parameters:

```AL
procedure InitSeries(
  DefaultNoSeriesCode: Code[20]; 
  OldNoSeriesCode: Code[20];
  NewDate: Date;
  var NewNo: Code[20];
  var NewNoSeriesCode: Code[20])
```

The **DefaultNoSeriesCode** parameter is typically from a setup table. In the Customer example, this comes from the **Sales Setup** **Customer Nos.** setting.

The **OldNoSeriesCode** is used to verify when changing from one No Series to another that they are related.

The **NewDate** parameter is used to drive numbering based on Dates. This is typically used on Documents.  For master entities, like Customer, an empty date `0D` can be passed in.  

{{% alert title="Note" color="info" %}}
Many parts of the NoSeriesManagement codeunit predate method overloading, so if the system was created today, some parameters like NewDate would likely be optional.
{{% /alert %}}

The **NewNo** is a `var` parameter, and is how the new value comes back from the engine.  This also serves two other purposes:
 - if passed in blank, the Number Series used must be configured to have **Default Nos.** enabled
 - if passed in with a value, the Number Series used must be configured to have **Manual Nos** enabled.

The **NewNoSeriesCode** is more often used to switch between related number series, but is a required parameter, and is also passed back from the engine, so it is also a `var`.


Since the Customer data entity supports the **No. Series Relationship** functionality, there are additional components.  On the table, there is a function called `AssistEdit`:

```AL
procedure AssistEdit(OldCust: Record Customer): Boolean
var
    Cust: Record Customer;
begin
    with Cust do begin
        Cust := Rec;
        SalesSetup.Get();
        SalesSetup.TestField("Customer Nos.");
        if NoSeriesMgt.SelectSeries(SalesSetup."Customer Nos.", OldCust."No. Series", "No. Series") then begin
            NoSeriesMgt.SetSeries("No.");
            Rec := Cust;
            OnAssistEditOnBeforeExit(Cust);
            exit(true);
        end;
    end;
end;
```

Similar to the OnInsert, some setup fields are checked.

Then, the `SelectSeries` function is called.  This will present a List to the user of available and relevant **No. Series** that are connected to the `SalesSetup."Customer Nos."` by a Number Series Relationship.

From the Customer Page (a Card type page), the **No.** field has an AssistEdit trigger:

```AL
trigger OnAssistEdit()
begin
    if AssistEdit(xRec) then
        CurrPage.Update();
end;
```

## Objects to Inspect

Business Central objects in the Base App to review to find out more:

| Object Type | Object ID | Object Name              |
|-------------|-----------|--------------------------|
| Table       | 308       | No. Series               |
| Table       | 309       | No. Series Line          |
| Table       | 310       | No. Series Relationship  |
| Page        | 456       | No. Series               |
| Page        | 457       | No. Series Lines         |
| Page        | 458       | No. Series Relationships |
| Page        | 571       | No. Series List          |
| Codeunit    | 396       | NoSeriesManagement       |

## When not to use

Typically, this pattern is used for unique Data Entities. It is not recommended for use in parts of the system where entries are created permanently (such as an Entry No. for ledgers) or highly mutable / working line data (such as Line No. for journals or document lines). 

## List of references

For usage of number series, there is more information available on:
- [Microsoft Docs: Create Number Series](https://docs.microsoft.com/en-us/dynamics365/business-central/ui-create-number-series)
- [Microsoft Learn: Set up number series and trail codes](https://docs.microsoft.com/en-us/learn/modules/number-series-trail-codes-dynamics-365-business-central/)

For more programming details, there is more information on [Microsoft Docs: Number Sequences in Business Central](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/devenv-number-sequences).