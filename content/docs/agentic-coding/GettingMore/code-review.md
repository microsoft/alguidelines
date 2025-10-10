---
title: "AI-Assisted Code Review"
linkTitle: "Code Review"
weight: 1
description: >
  Learn how to use AI assistants to review AL code for quality, performance, and best practices
---

## Scenario

You've just finished implementing a new feature: a codeunit that processes sales orders and applies volume-based discounts. Before submitting for peer review, you want to use AI to catch potential issues.

## Goal

Use AI to identify:
- Potential bugs or logical errors
- Performance issues
- AL best practice violations
- Missing error handling
- Code quality improvements

## The Code to Review

Here's the codeunit we'll review:

```al
codeunit 50100 "Sales Order Discount Processor"
{
    procedure ApplyVolumeDiscounts(var SalesHeader: Record "Sales Header")
    var
        SalesLine: Record "Sales Line";
        TotalQuantity: Decimal;
        DiscountPct: Decimal;
    begin
        TotalQuantity := 0;
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        if SalesLine.FindSet() then
            repeat
                TotalQuantity := TotalQuantity + SalesLine.Quantity;
            until SalesLine.Next() = 0;

        if TotalQuantity > 100 then
            DiscountPct := 15
        else if TotalQuantity > 50 then
            DiscountPct := 10
        else if TotalQuantity > 10 then
            DiscountPct := 5;

        SalesLine.Reset();
        if SalesLine.FindSet() then
            repeat
                SalesLine."Line Discount %" := DiscountPct;
                SalesLine.Modify();
            until SalesLine.Next() = 0;
    end;
}
```

## Step-by-Step Review Process

### Step 1: Initial Quality Review

**Prompt**:
```
Review this AL codeunit for potential bugs, code quality issues, and best practice violations.

[Paste the code above]

Specifically check for:
- Logical errors
- Missing error handling
- Variable declarations
- Commit/transaction issues
- Performance concerns
```

**Expected AI Findings**:
The AI should identify issues like:
- Missing `FindSet(true, false)` parameters for modification
- No error handling
- Multiple FindSet operations (inefficient)
- Hard-coded discount percentages
- No validation of input parameters
- Missing ModifyAll opportunity
- No telemetry or logging

### Step 2: Performance Analysis

**Prompt**:
```
Analyze the performance of this code, especially database operations.
What are potential performance issues? How can it be optimized?

[Include the code or reference it if in context]
```

**Expected AI Suggestions**:
- Combine the two FindSet loops into one
- Use ModifyAll instead of looping for discount application
- Consider using SetLoadFields for better performance
- Cache discount percentages instead of hard-coding

### Step 3: Best Practices Check

**Prompt**:
```
Check this code against AL coding best practices and Business Central development standards.
What improvements would you recommend?
```

**Expected AI Recommendations**:
- Add XML documentation
- Extract discount calculation to separate function
- Use constants for discount tiers
- Add error handling
- Implement proper transaction handling
- Add telemetry for monitoring

### Step 4: Request Improved Version

**Prompt**:
```
Rewrite this codeunit implementing all the improvements you suggested.
Include:
- XML documentation
- Error handling
- Performance optimizations
- Best practice compliance
- Telemetry/logging
- Discount tiers from setup table instead of hard-coded values
```

**Review the Generated Code**:
The AI should generate something like:

```al
/// <summary>
/// Processes sales orders and applies volume-based discounts
/// </summary>
codeunit 50100 "Sales Order Discount Processor"
{
    /// <summary>
    /// Applies volume-based discounts to a sales order
    /// </summary>
    /// <param name="SalesHeader">The sales header to process</param>
    procedure ApplyVolumeDiscounts(var SalesHeader: Record "Sales Header")
    var
        SalesLine: Record "Sales Line";
        TotalQuantity: Decimal;
        DiscountPct: Decimal;
    begin
        if SalesHeader."No." = '' then
            Error('Sales order number cannot be empty.');

        TotalQuantity := CalculateTotalQuantity(SalesHeader);
        DiscountPct := GetDiscountPercentage(TotalQuantity);

        if DiscountPct > 0 then
            ApplyDiscountToLines(SalesHeader, DiscountPct);

        LogTelemetry(SalesHeader."No.", TotalQuantity, DiscountPct);
    end;

    local procedure CalculateTotalQuantity(SalesHeader: Record "Sales Header"): Decimal
    var
        SalesLine: Record "Sales Line";
        TotalQty: Decimal;
    begin
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.SetLoadFields(Quantity);
        
        if SalesLine.FindSet() then
            repeat
                TotalQty += SalesLine.Quantity;
            until SalesLine.Next() = 0;

        exit(TotalQty);
    end;

    local procedure GetDiscountPercentage(TotalQuantity: Decimal): Decimal
    var
        VolumeDiscountSetup: Record "Volume Discount Setup"; // Assuming this table exists
    begin
        VolumeDiscountSetup.SetFilter("Minimum Quantity", '<=%1', TotalQuantity);
        VolumeDiscountSetup.SetCurrentKey("Minimum Quantity");
        VolumeDiscountSetup.Ascending(false);
        
        if VolumeDiscountSetup.FindFirst() then
            exit(VolumeDiscountSetup."Discount Percentage");

        exit(0);
    end;

    local procedure ApplyDiscountToLines(SalesHeader: Record "Sales Header"; DiscountPct: Decimal)
    var
        SalesLine: Record "Sales Line";
    begin
        SalesLine.SetRange("Document Type", SalesHeader."Document Type");
        SalesLine.SetRange("Document No.", SalesHeader."No.");
        SalesLine.ModifyAll("Line Discount %", DiscountPct, true);
    end;

    local procedure LogTelemetry(DocumentNo: Code[20]; TotalQty: Decimal; DiscountPct: Decimal)
    var
        Telemetry: Codeunit Telemetry;
        CustomDimensions: Dictionary of [Text, Text];
    begin
        CustomDimensions.Add('DocumentNo', DocumentNo);
        CustomDimensions.Add('TotalQuantity', Format(TotalQty));
        CustomDimensions.Add('DiscountPercentage', Format(DiscountPct));
        
        Telemetry.LogMessage('0001', 'Volume discount applied', Verbosity::Normal, 
            DataClassification::SystemMetadata, TelemetryScope::All, CustomDimensions);
    end;
}
```

