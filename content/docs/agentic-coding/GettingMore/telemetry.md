---
title: "Adding Telemetry"
linkTitle: "Telemetry"
weight: 3
description: >
  Use AI to instrument your AL extensions with Application Insights telemetry
---

## Scenario

You have a Business Central extension that's running in production, but you have limited visibility into:
- How often features are used
- Where errors occur
- Performance bottlenecks
- User behavior patterns

You want to add comprehensive telemetry using Application Insights to monitor your extension in production.

## Goal

Use AI to help you:
- Add telemetry calls throughout your code
- Log important events and errors
- Track feature usage
- Monitor performance
- Create custom dimensions for better analysis

## Prerequisites

Understanding of:
- Business Central telemetry concepts
- Application Insights basics
- When to log (without over-logging)
- Data classification requirements

## Step-by-Step Guide

### Step 1: Understanding Your Code

**Starting Code** (without telemetry):
```al
codeunit 50100 "Sales Order Processor"
{
    procedure ProcessOrder(var SalesHeader: Record "Sales Header"): Boolean
    var
        SalesLine: Record "Sales Line";
    begin
        if not ValidateOrder(SalesHeader) then
            exit(false);

        CalculateTotals(SalesHeader);
        ApplyDiscounts(SalesHeader);
        
        if not PostOrder(SalesHeader) then
            exit(false);

        exit(true);
    end;

    local procedure ValidateOrder(var SalesHeader: Record "Sales Header"): Boolean
    var
        Customer: Record Customer;
    begin
        if not Customer.Get(SalesHeader."Sell-to Customer No.") then begin
            Error('Customer %1 does not exist.', SalesHeader."Sell-to Customer No.");
        end;

        if Customer.Blocked <> Customer.Blocked::" " then begin
            Error('Customer %1 is blocked.', Customer."No.");
        end;

        exit(true);
    end;

    local procedure PostOrder(var SalesHeader: Record "Sales Header"): Boolean
    var
        SalesPost: Codeunit "Sales-Post";
    begin
        exit(SalesPost.Run(SalesHeader));
    end;
}
```

### Step 2: Request Telemetry Strategy

**Prompt**:
```
I want to add Application Insights telemetry to this Business Central codeunit.

Help me plan what to log:
- What events should be logged?
- What custom dimensions should be included?
- What data classification is appropriate?
- Where should we log errors vs. information?

[Paste the code above]
```

**Expected AI Response**:
The AI should suggest logging:

**Events to Log:**
1. Order processing started (Informational)
2. Order validation completed (Informational)
3. Order posted successfully (Informational)
4. Validation failures (Warning)
5. Posting failures (Error)
6. Performance metrics (Informational)

**Custom Dimensions:**
- Order Number
- Customer Number
- Order Amount
- Number of Lines
- Processing Duration
- Error Details (when applicable)

**Data Classification:**
- Order Number: CustomerContent
- Customer Number: CustomerContent
- Amounts: CustomerContent
- Counts: SystemMetadata
- Duration: SystemMetadata

### Step 3: Add Telemetry to Main Procedure

**Prompt**:
```
Add Application Insights telemetry to the ProcessOrder procedure.

Include:
- Start event when processing begins
- Success event when complete
- Error event on failure
- Custom dimensions: OrderNo, CustomerNo, TotalAmount, LineCount, Duration
- Use appropriate data classification
- Use the Telemetry codeunit from System Application

Update this code:
[Paste ProcessOrder procedure]
```

