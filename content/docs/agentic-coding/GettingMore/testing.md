---
title: "Writing Tests"
linkTitle: "Testing"
weight: 5
description: >
  Use AI to create comprehensive test coverage for your AL code
---

## Scenario

You've developed new features for your Business Central extension, but you need comprehensive test coverage to:

- Ensure code works as expected
- Prevent regressions when making changes
- Document expected behavior
- Enable confident refactoring
- Meet quality standards

Writing tests manually is time-consuming, and you want to use AI to accelerate the process while maintaining test quality.

## Goal

Use AI to help you:

- Generate unit tests for individual procedures
- Create integration tests for complex workflows
- Design test data and scenarios
- Write test helpers and fixtures
- Create mock objects for dependencies
- Implement data-driven tests

## The Code to Test

Here's a codeunit that needs test coverage:

```al
codeunit 50100 "Order Discount Manager"
{
    procedure CalculateDiscount(var SalesHeader: Record "Sales Header"): Decimal
    var
        Customer: Record Customer;
        DiscountPct: Decimal;
    begin
        if not Customer.Get(SalesHeader."Sell-to Customer No.") then
            Error('Customer %1 not found', SalesHeader."Sell-to Customer No.");

        DiscountPct := GetCustomerDiscount(Customer);
        DiscountPct += GetVolumeDiscount(SalesHeader);
        DiscountPct += GetSeasonalDiscount();

        if DiscountPct > 50 then
            DiscountPct := 50;

        exit(DiscountPct);
    end;

    local procedure GetCustomerDiscount(Customer: Record Customer): Decimal
    begin
        case Customer."Customer Discount Group" of
            'VIP':
                exit(10);
            'PREMIUM':
                exit(5);
            else
                exit(0);
        end;
    end;

    local procedure GetVolumeDiscount(SalesHeader: Record "Sales Header"): Decimal
    var
        SalesLine: Record "Sales Line";
        TotalAmount: Decimal;
    begin
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.CalcSums("Line Amount");
        TotalAmount := SalesLine."Line Amount";

        case true of
            TotalAmount >= 10000:
                exit(15);
            TotalAmount >= 5000:
                exit(10);
            TotalAmount >= 1000:
                exit(5);
            else
                exit(0);
        end;
    end;

    local procedure GetSeasonalDiscount(): Decimal
    begin
        if Date2DMY(Today, 2) in [11, 12] then
            exit(5);
        exit(0);
    end;
}
```

## Step-by-Step Testing Process

### Step 1: Generate Basic Test Structure

**Prompt**:

```
Create a test codeunit for the Order Discount Manager.

Include:
- Proper test codeunit structure with Subtype = Test
- Setup and teardown if needed
- Test procedure stubs for all public procedures
- Use AL test framework conventions
- Include Assert codeunit
- Add test isolation attributes where needed

[Include the codeunit code]
```

**Expected Result**:

```al
codeunit 50101 "Order Discount Mgr. Tests"
{
    Subtype = Test;

    var
        Assert: Codeunit "Library Assert";
        LibrarySales: Codeunit "Library - Sales";
        LibraryUtility: Codeunit "Library - Utility";
        IsInitialized: Boolean;

    [Test]
    procedure TestCalculateDiscountBasic()
    begin
        // Test basic discount calculation
    end;

    [Test]
    procedure TestVIPCustomerDiscount()
    begin
        // Test VIP customer gets 10% discount
    end;

    [Test]
    procedure TestVolumeDiscountTiers()
    begin
        // Test volume discount calculation at different amounts
    end;

    [Test]
    procedure TestSeasonalDiscount()
    begin
        // Test seasonal discount in November/December
    end;

    [Test]
    procedure TestMaximumDiscountCap()
    begin
        // Test discount cannot exceed 50%
    end;

    [Test]
    procedure TestCustomerNotFoundError()
    begin
        // Test error when customer doesn't exist
    end;

    local procedure Initialize()
    begin
        if IsInitialized then
            exit;

        // Setup code here
        IsInitialized := true;
    end;
}
```

### Step 2: Implement Happy Path Test

