---
title: "GitHub Copilot Agent"
linkTitle: "GitHub Copilot"
weight: 1
description: >
  Microsoft's AI pair programmer for real-time code completion and suggestions
---

## Overview

GitHub Copilot is Microsoft's AI-powered coding assistant that provides real-time code suggestions directly in your editor. It's trained on billions of lines of public code and integrates seamlessly with Visual Studio Code.

**Developer**: GitHub (Microsoft)  
**Type**: AI Code Completion Agent  
**Primary Use**: Inline code suggestions as you type  
**Integration**: Native VS Code extension

## What is GitHub Copilot?

GitHub Copilot acts as an AI pair programmer that:
- Suggests code completions in real-time
- Generates entire functions from comments
- Provides multiple suggestion alternatives
- Understands context from your codebase
- Learns patterns specific to AL and Business Central

### How It Works

1. **You write**: A comment or start typing code
2. **Copilot analyzes**: Your code, open files, and context
3. **Copilot suggests**: Code completion in gray text
4. **You decide**: Accept (Tab), reject (Esc), or modify

**Example**:
```al
// Type this comment:
/// Calculate total sales for customer including tax

// Copilot suggests (press Tab to accept):
procedure CalculateTotalSalesWithTax(CustomerNo: Code[20]): Decimal
var
    SalesLine: Record "Sales Line";
    TotalAmount: Decimal;
begin
    SalesLine.SetRange("Sell-to Customer No.", CustomerNo);
    SalesLine.SetRange(Type, SalesLine.Type::Item);
    if SalesLine.FindSet() then
        repeat
            TotalAmount += SalesLine."Amount Including VAT";
        until SalesLine.Next() = 0;
    exit(TotalAmount);
end;
```

## Key Capabilities

### For AL Development

**Understands AL Syntax**:
- Recognizes AL keywords and patterns
- Knows Business Central object types
- Suggests BC-appropriate code structures
- Follows AL naming conventions

**Business Central Awareness**:
- Familiar with BC table structures
- Knows common BC APIs
- Suggests standard BC patterns
- Understands event subscriber patterns

**Context-Aware**:
- Reads your open AL files
- Understands project structure
- References your existing code
- Adapts to your coding style

### Code Generation Capabilities

**From Comments**:
```al
/// Create a page extension for Customer Card that adds loyalty points field

// Generates complete page extension structure
```

**From Partial Code**:
```al
procedure ValidateCustomer
// Continue typing... Copilot completes with parameters, logic
```

**From Patterns**:
```al
[EventSubscriber(
// Copilot suggests common event patterns
```

**Test Code**:
```al
[Test]
procedure TestCustomerValidation
// Generates test structure with Given-When-Then
```

## Strengths for AL Development

### ✓ Excellent At

**Boilerplate Code**:
- Table and field definitions
- Page layouts
- Standard procedures
- Variable declarations

**Common Patterns**:
- CRUD operations
- Validation logic
- Event subscribers
- API pages

**Code Structure**:
- Procedure signatures
- If-then-else logic
- Loop structures
- Error handling templates

**Quick Edits**:
- Adding fields
- Extending objects
- Creating similar code
- Repetitive tasks

### ⚠ Use With Care

**Complex Business Logic**:
- May not understand specific requirements
- Review carefully for correctness
- Validate against business rules

**Performance-Critical Code**:
- Check for efficient database queries
- Verify optimal AL patterns
- Profile if needed

**Security-Sensitive Code**:
- Review authentication logic
- Validate input handling
- Check authorization patterns

## How It Differs from Other Agents

### vs. GitHub Copilot Chat
**Copilot (Inline)**:
- ✓ Better for code completion
- ✓ Faster for quick edits
- ✓ Non-intrusive workflow
- ✗ Limited explanation ability
- ✗ Can't answer questions

**Copilot Chat**:
- ✓ Better for explanations
- ✓ Can answer questions
- ✓ Helps with debugging
- ✗ Slower for quick completions
- ✗ More context switching

**Best Practice**: Use both together

### vs. Claude
**GitHub Copilot**:
- ✓ Faster inline suggestions
- ✓ Better VS Code integration
- ✓ No context switching
- ✗ Smaller context window
- ✗ No deep analysis

**Claude**:
- ✓ Larger context window
- ✓ Better for complex analysis
- ✓ Strong reasoning
- ✗ No inline completions
- ✗ Manual copy-paste workflow