## Review Points: What to Check

After AI generates the improved code, verify:

### ✅ Correctness
- [ ] Logic matches business requirements
- [ ] All edge cases handled
- [ ] No regression in functionality
- [ ] Error messages are clear

### ✅ Performance
- [ ] Efficient database queries
- [ ] Proper use of SetLoadFields
- [ ] ModifyAll used where appropriate
- [ ] No unnecessary loops

### ✅ Best Practices
- [ ] XML documentation complete
- [ ] Proper error handling
- [ ] Appropriate transaction handling
- [ ] Good function decomposition

### ✅ AL Specifics
- [ ] Correct AL syntax
- [ ] Proper use of BC APIs
- [ ] No deprecated features
- [ ] Appropriate data types

### ✅ Maintainability
- [ ] Clear, descriptive names
- [ ] Logical organization
- [ ] Easy to test
- [ ] Well documented

## Additional Review Prompts

### Security Review
```
Review this code for potential security issues:
- Input validation
- Authorization checks
- Data access controls
- Potential injection vulnerabilities
```

### Testability Review
```
Analyze this code for testability. 
What makes it easy or hard to test?
How can it be restructured to be more testable?
```

### Documentation Review
```
Review the XML documentation for this code.
Is it complete? Clear? Helpful?
What additional documentation would be valuable?
```

## Common Issues AI Might Miss

Be aware AI might not catch:

1. **Business Logic Errors**
   - AI doesn't know your specific discount rules
   - Verify the logic matches actual requirements

2. **Integration Issues**
   - AI doesn't know about other extensions
   - Check for conflicts with existing code

3. **BC Version Compatibility**
   - AI might use features not in your BC version
   - Verify all APIs are available

4. **Organization Standards**
   - AI doesn't know your specific standards
   - Adapt to match your conventions

## Best Practices for AI Code Review

### 1. Use Specific Review Criteria
Instead of "review this code", specify what to look for:
```
Review for: performance, error handling, AL best practices, testability
```

### 2. Review in Layers
Don't try to review everything at once:
- First: Correctness and logic
- Second: Performance
- Third: Best practices
- Fourth: Documentation

### 3. Combine with Tools
Use AI review alongside:
- AL code analyzers
- Static analysis tools
- Peer review
- Testing

### 4. Iterate
Review, improve, review again:
```
Review the updated code. Are there any remaining issues?
```

### 5. Document Findings
Keep track of:
- Common issues AI finds
- Issues AI misses
- Effective review prompts

## Practice Exercise

Try reviewing this code with AI:

```al
procedure CalculateShippingCost(SalesHeader: Record "Sales Header"): Decimal
var
    SalesLine: Record "Sales Line";
    Weight: Decimal;
begin
    Weight := 0;
    SalesLine.SetRange("Document No.", SalesHeader."No.");
    if SalesLine.Find('-') then
        repeat
            Weight := Weight + SalesLine.Quantity;
        until SalesLine.Next() = 0;
    
    if Weight < 10 then
        exit(5.00)
    else
        exit(10.00);
end;
```

**Your Tasks**:
1. Ask AI to review for issues
2. Request performance improvements
3. Ask for best practice compliance
4. Get an improved version
5. Review the improved version yourself

## Next Steps

- Learn how to use AI for [generating documentation](../documentation)
- See how AI can help with [adding telemetry](../telemetry)
- Explore [refactoring legacy code](../refactoring) with AI assistance