**Expected Result**:
```al
procedure ProcessOrder(var SalesHeader: Record "Sales Header"): Boolean
var
    Telemetry: Codeunit Telemetry;
    CustomDimensions: Dictionary of [Text, Text];
    StartTime: DateTime;
    Duration: Duration;
    LineCount: Integer;
begin
    StartTime := CurrentDateTime;
    
    // Log start of processing
    AddOrderDimensions(CustomDimensions, SalesHeader);
    Telemetry.LogMessage('SALESORD-001', 'Order processing started', 
        Verbosity::Normal, DataClassification::SystemMetadata, 
        TelemetryScope::ExtensionPublisher, CustomDimensions);

    if not ValidateOrder(SalesHeader) then begin
        LogProcessingError(SalesHeader, 'Validation failed');
        exit(false);
    end;

    CalculateTotals(SalesHeader);
    ApplyDiscounts(SalesHeader);
    
    if not PostOrder(SalesHeader) then begin
        LogProcessingError(SalesHeader, 'Posting failed');
        exit(false);
    end;

    // Log successful completion
    Duration := CurrentDateTime - StartTime;
    Clear(CustomDimensions);
    AddOrderDimensions(CustomDimensions, SalesHeader);
    CustomDimensions.Add('Duration', Format(Duration));
    CustomDimensions.Add('LineCount', Format(CountLines(SalesHeader)));
    
    Telemetry.LogMessage('SALESORD-002', 'Order processing completed successfully', 
        Verbosity::Normal, DataClassification::SystemMetadata, 
        TelemetryScope::ExtensionPublisher, CustomDimensions);

    exit(true);
end;

local procedure AddOrderDimensions(var CustomDimensions: Dictionary of [Text, Text]; SalesHeader: Record "Sales Header")
begin
    CustomDimensions.Add('OrderNo', SalesHeader."No.");
    CustomDimensions.Add('CustomerNo', SalesHeader."Sell-to Customer No.");
    CustomDimensions.Add('OrderType', Format(SalesHeader."Document Type"));
end;

local procedure LogProcessingError(SalesHeader: Record "Sales Header"; ErrorMessage: Text)
var
    Telemetry: Codeunit Telemetry;
    CustomDimensions: Dictionary of [Text, Text];
begin
    AddOrderDimensions(CustomDimensions, SalesHeader);
    CustomDimensions.Add('ErrorMessage', ErrorMessage);
    CustomDimensions.Add('ErrorCallStack', GetLastErrorCallStack());
    
    Telemetry.LogMessage('SALESORD-E001', StrSubstNo('Order processing failed: %1', ErrorMessage), 
        Verbosity::Error, DataClassification::SystemMetadata, 
        TelemetryScope::ExtensionPublisher, CustomDimensions);
end;

local procedure CountLines(SalesHeader: Record "Sales Header"): Integer
var
    SalesLine: Record "Sales Line";
begin
    SalesLine.SetRange("Document Type", SalesHeader."Document Type");
    SalesLine.SetRange("Document No.", SalesHeader."No.");
    exit(SalesLine.Count);
end;
```

### Step 4: Add Error Tracking

**Prompt**:
```
Update the ValidateOrder procedure to log validation failures with telemetry.

For each validation check that fails:
- Log a warning message
- Include relevant details (customer number, block reason, etc.)
- Use appropriate custom dimensions
- Use proper data classification

[Paste ValidateOrder procedure]
```

**Expected Result**:
```al
local procedure ValidateOrder(var SalesHeader: Record "Sales Header"): Boolean
var
    Customer: Record Customer;
    Telemetry: Codeunit Telemetry;
    CustomDimensions: Dictionary of [Text, Text];
begin
    if not Customer.Get(SalesHeader."Sell-to Customer No.") then begin
        AddValidationDimensions(CustomDimensions, SalesHeader, 'Customer not found');
        Telemetry.LogMessage('SALESORD-W001', 'Order validation failed: Customer not found', 
            Verbosity::Warning, DataClassification::SystemMetadata, 
            TelemetryScope::ExtensionPublisher, CustomDimensions);
        
        Error('Customer %1 does not exist.', SalesHeader."Sell-to Customer No.");
    end;

    if Customer.Blocked <> Customer.Blocked::" " then begin
        Clear(CustomDimensions);
        AddValidationDimensions(CustomDimensions, SalesHeader, 'Customer blocked');
        CustomDimensions.Add('BlockedReason', Format(Customer.Blocked));
        
        Telemetry.LogMessage('SALESORD-W002', 'Order validation failed: Customer blocked', 
            Verbosity::Warning, DataClassification::SystemMetadata, 
            TelemetryScope::ExtensionPublisher, CustomDimensions);
        
        Error('Customer %1 is blocked.', Customer."No.");
    end;

    exit(true);
end;

local procedure AddValidationDimensions(var CustomDimensions: Dictionary of [Text, Text]; SalesHeader: Record "Sales Header"; Reason: Text)
begin
    CustomDimensions.Add('OrderNo', SalesHeader."No.");
    CustomDimensions.Add('CustomerNo', SalesHeader."Sell-to Customer No.");
    CustomDimensions.Add('ValidationFailure', Reason);
end;
```

### Step 5: Add Performance Tracking

**Prompt**:
```
Add performance telemetry to track how long different operations take.

Track duration for:
- Overall processing
- Validation
- Calculation
- Posting

Log if any operation takes longer than expected thresholds:
- Validation > 1 second
- Calculation > 2 seconds
- Posting > 5 seconds

[Include the codeunit]
```