**Best Practice**: Copilot for coding, Claude for analysis

### vs. Cursor
**GitHub Copilot**:
- ✓ Works in standard VS Code
- ✓ Familiar environment
- ✓ Just code completion
- ✗ Single AI model
- ✗ Limited multi-file edits

**Cursor**:
- ✓ Multiple AI models
- ✓ Advanced features (Composer)
- ✓ Better multi-file editing
- ✗ Different editor
- ✗ Learning curve

**Best Practice**: Copilot for standard workflow, Cursor for advanced needs

## Practical AL Examples

### Creating Tables

**Input** (comment):
```al
/// Customer loyalty tier table with tier name, min points, and discount percentage
```

**Copilot Generates**:
```al
table 50100 "Customer Loyalty Tier"
{
    DataClassification = CustomerContent;
    
    fields
    {
        field(1; "Code"; Code[20])
        {
            Caption = 'Code';
        }
        field(2; "Tier Name"; Text[50])
        {
            Caption = 'Tier Name';
        }
        field(10; "Minimum Points"; Integer)
        {
            Caption = 'Minimum Points';
            MinValue = 0;
        }
        field(20; "Discount Percentage"; Decimal)
        {
            Caption = 'Discount Percentage';
            MinValue = 0;
            MaxValue = 100;
            DecimalPlaces = 0 : 2;
        }
    }
    
    keys
    {
        key(PK; "Code")
        {
            Clustered = true;
        }
        key(Points; "Minimum Points")
        {
        }
    }
}
```

### Event Subscribers

**Input** (partial code):
```al
[EventSubscriber(ObjectType::Table, Database::Customer, 'OnAfterValidateEvent', 'Credit Limit (LCY)', false, false)]
```

**Copilot Completes**:
```al
local procedure OnAfterValidateCreditLimit(var Rec: Record Customer; var xRec: Record Customer; CurrFieldNo: Integer)
begin
    // Add your implementation
    if Rec."Credit Limit (LCY)" > 100000 then
        if not Confirm('Credit limit exceeds 100,000. Continue?') then
            Error('');
end;
```

### API Pages

**Input**:
```al
/// API v2.0 page for items exposing no, description, unit price, inventory
```

**Copilot Generates**:
```al
page 50100 "Item API"
{
    APIVersion = 'v2.0';
    APIPublisher = 'yourcompany';
    APIGroup = 'inventory';
    EntityCaption = 'Item';
    EntitySetCaption = 'Items';
    PageType = API;
    SourceTable = Item;
    DelayedInsert = true;
    ODataKeyFields = SystemId;

    layout
    {
        area(Content)
        {
            repeater(GroupName)
            {
                field(id; Rec.SystemId)
                {
                    Caption = 'Id';
                    Editable = false;
                }
                field(number; Rec."No.")
                {
                    Caption = 'No.';
                }
                field(description; Rec.Description)
                {
                    Caption = 'Description';
                }
                field(unitPrice; Rec."Unit Price")
                {
                    Caption = 'Unit Price';
                }
                field(inventory; Rec.Inventory)
                {
                    Caption = 'Inventory';
                }
            }
        }
    }
}
```

### Test Code

**Input**:
```al
[Test]
procedure TestCustomerCreditLimitValidation
```

**Copilot Suggests**:
```al
[Test]
procedure TestCustomerCreditLimitValidation()
var
    Customer: Record Customer;
    LibrarySales: Codeunit "Library - Sales";
begin
    // [GIVEN] A customer with credit limit set
    Customer.Init();
    Customer."No." := LibrarySales.CreateCustomerNo();
    Customer."Credit Limit (LCY)" := 50000;
    Customer.Insert();

    // [WHEN] Credit limit is exceeded
    Customer.Validate("Credit Limit (LCY)", 150000);

    // [THEN] Appropriate validation occurs
    // Add assertions here
end;
```

## Setup & Configuration

### Installation

See the detailed [GitHub Copilot Tool Page](../Tools/github-copilot) for complete installation instructions.

**Quick Start**:
1. Install GitHub Copilot extension in VS Code
2. Sign in with GitHub account
3. Verify subscription is active
4. Start coding in AL files

### Optimizing for AL