**Prompt**:

```
Implement the TestCalculateDiscountBasic test using the Given-When-Then pattern.

This test should verify:
- A standard customer with a medium-size order
- Gets the expected discount percentage
- No errors are raised

Use the Library codeunits for test data creation.
Include clear comments for each section.
```

**Expected Result**:

```al
[Test]
procedure TestCalculateDiscountBasic()
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    ActualDiscount: Decimal;
    ExpectedDiscount: Decimal;
begin
    // [SCENARIO] Calculate discount for a standard order
    Initialize();

    // [GIVEN] A standard customer with no special discount group
    Customer.Get(LibrarySales.CreateCustomerNo());
    Customer."Customer Discount Group" := '';
    Customer.Modify();

    // [GIVEN] A sales order with total amount of 2000 (5% volume discount)
    SalesHeader := CreateSalesOrderWithAmount(Customer."No.", 2000);

    // [GIVEN] Expected discount is 5% (volume only, not in seasonal months)
    ExpectedDiscount := 5;

    // [WHEN] Calculating the discount
    ActualDiscount := OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // [THEN] The discount matches expected value
    Assert.AreEqual(ExpectedDiscount, ActualDiscount, 'Discount calculation incorrect');
end;

local procedure CreateSalesOrderWithAmount(CustomerNo: Code[20]; TotalAmount: Decimal): Record "Sales Header"
var
    SalesHeader: Record "Sales Header";
    SalesLine: Record "Sales Line";
    Item: Record Item;
begin
    LibrarySales.CreateSalesHeader(SalesHeader, SalesHeader."Document Type"::Order, CustomerNo);

    Item."No." := LibraryUtility.GenerateRandomCode(Item.FieldNo("No."), Database::Item);
    Item."Unit Price" := TotalAmount;
    Item.Insert();

    LibrarySales.CreateSalesLine(SalesLine, SalesHeader, SalesLine.Type::Item, Item."No.", 1);
    SalesLine.Validate("Unit Price", TotalAmount);
    SalesLine.Modify(true);

    exit(SalesHeader);
end;
```

### Step 3: Implement Error Case Tests

**Prompt**:

```
Implement the TestCustomerNotFoundError test.

This should verify:
- When an invalid customer number is used
- An appropriate error is raised
- The error message contains the customer number

Use asserterror and Assert.ExpectedError.
```

**Expected Result**:

```al
[Test]
procedure TestCustomerNotFoundError()
var
    SalesHeader: Record "Sales Header";
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    InvalidCustomerNo: Code[20];
begin
    // [SCENARIO] Error is raised when customer doesn't exist
    Initialize();

    // [GIVEN] A sales header with non-existent customer
    InvalidCustomerNo := 'INVALID123';
    LibrarySales.CreateSalesHeader(SalesHeader, SalesHeader."Document Type"::Order, '');
    SalesHeader."Sell-to Customer No." := InvalidCustomerNo;
    SalesHeader.Modify();

    // [WHEN] Calculating discount for non-existent customer
    asserterror OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // [THEN] Appropriate error is raised with customer number
    Assert.ExpectedError(StrSubstNo('Customer %1 not found', InvalidCustomerNo));
end;
```

### Step 4: Implement Data-Driven Tests

**Prompt**:

```
Create a comprehensive test for all customer discount tiers (VIP, PREMIUM, standard).

Use a data-driven approach where one test function tests all scenarios.
Create a local helper that takes customer group and expected discount as parameters.

Test these scenarios:
- VIP customer: 10% discount
- PREMIUM customer: 5% discount
- Standard customer: 0% discount
- Empty group: 0% discount
```

**Expected Result**:

```al
[Test]
procedure TestCustomerDiscountTiers()
begin
    // [SCENARIO] Different customer types receive appropriate discounts
    Initialize();

    // [GIVEN] [WHEN] [THEN] VIP customers get 10% discount
    VerifyCustomerTypeDiscount('VIP', 10);

    // [GIVEN] [WHEN] [THEN] Premium customers get 5% discount
    VerifyCustomerTypeDiscount('PREMIUM', 5);

    // [GIVEN] [WHEN] [THEN] Standard customers get 0% discount
    VerifyCustomerTypeDiscount('STANDARD', 0);

    // [GIVEN] [WHEN] [THEN] Customers with no group get 0% discount
    VerifyCustomerTypeDiscount('', 0);
end;

local procedure VerifyCustomerTypeDiscount(CustomerGroup: Code[10]; ExpectedDiscount: Decimal)
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    ActualDiscount: Decimal;
begin
    // Create customer with specific group
    Customer.Get(LibrarySales.CreateCustomerNo());
    Customer."Customer Discount Group" := CustomerGroup;
    Customer.Modify();

    // Create order with amount below volume discount threshold (< 1000)
    SalesHeader := CreateSalesOrderWithAmount(Customer."No.", 500);

    // Calculate discount
    ActualDiscount := OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // Verify expected discount
    Assert.AreEqual(ExpectedDiscount, ActualDiscount,
        StrSubstNo('Incorrect discount for customer group %1', CustomerGroup));
end;
```

### Step 5: Test Edge Cases and Boundaries

**Prompt**:

```
Create tests for volume discount boundary conditions.

Test these specific amounts:
- 999: Just below 1000 threshold (expect 0% volume discount)
- 1000: Exactly at threshold (expect 5% volume discount)
- 4999: Just below 5000 threshold (expect 5% volume discount)
- 5000: Exactly at threshold (expect 10% volume discount)
- 9999: Just below 10000 threshold (expect 10% volume discount)
- 10000: Exactly at threshold (expect 15% volume discount)

Use descriptive test names and clear assertions.
```

**Expected Result**:

```al
[Test]
procedure TestVolumeDiscountBoundaries()
begin
    // [SCENARIO] Volume discounts applied at exact thresholds
    Initialize();

    // Just below first threshold
    VerifyVolumeDiscount(999, 0, 'Below 1000 threshold');

    // Exactly at first threshold
    VerifyVolumeDiscount(1000, 5, 'At 1000 threshold');

    // Between first and second threshold
    VerifyVolumeDiscount(4999, 5, 'Below 5000 threshold');

    // Exactly at second threshold
    VerifyVolumeDiscount(5000, 10, 'At 5000 threshold');

    // Between second and third threshold
    VerifyVolumeDiscount(9999, 10, 'Below 10000 threshold');

    // Exactly at third threshold
    VerifyVolumeDiscount(10000, 15, 'At 10000 threshold');
end;

local procedure VerifyVolumeDiscount(OrderAmount: Decimal; ExpectedVolumeDiscount: Decimal; Scenario: Text)
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    ActualDiscount: Decimal;
begin
    // Create standard customer (no customer discount)
    Customer.Get(LibrarySales.CreateCustomerNo());
    Customer."Customer Discount Group" := '';
    Customer.Modify();

    // Create order with specific amount
    SalesHeader := CreateSalesOrderWithAmount(Customer."No.", OrderAmount);

    // Calculate discount (assuming not in seasonal months)
    ActualDiscount := OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // Verify volume discount
    Assert.AreEqual(ExpectedVolumeDiscount, ActualDiscount,
        StrSubstNo('Incorrect volume discount for %1 (amount: %2)', Scenario, OrderAmount));
end;
```

### Step 6: Test Maximum Discount Cap

**Prompt**:

```
Create a test that verifies the 50% maximum discount cap.

Test scenario:
- VIP customer (10% discount)
- Large order (15% volume discount)
- Seasonal period (5% discount)
- Total would be 30%, which is under cap - verify this works
- Then test a scenario that would exceed 50% and verify it's capped

Consider: What combination would exceed 50%? We may need to modify setup or mock GetSeasonalDiscount.
```

**Expected Result**:

```al
[Test]
procedure TestMaximumDiscountCap()
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    ActualDiscount: Decimal;
begin
    // [SCENARIO] Discount is capped at 50% even when combined discounts exceed it
    Initialize();

    // [GIVEN] VIP customer (10% discount)
    Customer.Get(LibrarySales.CreateCustomerNo());
    Customer."Customer Discount Group" := 'VIP';
    Customer.Modify();

    // [GIVEN] Large order (15% volume discount)
    SalesHeader := CreateSalesOrderWithAmount(Customer."No.", 10000);

    // [GIVEN] If testing in Nov/Dec, total would be 10+15+5=30% (under cap)
    // [GIVEN] To test cap, we need a scenario that would exceed 50%
    // Note: With current business logic, max is 30%, so cap isn't reached

    // [WHEN] Calculating discount
    ActualDiscount := OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // [THEN] Discount doesn't exceed 50%
    Assert.IsTrue(ActualDiscount <= 50, 'Discount exceeds maximum allowed 50%');
end;

[Test]
procedure TestDiscountCapWithMultipleDiscounts()
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    ActualDiscount: Decimal;
begin
    // [SCENARIO] Combined discounts that would exceed 50% are capped
    // [NOTE] This test may need the business logic modified to make it relevant
    // Current max possible: 10% (VIP) + 15% (volume) + 5% (seasonal) = 30%

    Initialize();

    // Create scenario with maximum possible discounts
    Customer.Get(LibrarySales.CreateCustomerNo());
    Customer."Customer Discount Group" := 'VIP';
    Customer.Modify();

    SalesHeader := CreateSalesOrderWithAmount(Customer."No.", 10000);

    // Calculate discount
    ActualDiscount := OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // Verify cap is applied if needed
    Assert.IsTrue(ActualDiscount <= 50, 'Discount cap not enforced');
end;
```

### Step 7: Test Time-Dependent Logic

**Prompt**:

```
Create tests for the seasonal discount that depends on the current date.

Since GetSeasonalDiscount uses Today, we need to:
1. Test behavior for November (month 11)
2. Test behavior for December (month 12)
3. Test behavior for other months

Explain how to handle date-dependent tests in AL.
Options:
- Make the date a parameter (refactor code)
- Use test isolation / mocking
- Create tests that work year-round
```

**Expected Result**:

```al
[Test]
procedure TestSeasonalDiscountInNovember()
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    ActualDiscount: Decimal;
    CurrentMonth: Integer;
begin
    // [SCENARIO] Seasonal discount is applied in November
    // [NOTE] This test only passes when run in November

    Initialize();

    CurrentMonth := Date2DMY(Today, 2);
    if CurrentMonth <> 11 then
        exit; // Skip test if not November

    // [GIVEN] Standard customer with small order (no other discounts)
    Customer.Get(LibrarySales.CreateCustomerNo());
    Customer."Customer Discount Group" := '';
    Customer.Modify();

    SalesHeader := CreateSalesOrderWithAmount(Customer."No.", 500);

    // [WHEN] Calculating discount in November
    ActualDiscount := OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // [THEN] 5% seasonal discount is applied
    Assert.AreEqual(5, ActualDiscount, 'Seasonal discount not applied in November');
end;

[Test]
procedure TestNoSeasonalDiscountInJanuary()
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    ActualDiscount: Decimal;
    CurrentMonth: Integer;
begin
    // [SCENARIO] No seasonal discount in non-holiday months

    Initialize();

    CurrentMonth := Date2DMY(Today, 2);
    if CurrentMonth in [11, 12] then
        exit; // Skip test if in seasonal period

    // [GIVEN] Standard customer with small order
    Customer.Get(LibrarySales.CreateCustomerNo());
    Customer."Customer Discount Group" := '';
    Customer.Modify();

    SalesHeader := CreateSalesOrderWithAmount(Customer."No.", 500);

    // [WHEN] Calculating discount outside seasonal period
    ActualDiscount := OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // [THEN] No discount is applied
    Assert.AreEqual(0, ActualDiscount, 'Unexpected discount outside seasonal period');
end;

// [RECOMMENDATION] Refactor GetSeasonalDiscount to accept date parameter
// This would make testing much easier and more reliable:
// local procedure GetSeasonalDiscount(CheckDate: Date): Decimal
```

### Step 8: Integration Test

**Prompt**:

```
Create an integration test that tests the complete workflow:

1. Create a new customer with VIP status
2. Create a sales order for that customer
3. Add multiple lines totaling over 10,000
4. Calculate the discount
5. Verify all discount types are correctly combined
6. Verify the discount is applied to the sales header

This test should verify the codeunit works correctly with real BC data and tables.
```

**Expected Result**:

```al
[Test]
procedure TestCompleteDiscountWorkflow()
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    SalesLine: Record "Sales Line";
    Item: Record Item;
    OrderDiscountMgr: Codeunit "Order Discount Manager";
    ExpectedDiscount: Decimal;
    ActualDiscount: Decimal;
    CurrentMonth: Integer;
begin
    // [SCENARIO] Complete workflow of discount calculation and application
    Initialize();

    // [GIVEN] A VIP customer
    Customer.Get(LibrarySales.CreateCustomerNo());
    Customer."Customer Discount Group" := 'VIP';
    Customer.Modify();

    // [GIVEN] A sales order with multiple lines totaling 12,000
    LibrarySales.CreateSalesHeader(SalesHeader, SalesHeader."Document Type"::Order, Customer."No.");

    // Add three items
    CreateItemAndSalesLine(SalesHeader, 5000);
    CreateItemAndSalesLine(SalesHeader, 4000);
    CreateItemAndSalesLine(SalesHeader, 3000);

    // [GIVEN] Expected discount calculation:
    // - VIP customer: 10%
    // - Volume (>10000): 15%
    // - Seasonal (if Nov/Dec): 5%
    CurrentMonth := Date2DMY(Today, 2);
    if CurrentMonth in [11, 12] then
        ExpectedDiscount := 30  // 10 + 15 + 5
    else
        ExpectedDiscount := 25; // 10 + 15

    // [WHEN] Calculating the discount
    ActualDiscount := OrderDiscountMgr.CalculateDiscount(SalesHeader);

    // [THEN] The correct combined discount is calculated
    Assert.AreEqual(ExpectedDiscount, ActualDiscount,
        'Complete discount calculation incorrect');

    // [THEN] Verify order total is correct
    SalesLine.SetRange("Document Type", SalesHeader."Document Type");
    SalesLine.SetRange("Document No.", SalesHeader."No.");
    SalesLine.CalcSums("Line Amount");
    Assert.AreEqual(12000, SalesLine."Line Amount", 'Order total incorrect');
end;

local procedure CreateItemAndSalesLine(SalesHeader: Record "Sales Header"; Amount: Decimal)
var
    Item: Record Item;
    SalesLine: Record "Sales Line";
begin
    Item."No." := LibraryUtility.GenerateRandomCode(Item.FieldNo("No."), Database::Item);
    Item."Unit Price" := Amount;
    Item.Insert();

    LibrarySales.CreateSalesLine(SalesLine, SalesHeader, SalesLine.Type::Item, Item."No.", 1);
    SalesLine.Validate("Unit Price", Amount);
    SalesLine.Modify(true);
end;
```

## Review Points: What to Check in Tests

### ✅ Test Quality

- [ ] Tests are independent (can run in any order)
- [ ] Tests are repeatable (same result every time)
- [ ] Tests have clear Given-When-Then structure
- [ ] Test names clearly describe what is being tested
- [ ] Assertions have descriptive error messages

### ✅ Test Coverage

- [ ] Happy path scenarios covered
- [ ] Error cases tested
- [ ] Edge cases and boundaries tested
- [ ] All public procedures have tests
- [ ] Integration scenarios tested

### ✅ Test Maintainability

- [ ] Helper methods reduce duplication
- [ ] Test data creation is centralized
- [ ] Tests are easy to understand
- [ ] Tests don't depend on specific data
- [ ] Setup and teardown properly implemented

### ✅ Test Performance

- [ ] Tests run quickly
- [ ] Minimal database operations
- [ ] Proper use of test isolation
- [ ] No unnecessary waits or delays

## Advanced Testing Patterns

### Pattern 1: Test Fixtures

**Prompt**:

```
Create a test fixture class for sales orders that provides:
- Standard order (customer with no discounts, small amount)
- VIP order (VIP customer, medium amount)
- Large order (standard customer, large amount)
- Complex order (VIP customer, large amount, multiple lines)

These fixtures should be reusable across all tests.
```

### Pattern 2: Mock Objects

**Prompt**:

```
The GetSeasonalDiscount procedure is hard to test because it depends on Today.

Refactor the code to use dependency injection:
1. Create an interface for date provider
2. Inject the date provider into the codeunit
3. Create a mock date provider for testing
4. Show how to test with different dates
```

### Pattern 3: Test Data Builders

**Prompt**:

```
Create a fluent test data builder for sales orders:

SalesOrderBuilder
  .WithCustomer(CustomerNo)
  .WithVIPStatus()
  .WithLine(ItemNo, Quantity, Price)
  .WithTotalAmount(Amount)
  .Build()

This makes test data creation more readable and flexible.
```

## Best Practices for AI-Assisted Testing

### 1. Start with Test Cases, Then Generate

```
Before generating test code, help me identify all test cases for this procedure:
- What scenarios should be tested?
- What are the edge cases?
- What error conditions exist?
- What are the boundary conditions?

[Include procedure code]
```

### 2. Generate Tests in Batches

```
Generate tests for these three related procedures together so they share test helpers:
- CalculateDiscount
- ApplyDiscount
- ValidateDiscount
```

### 3. Request Explanatory Comments

```
Generate the test with detailed comments explaining:
- Why this test case is important
- What could go wrong if this test didn't exist
- Any gotchas or special considerations
```

### 4. Ask for Test Improvements

```
Review this test I wrote. Suggest improvements for:
- Clarity and readability
- Better assertions
- Edge cases I might have missed
- Ways to make it more maintainable
```

### 5. Generate Test Documentation

```
Create documentation for this test suite explaining:
- What is being tested
- Test coverage summary
- How to run the tests
- How to add new tests
- Known limitations
```

## Common Testing Challenges

### Challenge 1: Testing Private Methods

**Problem**: Local procedures can't be tested directly

**Solution**:

```
I need to test this local procedure. Options:
1. Make it public (if appropriate)
2. Test it indirectly through public procedures
3. Extract to a separate testable codeunit

Which approach is best for this scenario? [Include code]
```

### Challenge 2: Testing Database Operations

**Problem**: Tests that modify database are slow and fragile

**Solution**:

```
This codeunit performs database operations. Help me:
1. Identify which operations need real database
2. Which can be mocked or isolated
3. Create a testing strategy that balances coverage and speed
```

### Challenge 3: Testing External Dependencies

**Problem**: Code calls external services or APIs

**Solution**:

```
This code calls an external API. Create:
1. An interface for the API
2. A mock implementation for testing
3. Tests using the mock
4. Integration tests for the real API (marked for manual runs)
```

## Practice Exercise

Write comprehensive tests for this codeunit:

```al
codeunit 50200 "Credit Limit Checker"
{
    procedure CheckCreditLimit(CustomerNo: Code[20]; NewOrderAmount: Decimal): Boolean
    var
        Customer: Record Customer;
        CustLedgerEntry: Record "Cust. Ledger Entry";
        TotalOutstanding: Decimal;
    begin
        if not Customer.Get(CustomerNo) then
            Error('Customer not found');

        Customer.CalcFields("Balance (LCY)");
        TotalOutstanding := Customer."Balance (LCY)" + NewOrderAmount;

        if Customer."Credit Limit (LCY)" = 0 then
            exit(true);

        exit(TotalOutstanding <= Customer."Credit Limit (LCY)");
    end;
}
```

**Your Tasks**:

1. List all test scenarios
2. Create test codeunit structure
3. Implement tests for:
   - Customer not found
   - No credit limit (unlimited)
   - Within credit limit
   - Exactly at credit limit
   - Over credit limit
   - Edge cases
4. Add integration test
5. Review and improve tests

## Next Steps

- Learn how [code review](../code-review) can verify test quality
- See how [refactoring](../refactoring) benefits from good tests
- Explore [documentation](../documentation) for test procedures