**Expected Result**:
```al
local procedure ValidateOrder(var SalesHeader: Record "Sales Header"): Boolean
var
    Customer: Record Customer;
    Telemetry: Codeunit Telemetry;
    CustomDimensions: Dictionary of [Text, Text];
    StartTime: DateTime;
    Duration: Duration;
begin
    StartTime := CurrentDateTime;
    
    // Validation logic here...
    
    Duration := CurrentDateTime - StartTime;
    
    // Log if validation took too long
    if Duration > 1000 then begin
        AddOrderDimensions(CustomDimensions, SalesHeader);
        CustomDimensions.Add('Duration', Format(Duration));
        CustomDimensions.Add('Operation', 'Validation');
        
        Telemetry.LogMessage('SALESORD-P001', 'Slow validation detected', 
            Verbosity::Warning, DataClassification::SystemMetadata, 
            TelemetryScope::ExtensionPublisher, CustomDimensions);
    end;

    exit(true);
end;
```

### Step 6: Add Feature Usage Tracking

**Prompt**:
```
Add telemetry to track feature usage for business analytics.

Track:
- How often each discount type is applied
- Which payment methods are used
- Popular shipping methods
- Order size distribution

Create a FeatureUsageTelemetry codeunit for this purpose.
```

**Expected Result**:
```al
codeunit 50101 "Feature Usage Telemetry"
{
    var
        Telemetry: Codeunit Telemetry;

    procedure LogDiscountApplied(DiscountType: Text; DiscountPct: Decimal; OrderNo: Code[20])
    var
        CustomDimensions: Dictionary of [Text, Text];
    begin
        CustomDimensions.Add('FeatureArea', 'Discounts');
        CustomDimensions.Add('DiscountType', DiscountType);
        CustomDimensions.Add('DiscountPercentage', Format(DiscountPct));
        CustomDimensions.Add('OrderNo', OrderNo);
        
        Telemetry.LogMessage('FEATURE-DISCOUNT', 'Discount applied', 
            Verbosity::Normal, DataClassification::SystemMetadata, 
            TelemetryScope::ExtensionPublisher, CustomDimensions);
    end;

    procedure LogPaymentMethod(PaymentMethodCode: Code[10]; Amount: Decimal)
    var
        CustomDimensions: Dictionary of [Text, Text];
    begin
        CustomDimensions.Add('FeatureArea', 'Payment');
        CustomDimensions.Add('PaymentMethod', PaymentMethodCode);
        CustomDimensions.Add('AmountRange', GetAmountRange(Amount));
        
        Telemetry.LogMessage('FEATURE-PAYMENT', 'Payment method used', 
            Verbosity::Normal, DataClassification::SystemMetadata, 
            TelemetryScope::ExtensionPublisher, CustomDimensions);
    end;

    local procedure GetAmountRange(Amount: Decimal): Text
    begin
        case true of
            Amount < 100:
                exit('0-100');
            Amount < 1000:
                exit('100-1000');
            Amount < 10000:
                exit('1000-10000');
            else
                exit('10000+');
        end;
    end;
}
```

## Telemetry Best Practices

### ✅ Do's

**Use Consistent Event IDs**
```
SALESORD-001: Processing started
SALESORD-002: Processing completed
SALESORD-E001: Processing error
SALESORD-W001: Validation warning
SALESORD-P001: Performance warning
```

**Use Meaningful Messages**
```al
// Good
Telemetry.LogMessage('SALESORD-001', 'Sales order processing started for large order', ...);

// Bad
Telemetry.LogMessage('001', 'Started', ...);
```

**Include Helpful Custom Dimensions**
```al
CustomDimensions.Add('OrderNo', OrderNo);
CustomDimensions.Add('CustomerNo', CustomerNo);
CustomDimensions.Add('LineCount', Format(LineCount));
CustomDimensions.Add('TotalAmount', Format(TotalAmount));
CustomDimensions.Add('ProcessingDuration', Format(Duration));
```

**Use Appropriate Data Classification**
```al
// Customer data
DataClassification::CustomerContent

// System metrics
DataClassification::SystemMetadata

// Organizational data
DataClassification::OrganizationIdentifiableInformation
```

### ❌ Don'ts

**Don't Over-Log**
```al
// Too much logging
Telemetry.LogMessage('001', 'Line 1', ...);
Telemetry.LogMessage('002', 'Line 2', ...);
// Don't log every single step
```

