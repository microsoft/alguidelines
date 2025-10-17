---
title: "Naming Conventions Rules"
description: >
  Comprehensive naming conventions for AL files, objects, variables, and functions
globs: ["*.al"]
alwaysApply: true
---

# Naming Conventions Rules

Consistent naming conventions improve code readability, maintainability, and help AI assistants understand code structure and intent.

## Rule 1: Object Naming Conventions

### Intent
Use consistent naming patterns for all AL objects to improve discoverability and maintain professional standards. Use PascalCase for object names (tables, pages, reports, codeunits) and meaningful, descriptive names that clearly indicate the object's purpose. Object names must not exceed 30 characters total, with a maximum of 26 characters for the name itself to reserve space for prefixes/affixes (3 characters + 1 space).

### Examples

```al
// Good examples (within 26 character limit)
table 50100 "Customer Ledger Entry"     // 20 chars
page 50101 "Sales Invoice"              // 13 chars  
codeunit 50102 "Sales Invoice Posting"  // 21 chars
report 50103 "Customer Statement"       // 18 chars
```

```al
// Bad examples (avoid abbreviations, unclear names, or length violations)
table 50100 "CustLE"                           // Too abbreviated
page 50101 "SalesInv"                          // Too abbreviated
table 50104 "Very Long Customer Ledger Entry" // 32 chars - exceeds limit
codeunit 50102 "SIPoster"                      // Unclear abbreviation
```

## Rule 2: File Naming Conventions

### Intent
Establish consistent file naming patterns that clearly identify object types and facilitate organized development. Use pattern `<ObjectName>.<ObjectType>.al` and maintain consistency across all file names. Ensure file names are descriptive and match the AL object name within the files.

### Examples

```al
// Good examples
NoSeries.Page.al
NoSeries.Table.al
NoSeriesErrorsImpl.Codeunit.al
NoSeriesSetup.Codeunit.al
CustomerCard.Page.al
SalesHeader.Table.al
PostSalesInvoice.Codeunit.al
ItemLedgerEntry.Report.al
InventorySetup.PageExt.al
SalesHeader.TableExt.al

// For implementations and interfaces
INoSeries.Interface.al
NoSeriesImpl.Codeunit.al

// For test files
NoSeriesTests.Codeunit.al
SalesPostingTests.Codeunit.al
```

## Rule 3: Variable and Function Naming

### Intent
Use consistent naming conventions for variables and functions to improve code readability. Use PascalCase for variable and function names, descriptive names that clearly indicate purpose, and avoid abbreviations unless they are well-known business terms. Use consistent parameter naming in procedures.

### Examples

```al
// Good examples - Variables
var
  CustomerLedgerEntry: Record "Cust. Ledger Entry";
  TotalAmount: Decimal;
  DiscountPercentage: Decimal;
  IsValidTransaction: Boolean;
```

```al
// Good examples - Functions
procedure CalculateCustomerBalance(CustomerNo: Code[20]): Decimal
procedure ValidateSalesDocument(var SalesHeader: Record "Sales Header")
procedure UpdateInventoryQuantity(ItemNo: Code[20]; Quantity: Decimal)
```

## Rule 4: Parameter Naming in Event Subscribers

### Intent
Use meaningful parameter names in event subscribers to improve code clarity and maintainability. Use descriptive parameter names that clearly indicate their purpose, follow Business Central conventions for common parameter types, and maintain consistency across similar event subscribers. Avoid unclear generic names like "Rec" - use specific descriptive names.

### Examples

```al
// Good example - Descriptive parameter names
[EventSubscriber(ObjectType::Table, Database::"Sales Header", OnBeforeInsert, '', false, false)]
local procedure AddDefaultValuesOnBeforeInsertSalesHeader(var SalesHeader: Record "Sales Header"; RunTrigger: Boolean)
begin
  // Event handling logic
end;

[EventSubscriber(ObjectType::Table, Database::Customer, OnBeforeModify, '', false, false)]
local procedure CheckBalanceOnBeforeModifyCustomer(var Customer: Record Customer; var xCustomer: Record Customer)
begin
  // Event handling logic
end;
```

## Rule 5: Interface and Implementation Naming

### Intent
Clearly distinguish between interfaces and their implementations using consistent naming patterns. Prefix interfaces with "I" (e.g., `INoSeries`), use "Impl" suffix for implementation codeunits, and keep interface and implementation names closely related. Ensure names stay within the 26-character limit.

### Examples

```al
// Good examples (within character limits)
// Interface file: ICustomerService.Interface.al
interface ICustomerService
{
    procedure GetCustomerBalance(CustomerNo: Code[20]): Decimal;
}

// Implementation file: CustomerServiceImpl.Codeunit.al
codeunit 50100 "Customer Service Impl" implements ICustomerService
{
    procedure GetCustomerBalance(CustomerNo: Code[20]): Decimal
    begin
        // Implementation logic
    end;
}
```