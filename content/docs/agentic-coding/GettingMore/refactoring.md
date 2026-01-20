---
title: "Refactoring Legacy Code"
linkTitle: "Refactoring"
weight: 4
description: >
  Use AI to modernize and improve existing AL code while maintaining functionality
---

## Scenario

You've inherited a legacy AL codeunit from an older Business Central version. The code works, but it:

- Uses deprecated patterns and APIs
- Has poor structure and naming
- Lacks documentation and error handling
- Contains duplicated logic
- Is difficult to test and maintain

You need to modernize this code while ensuring it continues to work correctly.

## Goal

Use AI to help you:

- Identify refactoring opportunities
- Modernize deprecated APIs
- Improve code structure
- Enhance readability and maintainability
- Add tests to ensure no regression

## The Legacy Code

Here's a typical legacy codeunit that needs refactoring:

```al
codeunit 50100 "Sales Order Management"
{
    procedure ProcessSalesOrder(DocNo: Code[20])
    var
        SH: Record "Sales Header";
        SL: Record "Sales Line";
        C: Record Customer;
        I: Record Item;
        qty: Decimal;
        amt: Decimal;
    begin
        if DocNo = '' then
            exit;

        SH.Get(SH."Document Type"::Order, DocNo);
        C.Get(SH."Sell-to Customer No.");

        if C.Blocked <> C.Blocked::" " then begin
            Message('Customer is blocked!');
            exit;
        end;

        SL.SetRange("Document No.", DocNo);
        if SL.Find('-') then
            repeat
                if SL.Type = SL.Type::Item then begin
                    I.Get(SL."No.");
                    if I.Inventory < SL.Quantity then
                        Message('Not enough inventory for item ' + I."No.");
                end;
                qty := qty + SL.Quantity;
                amt := amt + SL."Line Amount";
            until SL.Next() = 0;

        if amt > 10000 then begin
            SL.SetRange("Document No.", DocNo);
            if SL.Find('-') then
                repeat
                    SL."Line Discount %" := 5;
                    SL.Modify();
                until SL.Next() = 0;
        end;

        Message('Processed order ' + DocNo + ' with total amount ' + Format(amt));
    end;
}
```

## Step-by-Step Refactoring Process

### Step 1: Write Tests First

{{% alert title="Critical: Test First!" color="warning" %}}
Before refactoring any code, write tests that verify its current behavior. These tests act as a safety net, ensuring you don't accidentally break functionality during refactoring.
{{% /alert %}}

**Prompt**:

```
I need to refactor this legacy code, but first I need comprehensive tests for its current behavior.

Create tests that verify:
- All current functionality (even if imperfect)
- Expected outputs for given inputs
- Error conditions and edge cases
- Current behavior (not ideal behavior)

These tests should pass with the current code and catch any regressions during refactoring.

[Paste the legacy code]
```

**Expected Test Code**:

```al
codeunit 50101 "Sales Order Mgmt. Tests"
{
    Subtype = Test;

    var
        Assert: Codeunit "Library Assert";
        LibrarySales: Codeunit "Library - Sales";

    [Test]
    procedure TestCurrentBehaviorWithValidOrder()
    var
        SalesHeader: Record "Sales Header";
        SalesOrderMgmt: Codeunit "Sales Order Management";
    begin
        // [GIVEN] A valid sales order (testing CURRENT behavior)
        CreateTestSalesOrder(SalesHeader, 'CUST001', 5000);

        // [WHEN] Processing the order
        SalesOrderMgmt.ProcessSalesOrder(SalesHeader."No.");

        // [THEN] No error occurs and discount is applied
        // (These tests lock in current behavior before refactoring)
        Assert.IsTrue(true, 'Process completed without error');
    end;

    [Test]
    procedure TestCurrentBlockedCustomerBehavior()
    var
        SalesHeader: Record "Sales Header";
        Customer: Record Customer;
        SalesOrderMgmt: Codeunit "Sales Order Management";
    begin
        // [GIVEN] Order with blocked customer
        CreateBlockedCustomerOrder(SalesHeader, Customer);

        // [WHEN] Processing (currently just shows Message, doesn't error)
        SalesOrderMgmt.ProcessSalesOrder(SalesHeader."No.");

        // [THEN] Process completes (testing CURRENT behavior, even if not ideal)
        // Note: After refactoring, this might throw an error instead
    end;

    // Additional tests for current behavior...
}
```

