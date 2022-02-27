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

## Usage in Data Entities

To understand an example use in the Base App, the Customer data entity is a good choice.  

Implementation to connect the Customer **`No.`** field to the Number Series engine is done at the table level.  The Customer table contains:

A field to contain the number (typically the primary key), which will be of type **`Code`**, length of **20**:

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

And on the **`OnInsert`** trigger, code populates the **`No. Series`** and **`No.`** field.

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

`SalesSetup.Get();` fetches the sole setup table record.

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

Additionally, it is a good idea to have `OnValidate` functionality on the **`No.`** field.  The complete code for the Customer **`No.`** field:

```AL
field(1; "No."; Code[20])
{
    Caption = 'No.';

    trigger OnValidate()
    begin
        if "No." <> xRec."No." then begin
            SalesSetup.Get();
            NoSeriesMgt.TestManual(SalesSetup."Customer Nos.");
            "No. Series" := '';
        end;
        if "Invoice Disc. Code" = '' then
            "Invoice Disc. Code" := "No.";
    end;
}
```

If the user has changed the **`No.`** field (`"No." <> xRec."No."`), then:
- the Number Series is checked if manually setting a new value is allowed via the `TestManual` function
- The `No. Series` is cleared on the record, as it has no longer been given a value from that Series.   


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

{{% alert title="Note" color="warning" %}}
The use of **`WITH`** is deprecated.  While this code block represents the current state of the Base App, the use of **`WTIH`** should not be copied.
{{% /alert %}}

Similar to the **`OnInsert`** trigger, some setup fields are checked.

Then, the `SelectSeries` function is called.  This will present a List to the user of available and relevant **No. Series** that are connected to the `SalesSetup."Customer Nos."` by a Number Series Relationship.

From the **`Customer Page`** (a Card type page), the **No.** field has an **`AssistEdit`** trigger:

```AL
trigger OnAssistEdit()
begin
    if AssistEdit(xRec) then
        CurrPage.Update();
end;
```

## Usage in Journals

Journals utilize a **`Document No.`** as a non-primary key field and use a different strategy for use of the Number Series engine.  For each Journal Batch, a different **`No. Series`** can be set.

For example, on the **`General Journal`** Page, in the **`OnNewRecord`**, the **`SetUpNewLine`** function on the **`Gen. Journal Line`** Table is called:

```AL
procedure SetUpNewLine(LastGenJnlLine: Record "Gen. Journal Line"; Balance: Decimal; BottomLine: Boolean)
var
    IsHandled: Boolean;
begin
    IsHandled := false;
    OnBeforeSetUpNewLine(GenJnlTemplate, GenJnlBatch, GenJnlLine, LastGenJnlLine, GLSetupRead, Balance, BottomLine, IsHandled);
    if IsHandled then
        exit;

    GenJnlTemplate.Get("Journal Template Name");
    GenJnlBatch.Get("Journal Template Name", "Journal Batch Name");
    GenJnlLine.SetRange("Journal Template Name", "Journal Template Name");
    GenJnlLine.SetRange("Journal Batch Name", "Journal Batch Name");
    if GenJnlLine.FindFirst then begin
        "Posting Date" := LastGenJnlLine."Posting Date";
        "Document Date" := LastGenJnlLine."Posting Date";
        "Document No." := LastGenJnlLine."Document No.";
        OnSetUpNewLineOnBeforeIncrDocNo(GenJnlLine, LastGenJnlLine, Balance, BottomLine);
        if BottomLine and
            (Balance - LastGenJnlLine."Balance (LCY)" = 0) and
            not LastGenJnlLine.EmptyLine
        then
            IncrementDocumentNo(GenJnlBatch, "Document No.");
    end else begin
        "Posting Date" := WorkDate;
        "Document Date" := WorkDate;
        if GenJnlBatch."No. Series" <> '' then begin
            Clear(NoSeriesMgt);
            "Document No." := NoSeriesMgt.TryGetNextNo(GenJnlBatch."No. Series", "Posting Date");
        end;
    end;
    [...]
```

If the Batch is empty, and if the **`Gen. Journal Batch`** has a **`No. Series`** set, the **`Document No.`** is set from the number series via the **`NoSeriesManagement`** codeunit's **`TryGetNextNo`** function.  This takes two parameters:
- Which **`No. Series`** to get the next number from
- Which date to fetch for

This function does *not* update the **`Last No. Used`** and **`Last Date Used`** fields on the number series.  Those will be updated during the Posting process.


If the Batch is not empty *and* the sum of the existing lines totals to zero (in balance), the General Journal assumes the user wants to start a new set of lines under a new **`Document No.`**.  The table level procedure **`IncrementDocumentNo`** function is called:

```AL
procedure IncrementDocumentNo(GenJnlBatch: Record "Gen. Journal Batch"; var LastDocNumber: Code[20])
var
    NoSeriesLine: Record "No. Series Line";
begin
    if GenJnlBatch."No. Series" <> '' then begin
        NoSeriesMgt.SetNoSeriesLineFilter(NoSeriesLine, GenJnlBatch."No. Series", "Posting Date");
        if NoSeriesLine."Increment-by No." > 1 then
            NoSeriesMgt.IncrementNoText(LastDocNumber, NoSeriesLine."Increment-by No.")
        else
            LastDocNumber := IncStr(LastDocNumber);
    end else
        LastDocNumber := IncStr(LastDocNumber);
end;
```

If the batch's **`No. Series`** is set, it is checked if the **`Increment-By No.`** setting is anything besides `1`.  If so, use the special **`IncrementNoText`** function.

If neither of those cases is true, then the line's **`Document No.`** is updated with the language function **`IncStr`**.


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

Typically, this pattern is used for unique Data Entities. It is not recommended for use in parts of the system where entries are created permanently (such as an **`Entry No.`** for ledgers) or highly mutable / working line data (such as **`Line No.`** for journals or document lines). 

## List of references

For usage of number series, there is more information available on:
- [Microsoft Docs: Create Number Series](https://docs.microsoft.com/en-us/dynamics365/business-central/ui-create-number-series)
- [Microsoft Learn: Set up number series and trail codes](https://docs.microsoft.com/en-us/learn/modules/number-series-trail-codes-dynamics-365-business-central/)

For more programming details, there is more information on [Microsoft Docs: Number Sequences in Business Central](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/devenv-number-sequences).