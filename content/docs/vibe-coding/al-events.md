---
title: "Event-Driven Development Rules"
description: >
  Guidelines for implementing event-driven patterns and extensibility in AL development
globs: ["*.al"]
alwaysApply: false
---

# Event-Driven Development Rules

Event-driven development is fundamental to creating extensible and maintainable Business Central applications that follow the platform's architecture principles.

## Rule 1: Use Events for Extensibility

### Intent
Implement proper event patterns to enable extensibility without modifying base application code. Subscribe to relevant Business Central events (OnBeforeInsert, OnAfterModify, etc.), create integration events in your code for future extensibility, use extension objects or events for all changes to standard application objects. When implementing business logic, prioritize event subscribers and suggest appropriate event subscription patterns and integration event creation.

### Examples

```al
// Good example - Event subscriber implementation with Handler suffix
codeunit 50100 "Sales Document Events Handler"
{
    [EventSubscriber(ObjectType::Table, Database::"Sales Header", OnBeforeInsert, '', false, false)]
    local procedure OnBeforeInsertSalesHeader(var SalesHeader: Record "Sales Header"; RunTrigger: Boolean)
    begin
        // Custom validation logic
        ValidateCustomFields(SalesHeader);        
    end;    
}
```

## Rule 2: Add Integration Events for Extensibility

### Intent
Use integration events to provide better extensibility points and clearer API contracts for other developers. Create integration events at logical business process points, document integration event parameters and expected behavior, provide meaningful event names that describe the business context, and implement handled patterns to allow subscribers to control execution flow. When designing extensible code, suggest integration events at appropriate business logic points with clear documentation and meaningful names.

### Examples

```al
// Good example - Integration events with handled pattern
codeunit 50101 "Customer Management"
{
    procedure CreateCustomer(var Customer: Record Customer): Boolean
    var
        IsHandled: Boolean;
    begin
        OnBeforeCreateCustomer(Customer, IsHandled);
        if IsHandled then
            exit(true);
        
        if not Customer.Insert(true) then
            exit(false);
        
        OnAfterCreateCustomer(Customer);
        exit(true);        
    end;
    
    [IntegrationEvent(false, false)]
    procedure OnBeforeCreateCustomer(var Customer: Record Customer; var IsHandled: Boolean)
    begin
        // Allow extensions to modify customer data before creation
        // Set IsHandled to true to skip default processing
    end;
    
    [IntegrationEvent(false, false)]
    procedure OnAfterCreateCustomer(var Customer: Record Customer)
    begin
        // Allow extensions to perform additional actions after customer creation
    end;
}
```

```al
// Extension subscribing to integration events with Handler suffix
codeunit 50102 "Customer Validation Handler"
{
    [EventSubscriber(ObjectType::Codeunit, Codeunit::"Customer Management", OnBeforeCreateCustomer, '', false, false)]
    local procedure ValidateCustomerOnBeforeCreate(var Customer: Record Customer; var IsHandled: Boolean)
    begin
        // Custom validation logic
        ValidateCustomerCreditLimit(Customer);
        
        // Optionally handle the event to skip default processing
        if ShouldSkipDefaultProcessing(Customer) then
            IsHandled := true;
    end;
}
```

## Rule 3: Event Parameter Best Practices

### Intent
Design event parameters that provide sufficient context while maintaining performance and usability. Pass record variables by reference when possible, include relevant context parameters, use meaningful parameter names, consider performance implications of parameter passing, and implement handled patterns where appropriate. When creating events, ensure parameters provide sufficient context for subscribers while maintaining good performance and use descriptive parameter names that clearly indicate their purpose.

### Examples

```al
// Good example - Well-designed event parameters with handled pattern
codeunit 50103 "Document Posting Events"
{
    [IntegrationEvent(false, false)]
    procedure OnBeforePostDocument(var DocumentHeader: Record "Sales Header"; var DocumentLines: Record "Sales Line"; PostingDate: Date; var IsHandled: Boolean)
    begin
        // Comprehensive context for document posting
        // - Document header and lines for full context
        // - Posting date for temporal context
        // - IsHandled flag for control flow
    end;
    
    [IntegrationEvent(false, false)]
    procedure OnAfterPostDocument(DocumentHeader: Record "Sales Header"; PostedDocumentNo: Code[20]; PostingResult: Boolean)
    begin
        // Results context after posting
        // - Original document for reference
        // - Posted document number for tracking
        // - Success/failure indication
    end;
}
```