**Don't Log Sensitive Data**
```al
// Bad - logging password
CustomDimensions.Add('Password', Password);

// Bad - logging full credit card
CustomDimensions.Add('CreditCard', CreditCardNo);

// Bad - logging personal data unnecessarily
CustomDimensions.Add('SSN', SSN);
```

**Don't Use Generic Messages**
```al
// Bad
Telemetry.LogMessage('001', 'Error', ...);

// Good
Telemetry.LogMessage('SALESORD-E001', 'Failed to post sales order due to credit limit exceeded', ...);
```

## Advanced Telemetry Patterns

### Pattern 1: Telemetry Wrapper
**Prompt**:
```
Create a telemetry wrapper codeunit that simplifies logging for my extension.
Include helper methods for:
- LogStart/LogEnd (with automatic duration calculation)
- LogError (with automatic call stack)
- LogFeatureUsage
- LogPerformanceWarning

Prefix all event IDs with my extension prefix "SALESEXT-"
```

### Pattern 2: Telemetry Constants
**Prompt**:
```
Create a codeunit with constants for all my telemetry event IDs and messages.
This will ensure consistency and make it easier to update messages.
```

### Pattern 3: Contextual Telemetry
**Prompt**:
```
Create a system that automatically adds common context to all telemetry:
- User ID (anonymized)
- Company name (anonymized)
- Extension version
- BC version
- Environment type (Production/Sandbox)

Make this context added automatically to all telemetry calls.
```

## Monitoring and Analysis

### Creating Useful Queries

**Prompt**:
```
Create KQL (Kusto Query Language) queries for Application Insights to analyze:

1. Most common errors in the last 24 hours
2. Average processing duration by order size
3. Feature usage statistics
4. Performance trends over time
5. Error rate percentage

Base the queries on the telemetry we added to the Sales Order Processor codeunit.
```

**Expected Result**:
```kql
// Most common errors in last 24 hours
traces
| where timestamp > ago(24h)
| where message startswith "SALESORD-E"
| summarize ErrorCount = count() by message, customDimensions.ErrorMessage
| order by ErrorCount desc
| take 10

// Average processing duration by line count
traces
| where message == "SALESORD-002"
| extend LineCount = toint(customDimensions.LineCount)
| extend Duration = toint(customDimensions.Duration)
| summarize AvgDuration = avg(Duration) by LineCountBucket = bin(LineCount, 5)
| order by LineCountBucket asc

// Feature usage - discount types
traces
| where message == "FEATURE-DISCOUNT"
| summarize UsageCount = count() by DiscountType = tostring(customDimensions.DiscountType)
| order by UsageCount desc

// Performance warnings
traces
| where message startswith "SALESORD-P"
| summarize WarningCount = count() by Operation = tostring(customDimensions.Operation), bin(timestamp, 1h)
| render timechart

// Error rate percentage
let totalOrders = toscalar(
    traces
    | where message == "SALESORD-001"
    | where timestamp > ago(24h)
    | count
);
let failedOrders = toscalar(
    traces
    | where message startswith "SALESORD-E"
    | where timestamp > ago(24h)
    | count
);
print ErrorRate = (todouble(failedOrders) / todouble(totalOrders)) * 100
```

## Practice Exercise

Add comprehensive telemetry to this code:

```al
codeunit 50200 "Inventory Adjustment Handler"
{
    procedure AdjustInventory(ItemNo: Code[20]; Quantity: Decimal; ReasonCode: Code[10])
    var
        Item: Record Item;
        ItemJnlLine: Record "Item Journal Line";
    begin
        Item.Get(ItemNo);
        
        ItemJnlLine.Init();
        ItemJnlLine."Item No." := ItemNo;
        ItemJnlLine.Quantity := Quantity;
        ItemJnlLine."Reason Code" := ReasonCode;
        ItemJnlLine.Insert(true);
        
        CODEUNIT.Run(CODEUNIT::"Item Jnl.-Post Line", ItemJnlLine);
    end;
}
```

**Your Tasks**:
1. Add start/end telemetry
2. Add error handling and logging
3. Track performance
4. Log feature usage
5. Add appropriate custom dimensions
6. Create KQL queries for analysis

## Next Steps

- Learn about [refactoring legacy code](../refactoring) while adding telemetry
- See how [code review](../code-review) can catch telemetry issues
- Explore [testing strategies](../testing) for telemetry code
