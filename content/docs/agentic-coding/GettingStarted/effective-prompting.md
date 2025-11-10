---
title: "Effective Prompting"
linkTitle: "Effective Prompting"
weight: 3
description: >
  Learn how to communicate clearly with AI assistants to get the best results
---

## Overview

The quality of AI-generated code depends heavily on how you communicate your needs. This guide teaches you how to write effective prompts that lead to better results.

## The Basics of Good Prompts

### Be Specific
Vague prompts lead to generic results. Provide clear, specific instructions.

❌ **Vague**: "Create a page"
```
Create a page
```

✅ **Specific**: "Create a card page for Customer with fields No, Name, Address, and Phone Number"
```
Create a card page for the Customer table that displays these fields:
- No.
- Name
- Address
- Phone No.
Include FactBoxes for Sales Statistics and Contact Information.
```

### Provide Context
Help the AI understand what you're working on.

❌ **No Context**: "Add a field"
```
Add a field to store email
```

✅ **With Context**: "Add an email field to the Customer table extension for newsletter subscriptions"
```
I'm extending the Customer table. Add a new field called "Newsletter Email" to store 
the email address customers want to use for newsletters. This is separate from their 
primary email. Make it a Text field with length 80.
```

### Include Examples
Show the AI what you want by providing examples.

✅ **With Example**:
```
Create an event subscriber for OnAfterValidate on Sales Header's "Sell-to Customer No." 
field, similar to this pattern:

[EventSubscriber(ObjectType::Table, Database::"Sales Header", 'OnAfterValidateEvent', 'Sell-to Customer No.', false, false)]
local procedure OnAfterValidateSellToCustomerNo(var Rec: Record "Sales Header")
begin
    // Your implementation here
end;

The subscriber should copy the Newsletter Email from the Customer to the Sales Header.
```

## Prompting Patterns for AL Development

### 1. Code Generation

**Pattern**: `Create a [object type] that [does what] with [specific requirements]`

**Example**:
```
Create a codeunit named "Sales Order Validator" that validates sales orders before posting.
It should:
- Check that all lines have quantities > 0
- Verify customer credit limit is not exceeded
- Ensure all required fields are filled
- Return a list of validation errors
Use AL coding best practices.
```

### 2. Code Explanation

**Pattern**: `Explain [what] in [level of detail]`

**Examples**:
```
// Simple explanation
Explain what this function does

// Detailed explanation
Explain this procedure in detail, including the purpose of each parameter 
and the business logic flow

// For learning
Explain this code as if I'm new to AL development
```

### 3. Code Improvement

**Pattern**: `Improve this code by [what to improve]`

**Examples**:
```
Improve this code to follow AL best practices

Refactor this procedure to be more performant

Add error handling to this code

Make this code more testable by reducing dependencies
```

### 4. Code Review

**Pattern**: `Review this code for [specific concerns]`

**Examples**:
```
Review this code for potential bugs and performance issues

Check this code against AL coding guidelines

Identify security concerns in this procedure

Find opportunities to reduce database calls in this code
```

### 5. Documentation

**Pattern**: `Generate [documentation type] for [what]`

**Examples**:
```
Generate XML documentation comments for all procedures in this file

Create a README explaining what this extension does and how to install it

Write user documentation for this new feature
```

### 6. Testing

**Pattern**: `Create tests for [what] that [test scenarios]`

**Example**:
```
Create test codeunit for the Sales Order Validator that tests:
- Valid orders pass validation
- Orders with zero quantities fail
- Orders exceeding credit limit fail
- All validation error messages are correct
Use the AL Test framework with Given-When-Then pattern.
```

## Advanced Prompting Techniques

### Chain of Thought
Break complex requests into steps:

```
I need to create a new feature for automatic discount calculation. Let's approach this step by step:

1. First, create a table extension for Sales Line to store discount category
2. Then, create a discount setup table with categories and percentages
3. Next, create a codeunit to calculate discounts based on category
4. Finally, add an event subscriber to apply discounts automatically

Let's start with step 1...
```

### Constraints and Requirements
Be explicit about what you do and don't want:

```
Create a procedure to import customer data from CSV.
Requirements:
- Use streams for large file handling
- Validate email format before importing
- Skip duplicate records (based on external ID)
- Log errors but continue processing
- Return summary of imported, skipped, and failed records
Do NOT:
- Use temporary files
- Import if any record fails validation
- Modify existing customer records
```

### Reference Standards
Point to specific coding standards or patterns:

```
Create a page extension following the AL coding standards in this repository.
Use the same XML documentation pattern as in CustomerProcessor.codeunit.al.
Follow the naming conventions in our README.md.
```

### Iterative Refinement
Start broad, then refine:

```
// First prompt
Create a codeunit to process sales orders

// After seeing initial result, refine
Add error handling using try-catch pattern

// Further refinement
Add logging using AL telemetry

// Final touch
Add XML documentation comments
```

## AL-Specific Prompting Tips

### Specify AL Version
```
Create an AL procedure compatible with Business Central version 21
```

### Mention Dependencies
```
Create a page that uses the "Temp Blob" codeunit from the System Application
```

### Include Object Numbers (if applicable)
```
Create table 50100 "Custom Discount Setup" with fields...
```

### Specify Application Area
```
Create a page with ApplicationArea set to #Basic,#Suite
```

### Reference Standard BC Objects
```
Create a table extension for table 18 "Customer" that adds...
```

## Common Mistakes to Avoid

### ❌ Too Vague
```
Make it better
Fix this
Create something for customers
```

### ❌ Asking Multiple Unrelated Things
```
Create a customer page, fix the sales order bug, and document the project
```
*Better*: Break into separate prompts

### ❌ Assuming Too Much Context
```
Add the field we discussed
```
*Better*: Restate what you need

### ❌ No Validation Criteria
```
Create a validation function
```
*Better*: Specify what to validate and how

## Examples of Great Prompts

### Example 1: Table Extension
```
Create a table extension for Table 36 "Sales Header" that adds these fields:
- "Requested Delivery Date" (Date)
- "Special Instructions" (Text[250])
- "Requires Approval" (Boolean)

Add triggers:
- Set "Requires Approval" to true when amount exceeds $10,000
- Validate "Requested Delivery Date" is not in the past

Include XML documentation for all fields.
```

### Example 2: API Page
```
Create an API page for the Item table that exposes:
- No.
- Description
- Unit Price
- Inventory

Follow AL API best practices:
- Use API versioning (v1.0)
- Include OData annotations
- Handle GET, POST, PATCH methods
- Validate required fields on POST
```

### Example 3: Test Code
```
Create a test codeunit for the "Sales Order Validator" codeunit.
Tests needed:
1. TestValidOrderPassesValidation - Create valid order, verify no errors
2. TestZeroQuantityFails - Create order with 0 quantity, verify error
3. TestCreditLimitExceeded - Create order exceeding limit, verify error
4. TestMissingRequiredField - Skip required field, verify error

Use:
- [Test] attribute
- Given-When-Then pattern
- LibrarySales for test data
- Assert for verification
```

## Practice Exercise

Try improving this vague prompt:

❌ **Vague**: 
```
Create code for discounts
```

✅ **Improved Version** (your attempt):
```
[Think about: What type of code? What discounts? What should it do? 
What are the requirements? What standards should it follow?]
```

<details>
<summary>See Suggested Answer</summary>

```
Create a codeunit "Customer Discount Manager" that calculates volume-based discounts.

Requirements:
- Accept parameters: Customer No., Item No., Quantity
- Return: Discount percentage (Decimal)
- Business logic:
  * 0-10 units: No discount
  * 11-50 units: 5% discount
  * 51-100 units: 10% discount
  * 100+ units: 15% discount
- Read discount tiers from a setup table
- Log calculation to telemetry
- Include error handling for invalid inputs
- Add XML documentation
- Follow AL best practices for procedure naming and structure
```
</details>

## Next Steps

Now that you know how to write effective prompts:
- Review the [best practices](../best-practices) for AI-assisted development
- Try the [practical examples](../../gettingmore) with your new prompting skills
- Understand the [limitations](../limitations) of AI assistants
