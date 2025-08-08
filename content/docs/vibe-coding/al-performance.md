---
title: "AL Performance Optimization Rules"
description: >
  Performance optimization guidelines and best practices for AL development
globs: ["*.al"]
alwaysApply: true
---

# AL Performance Optimization Rules

These rules focus on writing performant AL code that scales well and provides optimal user experience in Business Central environments.

## AL Performance Guidelines Summary
- Always analyze performance impact when adding new features
- Optimize queries by filtering data as early as possible
- Avoid unnecessary loops; use set-based operations when possible
- Use SetLoadFields to minimize data retrieval
- Use temporary tables, dictionaries, or lists for temporary data storage

## Rule 1: Early Data Filtering and Query Optimization

### Intent
Optimize queries by filtering data as early as possible to reduce data transfer and processing overhead. Apply filters before processing records, use appropriate table keys and sorting, minimize the amount of data retrieved from the database, and use SetRange and SetFilter methods effectively.

### Examples

```al
// Good example - Early filtering
procedure GetNumberOfCustomersByCity(CityFilter: Text): Integer
var
  Customer: Record Customer;
begin
  Customer.SetRange(City, CityFilter);
  Customer.SetRange(Blocked, Customer.Blocked::" ");
  if Customer.FindSet() then
    repeat
      // Process only filtered customers
    until Customer.Next() = 0;
    
  exit(Customer.Count);
end;
```

```al
// Bad example (avoid processing all records)
procedure GetNumberOfCustomersByCity(CityFilter: Text): Integer
var
  Customer: Record Customer;
  Count: Integer;
begin
  if Customer.FindSet() then
    repeat
      // Processing all customers then filtering
      if (Customer.City = CityFilter) and (Customer.Blocked = Customer.Blocked::" ") then
        Count += 1;
    until Customer.Next() = 0;
    
  exit(Count);
end;
```

## Rule 2: Use SetLoadFields for Optimal Data Retrieval

### Intent
Use SetLoadFields to minimize data retrieval from the database by loading only the fields you need. Place SetLoadFields before the Get or Find operation, and include only the fields that will be used in your code.

### Examples

```al
// Good example - SetLoadFields with filtering
Item.SetRange("Third Party Item Exists", false);
Item.SetLoadFields("Item Category Code");
Item.FindFirst();
```

```al
// Bad example (avoid SetLoadFields after filtering)
Item.SetLoadFields("Item Category Code");
Item.SetRange("Third Party Item Exists", false);
Item.FindFirst();
```

## Rule 3: Use Temporary Tables, Dictionaries, and Lists for Performance

### Intent
Leverage temporary tables, dictionaries, and lists to improve performance in read-heavy scenarios and complex data processing. Use temporary tables for structured record data, dictionaries for key-value pairs, and lists for simple collections that are only temporarily needed.

### Examples

```al
// Good example - Using temporary tables for structured data
procedure ProcessSalesData(var TempSalesLine: Record "Sales Line" temporary)
var
  SalesLine: Record "Sales Line";
begin
  // Load data into temporary table once
  if SalesLine.FindSet() then
    repeat
      TempSalesLine := SalesLine;
      TempSalesLine.Insert();
    until SalesLine.Next() = 0;
    
  // Process temporary data multiple times without database hits
  ProcessDiscounts(TempSalesLine);
  CalculateTotals(TempSalesLine);
  ValidateInventory(TempSalesLine);
end;
```

```al
// Good example - Using dictionaries for key-value temporary data
procedure CacheCustomerData()
var
  Customer: Record Customer;
  CustomerCache: Dictionary of [Code[20], Text];
begin
  if Customer.FindSet() then
    repeat
      CustomerCache.Add(Customer."No.", Customer.Name);
    until Customer.Next() = 0;
    
  // Use cached data for lookups
  ProcessOrdersWithCache(CustomerCache);
end;
```

```al
// Good example - Using lists for simple collections
procedure GetBlockedCustomers(): List of [Code[20]]
var
  Customer: Record Customer;
  BlockedCustomers: List of [Code[20]];
begin
  Customer.SetRange(Blocked, Customer.Blocked::All);
  if Customer.FindSet() then
    repeat
      BlockedCustomers.Add(Customer."No.");
    until Customer.Next() = 0;
    
  exit(BlockedCustomers);
end;
```

## Rule 4: Avoid Unnecessary Loops - Use Set-Based Operations

### Intent
Minimize looping operations and favor set-based approaches when possible to improve performance. Use built-in aggregation methods (CalcSums, CalcFields), leverage SQL-based operations through AL, avoid nested loops when possible, and use batch operations for multiple record updates.

### Examples

```al
// Good example - Set-based operation
procedure GetTotalSalesAmount(CustomerNo: Code[20]): Decimal
var
  CustLedgerEntry: Record "Cust. Ledger Entry";
begin
  CustLedgerEntry.SetRange("Customer No.", CustomerNo);
  CustLedgerEntry.CalcSums(Amount);
  exit(CustLedgerEntry.Amount);
end;
```

```al
// Bad example (avoid manual loops for aggregation)
procedure GetTotalSalesAmount(CustomerNo: Code[20]): Decimal
var
  CustLedgerEntry: Record "Cust. Ledger Entry";
  TotalAmount: Decimal;
begin
  CustLedgerEntry.SetRange("Customer No.", CustomerNo);
  if CustLedgerEntry.FindSet() then
    repeat
      TotalAmount += CustLedgerEntry.Amount;
    until CustLedgerEntry.Next() = 0;
    
  exit(TotalAmount);
end;
```

## Rule 5: Performance Impact Analysis

### Intent
Always analyze and consider performance impact when adding new features or modifying existing code. While the AL compiler does not have direct access to performance profilers, you should implement performance-optimal code patterns from the start and consider scalability implications of code changes.

### Examples

```al
// Good example - Performance-conscious implementation
procedure UpdatePricesForItems(var Item: Record Item)
var
  ItemCount: Integer;
begin
  // Check data volume before processing
  ItemCount := Item.Count();
  
  if ItemCount > 1000 then begin
    // Use batch processing for large datasets
    UpdatePricesInBatches(Item);
  end else begin
    // Direct processing for smaller datasets
    UpdatePricesDirectly(Item);
  end;
end;
```

```al
// Good example - Batched modifications to minimize database writes
procedure UpdateCustomerStatistics(CustomerNo: Code[20])
var
  Customer: Record Customer;
  TotalBalance: Decimal;
  LastPaymentDate: Date;
begin
  // Calculate all values first
  CalculateCustomerTotals(CustomerNo, TotalBalance, LastPaymentDate);
  
  // Single database write with all changes
  Customer.SetLoadFields("Balance (LCY)", "Last Payment Date");
  if Customer.Get(CustomerNo) then begin
    Customer."Balance (LCY)" := TotalBalance;
    Customer."Last Payment Date" := LastPaymentDate;
    Customer.Modify(true);
  end;
end;
``` 