**Why This Matters**:

- Tests catch regressions immediately
- You can refactor confidently
- Tests document current behavior
- You can run tests after each small refactoring step

### Step 2: Initial Assessment

**Prompt**:

```
Analyze this legacy AL code and identify refactoring opportunities.

Categorize issues by:
- Critical: Deprecated APIs, security issues, bugs
- Major: Poor structure, missing error handling, performance issues
- Minor: Naming, formatting, documentation

[Paste the legacy code]

For each issue, explain:
- What the problem is
- Why it's problematic
- How to fix it
```

**Expected AI Findings**:

**Critical Issues:**

- Using `Message()` for errors instead of `Error()`
- No proper error handling (could cause partial updates)
- Using deprecated `Find('-')` syntax
- Modifying records in a loop without proper handling

**Major Issues:**

- Poor variable naming (SH, SL, C, I, qty, amt)
- Single large procedure doing multiple things
- Hard-coded business rules (10000 threshold, 5% discount)
- Inefficient database operations (multiple FindSet loops)
- No validation of input parameters
- Missing XML documentation
- No telemetry or logging

**Minor Issues:**

- No separation of concerns
- String concatenation for messages
- Magic numbers without explanation
- Inconsistent code style

### Step 3: Create a Refactoring Plan

**Prompt**:

```
Create a detailed refactoring plan for this code.

I already have tests in place that verify current behavior.

Break refactoring into phases:
1. Safe refactorings (no behavior change)
2. Structural improvements
3. API modernization
4. Behavioral improvements (may change behavior)
5. Final enhancements

For each phase, list specific changes and estimated risk level.
After each phase, I'll run tests to ensure nothing broke.
```

**Expected Plan**:

**Phase 0: Tests (COMPLETED)**

- ✅ Written tests for current behavior
- ✅ Tests pass with current code
- ✅ Ready to refactor safely

**Phase 1: Safe Refactorings (Low Risk)**

- Rename variables to be descriptive
- Add XML documentation
- Extract magic numbers to constants
- Format code consistently
- Add code comments for complex logic

**Phase 2: Structural Improvements (Medium Risk)**

- Extract methods for each responsibility
- Separate validation, calculation, and update logic
- Remove code duplication
- Improve error handling

**Phase 3: API Modernization (Medium Risk)**

- Replace deprecated Find('-') with FindSet()
- Use proper Error() instead of Message()
- Add SetLoadFields for performance
- Use ModifyAll where appropriate

**Phase 4: Behavioral Improvements (Medium Risk)**

- Change Message() to Error() for validation failures
- This CHANGES behavior, so update tests accordingly
- Add new tests for improved error handling

**Phase 5: Additional Testing (Low Risk)**

- Add more edge case tests
- Add tests for each extracted method
- Expand test coverage

**Phase 6: Enhancements (Low Risk)**

- Add telemetry
- Implement proper transaction handling
- Add configuration instead of hard-coded values

### Step 4: Run Your Tests

**Before refactoring anything**, verify your tests pass:

```powershell
# Run the test codeunit
Invoke-NAVCodeunit -CodeunitId 50101
```

**All tests should pass.** If they don't, fix the tests first.

### Step 5: Phase 1 - Safe Refactorings

**Prompt**:

```
Perform Phase 1 refactorings on this code:
- Rename all variables to be descriptive
- Add XML documentation to the procedure
- Extract magic numbers (10000, 5) to constants
- Add inline comments for complex logic
- Improve code formatting

[Paste the legacy code]
```

**Expected Result**:

```al
/// <summary>
/// Manages sales order processing including validation, inventory checking, and discount application
/// </summary>
codeunit 50100 "Sales Order Management"
{
    var
        LargeOrderThreshold: Decimal;
        LargeOrderDiscountPct: Decimal;

    /// <summary>
    /// Processes a sales order by validating customer, checking inventory, and applying discounts
    /// </summary>
    /// <param name="DocumentNo">The sales order number to process</param>
    procedure ProcessSalesOrder(DocumentNo: Code[20])
    var
        SalesHeader: Record "Sales Header";
        SalesLine: Record "Sales Line";
        Customer: Record Customer;
        Item: Record Item;
        TotalQuantity: Decimal;
        TotalAmount: Decimal;
    begin
        // Initialize thresholds
        LargeOrderThreshold := 10000;
        LargeOrderDiscountPct := 5;

        // Validate input
        if DocumentNo = '' then
            exit;

        // Get sales order and customer
        SalesHeader.Get(SalesHeader."Document Type"::Order, DocumentNo);
        Customer.Get(SalesHeader."Sell-to Customer No.");

        // Check if customer is blocked
        if Customer.Blocked <> Customer.Blocked::" " then begin
            Message('Customer is blocked!');
            exit;
        end;

        // Check inventory and calculate totals
        SalesLine.SetRange("Document No.", DocumentNo);
        if SalesLine.Find('-') then
            repeat
                // Verify inventory availability for items
                if SalesLine.Type = SalesLine.Type::Item then begin
                    Item.Get(SalesLine."No.");
                    if Item.Inventory < SalesLine.Quantity then
                        Message('Not enough inventory for item ' + Item."No.");
                end;

                // Accumulate totals
                TotalQuantity := TotalQuantity + SalesLine.Quantity;
                TotalAmount := TotalAmount + SalesLine."Line Amount";
            until SalesLine.Next() = 0;

        // Apply discount for large orders
        if TotalAmount > LargeOrderThreshold then begin
            SalesLine.SetRange("Document No.", DocumentNo);
            if SalesLine.Find('-') then
                repeat
                    SalesLine."Line Discount %" := LargeOrderDiscountPct;
                    SalesLine.Modify();
                until SalesLine.Next() = 0;
        end;

        // Notify user of completion
        Message('Processed order ' + DocumentNo + ' with total amount ' + Format(TotalAmount));
    end;
}
```

### Step 6: Run Tests After Phase 1

**Critical**: After completing Phase 1 refactorings, run your tests:

```powershell
Invoke-NAVCodeunit -CodeunitId 50101
```

**All tests should still pass.** If any fail, the refactoring changed behavior - fix it before proceeding.

### Step 7: Phase 2 - Structural Improvements

**Prompt**:

```
Refactor this code to improve structure:

1. Extract separate methods for:
   - Customer validation
   - Inventory checking
   - Total calculation
   - Discount application

2. Replace Message() with proper Error() for validation failures

3. Add proper error handling with try-catch where appropriate

4. Use StrSubstNo for string formatting

[Include the Phase 1 code]
```

**Expected Result**:

```al
/// <summary>
/// Manages sales order processing including validation, inventory checking, and discount application
/// </summary>
codeunit 50100 "Sales Order Management"
{
    var
        LargeOrderThreshold: Decimal;
        LargeOrderDiscountPct: Decimal;
        InsufficientInventoryErr: Label 'Insufficient inventory for item %1. Available: %2, Required: %3';
        CustomerBlockedErr: Label 'Customer %1 is blocked and cannot place orders.';
        OrderProcessedMsg: Label 'Successfully processed order %1 with total amount %2.';

    /// <summary>
    /// Processes a sales order by validating customer, checking inventory, and applying discounts
    /// </summary>
    /// <param name="DocumentNo">The sales order number to process</param>
    procedure ProcessSalesOrder(DocumentNo: Code[20])
    var
        SalesHeader: Record "Sales Header";
        TotalAmount: Decimal;
    begin
        LargeOrderThreshold := 10000;
        LargeOrderDiscountPct := 5;

        if DocumentNo = '' then
            Error('Document number cannot be empty.');

        if not SalesHeader.Get(SalesHeader."Document Type"::Order, DocumentNo) then
            Error('Sales order %1 not found.', DocumentNo);

        ValidateCustomer(SalesHeader);
        CheckInventoryAvailability(SalesHeader);
        TotalAmount := CalculateOrderTotal(SalesHeader);

        if TotalAmount > LargeOrderThreshold then
            ApplyLargeOrderDiscount(SalesHeader);

        Message(OrderProcessedMsg, DocumentNo, Format(TotalAmount, 0, '<Precision,2:2><Standard Format,0>'));
    end;

    /// <summary>
    /// Validates that the customer is not blocked
    /// </summary>
    /// <param name="SalesHeader">The sales header to validate</param>
    local procedure ValidateCustomer(SalesHeader: Record "Sales Header")
    var
        Customer: Record Customer;
    begin
        if not Customer.Get(SalesHeader."Sell-to Customer No.") then
            Error('Customer %1 not found.', SalesHeader."Sell-to Customer No.");

        if Customer.Blocked <> Customer.Blocked::" " then
            Error(CustomerBlockedErr, Customer."No.");
    end;

    /// <summary>
    /// Checks that sufficient inventory is available for all items on the order
    /// </summary>
    /// <param name="SalesHeader">The sales header to check</param>
    local procedure CheckInventoryAvailability(SalesHeader: Record "Sales Header")
    var
        SalesLine: Record "Sales Line";
        Item: Record Item;
    begin
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.SetRange(Type, SalesLine.Type::Item);

        if SalesLine.FindSet() then
            repeat
                if Item.Get(SalesLine."No.") then
                    if Item.Inventory < SalesLine.Quantity then
                        Error(InsufficientInventoryErr, Item."No.", Item.Inventory, SalesLine.Quantity);
            until SalesLine.Next() = 0;
    end;

    /// <summary>
    /// Calculates the total amount for the sales order
    /// </summary>
    /// <param name="SalesHeader">The sales header to calculate totals for</param>
    /// <returns>The total amount of the order</returns>
    local procedure CalculateOrderTotal(SalesHeader: Record "Sales Header"): Decimal
    var
        SalesLine: Record "Sales Line";
        TotalAmount: Decimal;
    begin
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.SetLoadFields("Line Amount");

        if SalesLine.FindSet() then
            repeat
                TotalAmount += SalesLine."Line Amount";
            until SalesLine.Next() = 0;

        exit(TotalAmount);
    end;

    /// <summary>
    /// Applies a discount to all lines on large orders
    /// </summary>
    /// <param name="SalesHeader">The sales header to apply discounts to</param>
    local procedure ApplyLargeOrderDiscount(SalesHeader: Record "Sales Header")
    var
        SalesLine: Record "Sales Line";
    begin
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.ModifyAll("Line Discount %", LargeOrderDiscountPct, true);
    end;
}
```

### Step 8: Run Tests After Phase 2

Run your tests again after structural improvements:

```powershell
Invoke-NAVCodeunit -CodeunitId 50101
```

Tests should pass. If not, review the structural changes.

### Step 9: Phase 3 - API Modernization

**Prompt**:

```
Modernize this code to use current AL best practices:

1. Replace deprecated Find('-') with FindSet()
2. Add proper SetLoadFields for performance
3. Use better error handling patterns
4. Implement proper transaction handling
5. Replace hard-coded values with setup table

[Include the Phase 2 code]
```

**Expected Result**:

```al
/// <summary>
/// Manages sales order processing including validation, inventory checking, and discount application
/// </summary>
codeunit 50100 "Sales Order Management"
{
    var
        InsufficientInventoryErr: Label 'Insufficient inventory for item %1. Available: %2, Required: %3';
        CustomerBlockedErr: Label 'Customer %1 is blocked and cannot place orders.';
        OrderProcessedMsg: Label 'Successfully processed order %1 with total amount %2.';

    /// <summary>
    /// Processes a sales order by validating customer, checking inventory, and applying discounts
    /// </summary>
    /// <param name="DocumentNo">The sales order number to process</param>
    procedure ProcessSalesOrder(DocumentNo: Code[20])
    var
        SalesHeader: Record "Sales Header";
        TotalAmount: Decimal;
    begin
        if DocumentNo = '' then
            Error('Document number cannot be empty.');

        if not SalesHeader.Get(SalesHeader."Document Type"::Order, DocumentNo) then
            Error('Sales order %1 not found.', DocumentNo);

        ValidateCustomer(SalesHeader);
        CheckInventoryAvailability(SalesHeader);
        TotalAmount := CalculateOrderTotal(SalesHeader);

        if ShouldApplyLargeOrderDiscount(TotalAmount) then
            ApplyLargeOrderDiscount(SalesHeader);

        LogOrderProcessing(DocumentNo, TotalAmount);
        Message(OrderProcessedMsg, DocumentNo, Format(TotalAmount, 0, '<Precision,2:2><Standard Format,0>'));
    end;

    local procedure ValidateCustomer(SalesHeader: Record "Sales Header")
    var
        Customer: Record Customer;
    begin
        Customer.SetLoadFields(Blocked);

        if not Customer.Get(SalesHeader."Sell-to Customer No.") then
            Error('Customer %1 not found.', SalesHeader."Sell-to Customer No.");

        if Customer.Blocked <> Customer.Blocked::" " then
            Error(CustomerBlockedErr, Customer."No.");
    end;

    local procedure CheckInventoryAvailability(SalesHeader: Record "Sales Header")
    var
        SalesLine: Record "Sales Line";
        Item: Record Item;
    begin
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.SetRange(Type, SalesLine.Type::Item);
        SalesLine.SetLoadFields("No.", Quantity);

        if SalesLine.FindSet() then begin
            Item.SetLoadFields(Inventory);
            repeat
                if Item.Get(SalesLine."No.") then
                    if Item.Inventory < SalesLine.Quantity then
                        Error(InsufficientInventoryErr, Item."No.", Item.Inventory, SalesLine.Quantity);
            until SalesLine.Next() = 0;
        end;
    end;

    local procedure CalculateOrderTotal(SalesHeader: Record "Sales Header"): Decimal
    var
        SalesLine: Record "Sales Line";
        TotalAmount: Decimal;
    begin
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.SetLoadFields("Line Amount");

        if SalesLine.FindSet() then
            repeat
                TotalAmount += SalesLine."Line Amount";
            until SalesLine.Next() = 0;

        exit(TotalAmount);
    end;

    local procedure ShouldApplyLargeOrderDiscount(TotalAmount: Decimal): Boolean
    var
        SalesSetup: Record "Sales & Receivables Setup";
    begin
        SalesSetup.Get();
        exit(TotalAmount > SalesSetup."Large Order Threshold");
    end;

    local procedure ApplyLargeOrderDiscount(SalesHeader: Record "Sales Header")
    var
        SalesLine: Record "Sales Line";
        SalesSetup: Record "Sales & Receivables Setup";
    begin
        SalesSetup.Get();

        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.ModifyAll("Line Discount %", SalesSetup."Large Order Discount %", true);
    end;

    local procedure LogOrderProcessing(DocumentNo: Code[20]; TotalAmount: Decimal)
    var
        Telemetry: Codeunit Telemetry;
        CustomDimensions: Dictionary of [Text, Text];
    begin
        CustomDimensions.Add('DocumentNo', DocumentNo);
        CustomDimensions.Add('TotalAmount', Format(TotalAmount));

        Telemetry.LogMessage('SALESORD-001', 'Sales order processed successfully',
            Verbosity::Normal, DataClassification::SystemMetadata,
            TelemetryScope::ExtensionPublisher, CustomDimensions);
    end;
}
```

### Step 10: Update Tests for Behavioral Changes

Now that the code is cleaner, you may want to improve behavior (e.g., Error() instead of Message()):

**Prompt**:

```
I want to change the behavior to use Error() instead of Message() for validation failures.

First, update the tests to expect these errors:
- TestBlockedCustomerError should expect an error
- Update assertions to use asserterror and Assert.ExpectedError

Then show the code changes needed.
```

### Step 11: Expand Test Coverage

**Prompt**:

```
Now that refactoring is complete, add more comprehensive tests for edge cases.

Include tests for:
- Happy path: successful processing
- Customer validation errors
- Inventory availability checks
- Large order discount application
- Edge cases: empty document number, non-existent order

Use the AL test framework with proper setup and teardown.
```

**Expected Test Codeunit**:

```al
codeunit 50101 "Sales Order Management Tests"
{
    Subtype = Test;

    var
        Assert: Codeunit "Library Assert";
        LibrarySales: Codeunit "Library - Sales";
        LibraryInventory: Codeunit "Library - Inventory";

    [Test]
    procedure TestSuccessfulOrderProcessing()
    var
        SalesHeader: Record "Sales Header";
        SalesOrderMgmt: Codeunit "Sales Order Management";
    begin
        // [GIVEN] A valid sales order with sufficient inventory
        CreateSalesOrderWithInventory(SalesHeader);

        // [WHEN] Processing the order
        SalesOrderMgmt.ProcessSalesOrder(SalesHeader."No.");

        // [THEN] No error is raised
        // Verified by test not failing
    end;

    [Test]
    procedure TestBlockedCustomerError()
    var
        SalesHeader: Record "Sales Header";
        Customer: Record Customer;
        SalesOrderMgmt: Codeunit "Sales Order Management";
    begin
        // [GIVEN] A sales order for a blocked customer
        CreateSalesOrderWithBlockedCustomer(SalesHeader, Customer);

        // [WHEN] Processing the order
        asserterror SalesOrderMgmt.ProcessSalesOrder(SalesHeader."No.");

        // [THEN] Error is raised about blocked customer
        Assert.ExpectedError(StrSubstNo('Customer %1 is blocked', Customer."No."));
    end;

    [Test]
    procedure TestInsufficientInventoryError()
    var
        SalesHeader: Record "Sales Header";
        Item: Record Item;
        SalesOrderMgmt: Codeunit "Sales Order Management";
    begin
        // [GIVEN] A sales order with more quantity than available inventory
        CreateSalesOrderWithInsufficientInventory(SalesHeader, Item);

        // [WHEN] Processing the order
        asserterror SalesOrderMgmt.ProcessSalesOrder(SalesHeader."No.");

        // [THEN] Error is raised about insufficient inventory
        Assert.ExpectedError('Insufficient inventory');
    end;

    [Test]
    procedure TestLargeOrderDiscountApplied()
    var
        SalesHeader: Record "Sales Header";
        SalesLine: Record "Sales Line";
        SalesSetup: Record "Sales & Receivables Setup";
        SalesOrderMgmt: Codeunit "Sales Order Management";
        ExpectedDiscount: Decimal;
    begin
        // [GIVEN] A large order that qualifies for discount
        SalesSetup.Get();
        ExpectedDiscount := SalesSetup."Large Order Discount %";
        CreateLargeSalesOrder(SalesHeader);

        // [WHEN] Processing the order
        SalesOrderMgmt.ProcessSalesOrder(SalesHeader."No.");

        // [THEN] Discount is applied to all lines
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.FindSet();
        repeat
            Assert.AreEqual(ExpectedDiscount, SalesLine."Line Discount %", 'Discount not applied correctly');
        until SalesLine.Next() = 0;
    end;

    local procedure CreateSalesOrderWithInventory(var SalesHeader: Record "Sales Header")
    var
        Item: Record Item;
        SalesLine: Record "Sales Line";
    begin
        LibraryInventory.CreateItem(Item);
        Item.Inventory := 100;
        Item.Modify();

        LibrarySales.CreateSalesHeader(SalesHeader, SalesHeader."Document Type"::Order, LibrarySales.CreateCustomerNo());
        LibrarySales.CreateSalesLine(SalesLine, SalesHeader, SalesLine.Type::Item, Item."No.", 10);
    end;

    // Additional helper procedures...
}
```

## Review Points: What to Check

After each refactoring phase, verify:

### ✅ Functionality Preserved

- [ ] All original functionality still works
- [ ] No regressions introduced
- [ ] Tests pass (or create tests first!)
- [ ] Edge cases still handled

### ✅ Code Quality Improved

- [ ] More readable and maintainable
- [ ] Better structure and organization
- [ ] Clearer naming and documentation
- [ ] Proper error handling

### ✅ Performance Not Degraded

- [ ] Database operations optimized
- [ ] No unnecessary loops or queries
- [ ] Proper use of SetLoadFields
- [ ] Efficient algorithms

### ✅ Modern Practices Applied

