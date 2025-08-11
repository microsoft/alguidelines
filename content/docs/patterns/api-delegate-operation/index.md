---
title: "Delegate API Operation"
tags: ["AL","API"]
categories: ["Pattern"]
---

_Created by Arend-Jan Kauffmann, Described by Arend-Jan Kauffmann_

## Abstract
The goal of this pattern is to delegate data operations from the API page to a codeunit. The codeunit can implement its own logic for inserting, modifying or deleting data.

## Context
API pages implement a specific pattern for insert, modify and delete operations. An API page uses delayed insert behavior, which means that all fields will be validated before the record is inserted or modified.
This is different from the standard behavior on a UI card page, where a record is first inserted, followed by a modify when field values are updated.

## Problem
The standard behavior can result in a number of challenges:

* Code behind the OnValidate triggers and its event subscribers may expect that a record already exists when a field is being validated.
* Usage of temporary buffer tables can become really complex when fully handled from the API page.
* Applying default data to records before they are created does not work well with delayed inserts.

## Description
To mitigate these problems, we can delegate the data operation to a codeunit while canceling the data operation inside the API page.

```al
codeunit 50000 "Item API Operations"
{
    internal procedure InsertItem(var Item: Record Item)
    begin
    end;

    internal procedure ModifyItem(var Item: Record Item)
    begin
    end;

    internal procedure DeleteItem(var Item: Record Item)
    begin
    end;
}
```

It is important that the record parameter is updated with the final result. This allows the API page to return the result of the API operation to the caller.

The API page implements this in the page triggers as follows:

```al
    var
        ItemAPIOperations: Codeunit "Item API Operations";

    trigger OnInsertRecord(BelowxRec: Boolean): Boolean
    begin
        ItemAPIOperations.InsertItem(Rec);
        exit(false);
    end;

    trigger OnModifyRecord(): Boolean
    begin
        ItemAPIOperations.ModifyItem(Rec);
        exit(false);
    end;

    trigger OnDeleteRecord(): Boolean
    begin
        ItemAPIOperations.DeleteItem(Rec);
        exit(false);
    end;
```

The triggers must return false in order to cancel the operation in the page.

## Benefits
Inside the functions in the codeunit you have full control over the steps that are performed for the specific operation.
