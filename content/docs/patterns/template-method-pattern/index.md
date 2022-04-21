---
title: "Template Method Pattern"
tags: ["AL"]
categories: ["Pattern"]
---

_Created by Patrick Schiefer, Described by Patrick Schiefer_

## Abstract
The goal of this pattern is too simplify the solution of  similar problems and make your code more readable


## Problem
In nearly every app you have sometimes to solve similar problems for diferent cases. Mostly not the same developer will solve every case. This results in diferent solutions.

## Description
The pattner is used when you have problems which are independent but require the same logical flow. Examples which occure very often are: Posting Documents, Printing Reports or Exporting Data. 

## Bad Code Example
```al
codeunit 50010 ExportSalesLines
{
    procedure ExportData(SalesHeader: Record "Sales Header", SalesLine : Record "Sales Line")
    begin
        if not SalesHeader.CheckData() then
          exit;
        repeat
          case SalesHeader.ExportType of
            Enum::ExportType::A:
              GenerateLineTypeA(SalesLine);
            Enum::ExportType::B:
              GenerateLineTypeB(SalesLine);
          end;
        until SalesLine.Next() = 0;
        
        case SalesHeader.ExportType of
          Enum::ExportType::A:
            WriteToFile();
          Enum::ExportType::B:
            SendToWebService();
        end;
    end;

    //TODO Implementation of procedures used in example
}
```
As you can see in this example the readability gets bader with every new case.

## The Pattern
To implement the Pattern you need at least 3 objects:
- A template codeunit
- An Interface which provides the needed procedures
- A codeunit which Implements the interrface

In my Example I show how to implement a data export with templating

We start with the template
```al
codeunit 50000 ExportTemplate
{
    procedure ExportData(export: Interface IDataExport)
    begin
        if not export.CheckData() then
            exit;
        if export.GetLinesToExport() then begin
            repeat
                export.ExportLine();
            until not export.NextLine();
        end;
        export.Finish();
    end;
}
```
As you can see the template just calls procedures via an interface and just defines the flow of the export without really implementing it.

As the second part we need an interface for the export functions
```al
interface IDataExport
{
    procedure CheckData(): Boolean;
    procedure GetLinesToExport(): Boolean;
    procedure ExportLine();
    procedure NextLine(): Boolean;
    procedure Finish();
}
```

And now we need an implementation. For my example I wrote a export Codeunit for Sales Headers
```al
codeunit 50001 SalesHeaderExport implements IDataExport
{
    procedure SetSalesHeader(DocType: Enum "Sales Document Type"; No: Code[10])
    begin
        SalesHeader.Get(DocType, No);
    end;

    procedure CheckData(): Boolean
    begin
        SalesHeader.TestField(Status, Enum::"Sales Document Status"::Released);
    end;

    procedure GetLinesToExport(): Boolean
    begin
        SalesLines.SetRange("Document Type", SalesHeader."Document Type");
        SalesLines.SetRange("Document No.", SalesHeader."No.");
        exit(SalesLines.FindSet());
    end;

    procedure ExportLine()
    begin
        //Generate Exportdata here
    end;

    procedure NextLine(): Boolean
    begin
        exit(SalesLines.Next() > 0);
    end;

    procedure Finish()
    begin
        // Send or Save data here
    end;

    var
        SalesHeader: Record "Sales Header";
        SalesLines: Record "Sales Line";
}
```

Now lets have a look how to use the pattern
```al
codeunit 50002 ExportOrders
{
    procedure ExportOrder(DocType: Enum "Sales Document Type"; No: Code[10])
    var
        export: Codeunit ExportTemplate;
        exportImpl: Codeunit SalesHeaderExport;
        exportInt: Interface IDataExport;
    begin
        exportImpl.SetSalesHeader(DocType, No);
        exportInt := exportImpl;
        export.ExportData(exportInt);
    end;
}
```



## Benefits
The logical flow is very easy to adopt, it is even possible to add entries to the queue while it is processed.

## Example

In this short example I show you how to post multiple sales orders and display message after finishing the last post.


We have two commands in this example, the "SalesOrderPostCommander" is used to post a sales order and the "MessageCommander" displays a message.
```al
codeunit 50104 "SalesOrderPostCommander" implements ICommand
{
   procedure SetSalesOrderNumber(value : Code[20])
   begin
     No := value;
   end;
   
   procedure Execute()
   begin
     // TODO Post Sales Header
   end;
   

   var 
     No : Code[20];
}


codeunit 50103 "MessageCommander" implements ICommand
{
    procedure SetText(value: Text);
    begin
        t := value;
    end;

    procedure Execute()
    begin
        Message(t);
    end;

    var
        t: Text;
}
```

Using this two codeunits we can now implement a patch posting
```al

codeunit 50105 PatchPostQueue
{
   procedure PatchPost()
   begin
     FilterSalesOrdersToPost();
     if not SalesOrders.Findset(false) then
       exit(); // Nothing to post
     
     repeat
       AddSalesOrderToQueue(SalesOrder."No.");
     until SalesOrders.Next() = 0;

     AddMessageToQueue('Posting Complete');
     ExecuteQueue();
   end;

   local procedure ExecuteQueue()
   var
     object : interface "ICommand";
   begin
     repeat 
       object := queue.Pop();
       object.Execute();
     until queue.GetSize() = 0;
   end;

   local procedure FilterSalesOrdersToPost()
   begin
      // Filter Sales Orders here
   end;
   
   local procedure AddMessageToQueue(message : Text);
   var 
     t: Codeunit MessageCommander;
     object: Interface ICommand;
   begin
     t.SetText(message);
     object := t;
     queue.Push(object);
   end;

   local procedure AddSalesOrderToQueue(No : Text);
   var 
     SaleOrderCommander: Codeunit SalesOrderPostCommander;
     object: Interface ICommand;
   begin
     SaleOrderCommander.SetSalesOrderNumber(No);
     object := SaleOrderCommander;
     queue.Push(object);
   end;
   

   var
     SalesOrders : Record "Sales Header";
     queue: Codeunit Queue;
}
```

## Benefits
Your code gains readability and it is very easy to add new cases for the template. You don't always have to think about the whole logic. You just have to implement the details

## When not to use
The Pattern should not be used for problems which differ to much. So for example if you have two data exports in your app, one is exporting header and lines and the second one only exports headers. In this case I would suggest to not use the pattern or to make two templates out of it.



## References 
[Detailed Explanation of the pattern](https://patrickschiefer.com/2022/04/08/template-method-pattern/)

