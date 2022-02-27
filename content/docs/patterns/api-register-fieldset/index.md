---
title: "API Register Fieldset"
tags: ["AL","API"]
categories: ["Pattern"]
---

_Created by Arend-Jan Kauffmann, Described by Arend-Jan Kauffmann_

## Abstract
The goal of this pattern is to register the fields that are part of the request body of an API call.

## Context
A request to an API page to insert or modify a record requires a JSON body with the fields and values. It is not required to specify all fields that are exposed by the API page. Only those fields that are specified in the JSON body will be validated with a value. A call to insert a record (http POST) will leave the unspecified fields at their default value. A call to modify a record (http PATCH) will only update the specified fields and leave the other fields to their current value.

## Problem
The standard behavior can result in a number of challenges:

* It's not possible to implement mandatory fields. Especially for fields with an initial value like integers, decimals and booleans the code doesn't know if they are their initial value or were included in the API request.
* In the OnValidate trigger of a field it's not possible to verify if the API request is an insert or modify operation. Fields can't be protected to be modified during a specific operation, e.g. do not allow to modify during an insert.
* When implementing the Delegated API Operation pattern, for example to implement a template with default values, the template values should not overwrite the provided values in the API request while non-specified fields should get a value from the template.

## Description
To mitigate these problems, we can register the fields during the OnValidate trigger in a temporary table. During the delayed insert or modify operation the code knows which fields were part of the API request.

```al
    field(displayName; Rec.Description)
    {
        trigger OnValidate()
        begin
            RegisterFieldSet(Rec.FieldNo(Description));
        end;
    }

    ....

    var
        TempFieldSet: Record Field temporary;

    local procedure RegisterFieldSet(FieldNumber: Integer)
    begin
        if TempFieldSet.Get(Database::Item, FieldNumber) then
            exit;

        TempFieldSet.Init();
        TempFieldSet.TableNo := Database::Item;
        TempFieldSet."No." := FieldNumber;
        TempFieldSet.Insert();
    end;
```

Now that we have the list of fields that are part of the request, they can be checked during the insert or modify operation. Or they can be handed over to a delegated operation.

Some examples to work with the list of fields:

```al
    trigger OnInsertRecord(BelowxRec: Boolean): Boolean
    begin
        if TempFieldSet.Get(Database::Item, Rec.FieldNo(Inventory)) then
            Error(InventoryCannotBeChangedInAPostRequestErr);

        ItemAPIOperations.InsertItem(Rec, TempFieldSet)
        exit(false);
    end;

    trigger OnModifyRecord(): Boolean
    begin
        ItemAPIOperations.ModifyItem(Rec, TempFieldSet;
        exit(false);
    end;

    trigger OnDeleteRecord(): Boolean
    begin
        ItemAPIOperations.DeleteItem(Rec);
        exit(false);
    end;
```

The example code combines this with the Delegated API Operation pattern. The codeunit for the delegated operation can use the fieldset to apply a template while keeping the original values from the request.

```al
codeunit 50000 "Item API Operations"
{
    internal procedure InsertItem(var Item: Record Item; var TempFieldSet: Record "Field"; ModifiedDateTime: DateTime)
    var
        ConfigTemplateHeader: Record "Config. Template Header";
        ConfigTemplateManagement: Codeunit "Config. Template Management";
        RecRef: RecordRef;
    begin
        if not FindConfigTemplateHeader(Item, ConfigTemplateHeader) then
            exit;
        RecRef.GetTable(Item);

        if ConfigTemplateManagement.ApplyTemplate(RecRef, TempFieldSet, RecRef, ConfigTemplateHeader) then
            RecRef.SetTable(Item);
    end;

    internal procedure ModifyItem(var Item: Record Item)
    begin
    end;

    internal procedure DeleteItem(var Item: Record Item)
    begin
    end;
}
```

## Benefits
Having a list of fields that are part of the API request provides more information during to the insert or modify operation. This helps to implement specific behavior, based on which fields were specified in the API request.