**VS Code Settings**:
```json
{
  "github.copilot.enable": {
    "*": true,
    "al": true
  },
  "editor.inlineSuggest.enabled": true,
  "editor.quickSuggestions": {
    "other": true,
    "comments": true,
    "strings": true
  }
}
```

**Project Setup**:
- Keep `app.json` well-configured
- Download BC symbols
- Use descriptive file names
- Maintain good code organization

## Best Practices

### Getting Quality Suggestions

**Write Clear Comments**:
```al
❌ // calc total
✅ /// Calculate the total sales amount for a customer including tax and discounts
```

**Use Meaningful Names**:
```al
❌ procedure Calc(x: Code[20]): Decimal
✅ procedure CalculateCustomerTotalSales(CustomerNo: Code[20]): Decimal
```

**Provide Context**:
- Keep related files open
- Use consistent naming
- Follow AL conventions
- Add XML documentation

### Review Checklist

Before accepting Copilot suggestions:

- [ ] Does it match my requirements?
- [ ] Is the AL syntax correct?
- [ ] Are BC APIs used properly?
- [ ] Is it performant?
- [ ] Does it follow best practices?
- [ ] Is error handling appropriate?
- [ ] Are data types correct?

### Workflow Integration

**Effective Use**:
1. Write descriptive comment or start typing
2. Review Copilot's suggestion
3. Accept if good, modify if needed
4. Test the generated code
5. Refine as necessary

**Don't**:
- Blindly accept every suggestion
- Skip testing generated code
- Ignore code analysis warnings
- Use without understanding

## Pricing

**Individual**:
- $10/month or $100/year
- Free for verified students
- Free for open source maintainers

**Business**:
- $19/user/month
- Organization management
- Policy controls
- Usage insights

**Free Trial**: Usually 30 days available

**Link**: [GitHub Copilot Pricing](https://github.com/features/copilot)

## Privacy & Security

### What Gets Sent
- Code snippets from your editor
- File names and structure
- Code you're working on
- Acceptance/rejection of suggestions

### What You Control
- Enable/disable globally
- Disable for specific files/repos
- Block suggestions from public code
- Telemetry settings

### Best Practices
- Don't commit secrets to code
- Review organization policies
- Use Business plan for enterprise control
- Understand data retention policies

## Troubleshooting

### Common Issues

**No Suggestions Appearing**:
- Check extension is enabled
- Verify subscription is active
- Ensure AL files are recognized
- Reload VS Code window

**Poor Quality Suggestions**:
- Download BC symbols
- Add more context (comments, related files)
- Use descriptive names
- Open related AL files

**Slow Performance**:
- Close unnecessary files
- Check internet connection
- Reduce workspace size
- Update VS Code

## Learning Resources

### Official Resources
- [GitHub Copilot Documentation](https://docs.github.com/copilot)
- [VS Code Extension Page](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- [Getting Started Guide](https://docs.github.com/copilot/getting-started-with-github-copilot)

### AL Guidelines Resources
- [Setup Guide](../../getting-started/setup)
- [Effective Prompting](../../getting-started/effective-prompting)
- [Best Practices](../../getting-started/best-practices)
- [Code Review Examples](../../getting-more/code-review)

## When to Use GitHub Copilot

### ✓ Ideal For

- Daily AL development
- Quick code generation
- Learning AL patterns
- Reducing boilerplate
- Standard BC implementations
- Exploring APIs

### ⚠ Consider Alternatives

- **Complex analysis** → Use Claude or Copilot Chat
- **Multi-file refactoring** → Use Cursor
- **Learning deep concepts** → Use Copilot Chat or Claude
- **Architecture decisions** → Human expertise required

## Complementary Tools

**Use With**:
- [GitHub Copilot Chat](github-copilot-chat-agent) - For explanations and debugging
- [AL Language Extension](../Tools/al-language) - For AL support
- [AL Code Analyzers](../Tools/al-codecop) - For quality checks

**Workflow**:
1. Copilot generates code
2. AL analyzers check quality
3. Copilot Chat explains complex parts
4. You review and test

---

**Next Steps**:
- Install and try [GitHub Copilot](../Tools/github-copilot)
- Learn about [Copilot Chat](github-copilot-chat-agent) for complementary features
- Compare with other [AI Agents](./)

**Questions?** Join [GitHub Discussions](https://github.com/microsoft/alguidelines/discussions)