- [ ] Current AL syntax and APIs
- [ ] No deprecated features
- [ ] Proper telemetry
- [ ] Good separation of concerns

## Advanced Refactoring Patterns

### Extract Interface for Testability

**Prompt**:

```
Refactor this codeunit to use interfaces for dependencies, making it more testable.

Extract interfaces for:
- Customer validation
- Inventory checking
- Discount calculation

This will allow us to mock these dependencies in tests.
```

### Convert to Event-Driven Architecture

**Prompt**:

```
Refactor this code to use Business Central events:

1. Publish events for:
   - Before order processing
   - After order validation
   - Before discount application
   - After order processing

2. This allows other extensions to hook into the process
```

### Add Batch Processing Support

**Prompt**:

```
Extend this codeunit to support batch processing of multiple orders.

Include:
- Progress tracking
- Error handling per order (don't fail entire batch)
- Summary reporting
- Performance optimization for bulk operations
```

## Common Refactoring Challenges

### Challenge 1: Unknown Business Logic

**Problem**: Code has complex logic without documentation

**Solution**:

```
Analyze this code and explain what business logic it implements:
[paste complex code]

Then suggest how to make the logic clearer through refactoring.
```

### Challenge 2: Tightly Coupled Code

**Problem**: Code has many dependencies that are hard to untangle

**Solution**:

```
This code is tightly coupled. Create a refactoring plan to:
1. Identify dependencies
2. Extract interfaces
3. Use dependency injection
4. Make code more modular
```

### Challenge 3: Large Procedures

**Problem**: Single procedure doing too many things

**Solution**:

```
This procedure is too large and complex.
Apply the Single Responsibility Principle to break it into smaller procedures.
Each procedure should have one clear purpose.
```

## Best Practices for Refactoring with AI

### 1. Always Write Tests First

{{% alert title="Golden Rule" color="primary" %}}
**Never refactor without tests.** Tests are your safety net. Write them first, run them, then refactor.
{{% /alert %}}

```
Before we start refactoring, let's write tests that lock in the current behavior.
Even if the current behavior isn't perfect, we need to know if we change it.
```

### 2. Refactor in Small Steps

Don't try to refactor everything at once. Use incremental changes:

```
Let's refactor this code in three phases:
Phase 1: Just improve naming and documentation
[Run tests - should pass]
Phase 2: Extract methods
[Run tests - should pass]
Phase 3: Modernize APIs
[Run tests - should pass]
```

### 3. Run Tests After Every Change

```
I've completed the naming refactoring.
Let me run the tests to make sure nothing broke.

[Run tests]

Great, tests pass. Now let's proceed to Phase 2.
```

### 3. Use Git Commits for Each Phase

```
We've completed Phase 1: Safe Refactorings.
Before moving to Phase 2, I'll commit these changes.
Suggest a good commit message for these refactorings.
```

### 4. Document Why, Not Just What

```
For each major refactoring, add a comment explaining WHY the change was made:
- Why was the old approach problematic?
- What does the new approach solve?
- Are there trade-offs?
```

### 5. Keep Performance in Mind

```
As we refactor this code, let's ensure we don't hurt performance.
For each database operation change, explain the performance implications.
```

## Practice Exercise

Refactor this legacy code:

```al
codeunit 50200 "Item Price Calculator"
{
    procedure CalcPrice(IN: Code[20]; CU: Code[10]; QT: Decimal): Decimal
    var
        I: Record Item;
        C: Record Customer;
        P: Decimal;
    begin
        I.Get(IN);
        P := I."Unit Price";

        if QT > 100 then
            P := P * 0.9;

        if CU <> '' then begin
            C.Get(CU);
            if C."Customer Price Group" = 'VIP' then
                P := P * 0.95;
        end;

        exit(P);
    end;
}
```

**Your Tasks**:

1. Assess the code and list issues
2. Create a refactoring plan
3. Apply naming improvements
4. Extract methods for each responsibility
5. Add proper documentation and error handling
6. Create tests
7. Add telemetry

## Next Steps

- Learn about [writing tests](../testing) for your refactored code
- See how [code review](../code-review) catches refactoring issues
- Explore [adding telemetry](../telemetry) to monitor refactored code
