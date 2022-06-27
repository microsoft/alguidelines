---
title: "if not then exit"
tags: ["AL"]
categories: ["Best Practice"]
---

_Created by sirhc101, Described by sirhc101_

## Description

In general when we are working with tables we want to make sure, the filtered dataset includes records and does not result in a runtime error, so we use `if` to handle the result of `Find()`, `FindSet()`, `Get()`, etc.
This automatically causes on indent in source code and often the source code does not just contain one but two or more tables involved which leads to multi-level indentation.

Basically this is a result of bad coding structure but maybe sometimes necessary. On the other hand this causes multiple `end;` usages and leads to the usage of colorization and other helpers to see which `begin` belongs to which `end;`.

Instead of using `if (Record.FindSet()) then` to fetch records from a database it's good practice to use `if (not Record.FindSet()) then` following by an `exit();` to not further process the source code and make it clear for other developers where they can stop reading in certain cases.

Furthermore, this more or less automatically leads to smaller and better structured procedures and reduces the complexity of the source code.

## Bad code

```al
    SalesHeader.Reset();
    SalesHeader.SetRange("Document Type", SalesHeader."Document Type"::Order);
    SalesHeader.SetRange(Status, SalesHeader.Status::Open);
    if (SalesHeader.FindSet(false)) then begin
        repeat
            SalesLine.Reset();
            SalesLine.SetRange("Document Type", SalesHeader."Document Type"::Order);
            SalesLine.SetRange("Document No.", SalesHeader."No.");
            if (SalesLine.FindSet(true)) then begin
                repeat
                    DoSomething();
                until SalesLine.Next() = 0;
            end;
        until SalesHeader.Next() = 0;

        DoSomethingElse();
    end;
```

or

```al
    SalesLine.Reset();
    SalesLine.SetRange("Document Type", SalesHeader."Document Type"::Order);
    if (SalesLine.FindSet(true)) then begin
        repeat
            case SalesLine."Type" of
                SalesLine."Type"::Item:
                    DoSomethingItem();
                SalesLine."Type"::Resource:
                    DoSomethingResource();
            end;
        until SalesLine.Next() = 0;
    end;
```

## Good code

```al
    SalesHeader.Reset();
    SalesHeader.SetRange("Document Type", SalesHeader."Document Type"::Order);
    SalesHeader.SetRange(Status, SalesHeader.Status::Open);
    if (not SalesHeader.FindSet(false)) then
        exit;

    repeat
        SalesLine.Reset();
        SalesLine.SetRange("Document Type", SalesHeader."Document Type"::Order);
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        if (SalesLine.FindSet(true)) then begin
            repeat
                DoSomething();
            until SalesLine.Next() = 0;
        end;
    until SalesHeader.Next() = 0;

    DoSomethingElse();
end;
```

or

```al
    SalesLine.Reset();
    SalesLine.SetRange("Document Type", SalesHeader."Document Type"::Order);
    if (not SalesLine.FindSet(true)) then
        exit;

    repeat
        case SalesLine."Type" of
            SalesLine."Type"::Item:
                DoSomethingItem();
            SalesLine."Type"::Resource:
                DoSomethingResource();
        end;
    until SalesLine.Next() = 0;
```