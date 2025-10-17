---
title: "GitHub Copilot Chat Agent"
linkTitle: "Copilot Chat"
weight: 2
description: >
  Conversational AI assistant for code explanations, debugging, and interactive development
---

## Overview

GitHub Copilot Chat is a conversational AI assistant integrated into Visual Studio Code that allows you to ask questions, get explanations, and receive guidance through natural language interaction.

**Developer**: GitHub (Microsoft)  
**Type**: Conversational AI Agent  
**Primary Use**: Interactive code assistance, explanations, and debugging  
**Integration**: Native VS Code extension

## What is GitHub Copilot Chat?

GitHub Copilot Chat provides an interactive AI assistant that:
- Answers questions about code and AL development
- Explains complex code segments
- Helps debug issues
- Suggests refactoring approaches
- Provides learning and guidance
- Generates code based on detailed requirements

### How It Works

1. **You ask**: Questions in natural language via chat panel or inline
2. **Chat analyzes**: Your code, context, and question
3. **Chat responds**: With explanations, code, or suggestions
4. **You interact**: Ask follow-ups, refine, or apply suggestions

**Example Interaction**:
```
You: Explain how this procedure validates customer credit limits

Chat: This procedure validates customer credit limits by:
1. Retrieving the customer record
2. Calculating current outstanding amount
3. Comparing against credit limit
4. Raising an error if exceeded

The validation ensures customers cannot exceed their credit limits
before creating new sales orders...
```

## Key Capabilities

### For AL Development

**Code Explanation**:
- Describes what AL code does
- Explains Business Central concepts
- Breaks down complex procedures
- Clarifies AL syntax and patterns

**Interactive Learning**:
- Teaches AL patterns
- Explains BC APIs
- Provides examples
- Answers "how do I..." questions

**Debugging Assistance**:
- Helps diagnose errors
- Suggests fixes
- Explains error messages
- Proposes debugging strategies

**Code Generation**:
- Creates code from detailed descriptions
- Generates test cases
- Produces documentation
- Builds complex structures

## How to Use Copilot Chat

### Chat Panel

**Open Chat**:
- Press `Ctrl+Shift+I` (Windows/Linux)
- Press `Cmd+Shift+I` (Mac)
- Click chat icon in sidebar

**Chat Interface**:
```
┌─────────────────────────────┐
│  GitHub Copilot Chat        │
├─────────────────────────────┤
│ Your conversation history   │
│                             │
│ You: How do I...            │
│ Copilot: You can...         │
│                             │
├─────────────────────────────┤
│ Type your question...    [>]│
└─────────────────────────────┘
```

### Inline Chat

**Open Inline**:
- Press `Ctrl+I` in editor
- Chat appears next to your code
- Ask quick questions
- Get instant suggestions

**Use Cases**:
- Quick refactoring
- Explain selected code
- Fix errors in place
- Generate code snippets

### Slash Commands

Quick access to common tasks:

| Command | Purpose | Example |
|---------|---------|---------|
| `/explain` | Explain selected code | `/explain this validation logic` |
| `/fix` | Suggest fixes | `/fix the error in this procedure` |
| `/tests` | Generate tests | `/tests for this codeunit` |
| `/help` | Show commands | `/help` |
| `/clear` | Clear chat | `/clear` |

## Strengths for AL Development

### ✓ Excellent At

**Learning & Understanding**:
- Explaining AL concepts
- Teaching BC patterns
- Clarifying syntax
- Answering "why" questions

**Debugging Help**:
- Diagnosing errors
- Suggesting fixes
- Explaining error messages
- Proposing test cases

**Code Exploration**:
- Understanding legacy code
- Identifying patterns
- Finding issues
- Suggesting improvements

**Documentation**:
- Generating XML comments
- Creating README files
- Writing user guides
- Explaining features

### ⚠ Use With Care

**Generated Code**:
- Always review for correctness
- Verify BC compatibility
- Test thoroughly
- Adapt to your standards

**Complex Business Logic**:
- May not understand specific requirements
- Review carefully
- Validate against rules

**Performance Advice**:
- General suggestions may not fit your scenario
- Profile and test
- Verify with BC best practices

## AL-Specific Examples

### Code Explanation

**You**:
```
Explain this procedure and when I should use it

[Select and paste this code]:
procedure CheckCreditLimit(var SalesHeader: Record "Sales Header")
var
    Customer: Record Customer;
    OutstandingAmount: Decimal;
begin
    Customer.Get(SalesHeader."Sell-to Customer No.");
    Customer.CalcFields("Balance (LCY)");
    OutstandingAmount := Customer."Balance (LCY)" + SalesHeader."Amount Including VAT";
    
    if OutstandingAmount > Customer."Credit Limit (LCY)" then
        Error('Customer %1 would exceed credit limit. Outstanding: %2, Limit: %3', 
              Customer."No.", OutstandingAmount, Customer."Credit Limit (LCY)");
end;
```

**Chat Explains**:
```
This procedure validates customer credit limits before allowing sales orders:

Purpose:
- Prevents customers from exceeding their credit limits
- Calculates total outstanding amount (existing balance + new order)
- Raises an error if limit would be exceeded

When to use:
- Before posting sales orders
- In OnValidate triggers for sales headers
- As part of order validation workflow

Key points:
- Uses CalcFields to get current customer balance
- Adds new order amount to existing balance
- Compares total against credit limit
- Provides clear error message with details

Best practice considerations:
- Consider making error message translatable
- Might want to allow override with permission
- Could log credit limit violations for reporting
```

### Generating Complex Code

**You**:
```
Create a codeunit that processes sales orders in batch.

Requirements:
- Accept list of sales order numbers
- Validate each order (check credit, inventory)
- Post valid orders
- Collect and return errors for failed orders
- Log to telemetry
- Show progress dialog

Use proper error handling and AL best practices.
```

**Chat Generates**:
```al
codeunit 50100 "Batch Sales Order Processor"
{
    /// <summary>
    /// Processes multiple sales orders in batch with validation and error handling
    /// </summary>
    procedure ProcessOrders(OrderNumbers: List of [Code[20]]): List of [Text]
    var
        SalesHeader: Record "Sales Header";
        Errors: List of [Text];
        Dialog: Dialog;
        Counter: Integer;
        TotalCount: Integer;
    begin
        TotalCount := OrderNumbers.Count;
        Dialog.Open('Processing orders...\Order #1######### of #2#########');
        
        foreach OrderNo in OrderNumbers do begin
            Counter += 1;
            Dialog.Update(1, Counter);
            Dialog.Update(2, TotalCount);
            
            if not ProcessSingleOrder(OrderNo, Errors) then
                LogProcessingError(OrderNo, Errors);
        end;
        
        Dialog.Close();
        LogBatchCompletion(TotalCount, Counter - Errors.Count, Errors.Count);
        exit(Errors);
    end;

    local procedure ProcessSingleOrder(OrderNo: Code[20]; var Errors: List of [Text]): Boolean
    var
        SalesHeader: Record "Sales Header";
        SalesPost: Codeunit "Sales-Post";
    begin
        if not SalesHeader.Get(SalesHeader."Document Type"::Order, OrderNo) then begin
            Errors.Add(StrSubstNo('Order %1 not found', OrderNo));
            exit(false);
        end;

        if not ValidateOrder(SalesHeader, Errors) then
            exit(false);

        if not SalesPost.Run(SalesHeader) then begin
            Errors.Add(StrSubstNo('Order %1: %2', OrderNo, GetLastErrorText()));
            exit(false);
        end;

        exit(true);
    end;

    // Additional helper procedures...
end;
```

### Debugging Assistance

**You**:
```
I'm getting error "The field Customer No. must have a value" 
when trying to post a sales order. Here's my code:

[Paste problematic code]

What's wrong?
```

**Chat Analyzes**:
```
The error occurs because the Customer No. field is empty when posting.

Looking at your code, the issue is on line 15:
    SalesHeader."Sell-to Customer No." := '';

This explicitly sets the customer number to empty before posting.

Likely causes:
1. Customer lookup/validation failed earlier
2. Field being cleared unintentionally
3. Missing validation before assignment

Recommended fixes:

1. Remove the line setting it to empty
2. Add validation:
   if Customer.Get(CustomerNo) then
       SalesHeader.Validate("Sell-to Customer No.", CustomerNo)
   else
       Error('Customer %1 does not exist', CustomerNo);

3. Add defensive check before posting:
   if SalesHeader."Sell-to Customer No." = '' then
       Error('Customer must be specified before posting');

Would you like me to show the corrected version of your code?
```

### Learning AL Patterns

**You**:
```
What's the standard AL pattern for implementing a document posting routine?
```

**Chat Explains**:
```
Standard AL document posting routines follow this pattern:

1. Structure:
   - Main posting codeunit
   - Optional posting preview codeunit
   - Optional batch posting codeunit

2. Main Posting Codeunit Pattern:

codeunit 50100 "Document-Post"
{
    TableNo = "Your Document Header";
    
    trigger OnRun()
    begin
        PostDocument(Rec);
    end;
    
    procedure PostDocument(var DocHeader: Record "Your Document Header")
    var
        DocLine: Record "Your Document Line";
    begin
        // 1. Pre-posting checks
        CheckDocumentIsReadyToPost(DocHeader);
        
        // 2. Run events (OnBeforePost)
        OnBeforePostDocument(DocHeader);
        
        // 3. Lock tables
        DocHeader.LockTable();
        
        // 4. Post with transaction
        PostDocumentLines(DocHeader);
        FinalizePosting(DocHeader);
        
        // 5. Run events (OnAfterPost)
        OnAfterPostDocument(DocHeader);
    end;
    
    // Additional procedures...
    
    // Events for extensibility
    [IntegrationEvent(false, false)]
    local procedure OnBeforePostDocument(var DocHeader: Record "Your Document Header")
    begin
    end;
}

Key principles:
- Use transactions (implicit in OnRun trigger)
- Lock records being modified
- Provide integration events
- Validate before posting
- Handle errors gracefully
- Use Codeunit.Run for error handling

Would you like examples of specific parts?
```

## How It Differs from Other Agents

### vs. GitHub Copilot (Inline)

**Copilot Chat**:
- ✓ Better for explanations
- ✓ Can answer questions
- ✓ Helps with debugging
- ✓ Teaches concepts
- ✗ Slower for quick edits

**Copilot (Inline)**:
- ✓ Faster completions
- ✓ Less disruptive
- ✓ Great for quick coding
- ✗ No explanations
- ✗ Can't answer questions

**Use Together**: Inline for coding, Chat for understanding

### vs. Claude

**Copilot Chat**:
- ✓ Native VS Code integration
- ✓ Automatic code context
- ✓ No copy-paste needed
- ✗ Smaller context window
- ✗ Less analytical depth

**Claude**:
- ✓ Larger context window
- ✓ Deeper analysis
- ✓ Better reasoning
- ✗ Manual workflow
- ✗ No editor integration

**Use Cases**: Chat for daily work, Claude for deep analysis

### vs. Cursor

**Copilot Chat**:
- ✓ Standard VS Code
- ✓ Familiar environment
- ✓ Single purpose
- ✗ One AI model
- ✗ Limited features

**Cursor**:
- ✓ Multiple AI models
- ✓ Composer mode
- ✓ Advanced features
- ✗ Different editor
- ✗ Higher learning curve

**Use Cases**: Chat for standard workflow, Cursor for advanced needs

## Best Practices

### Asking Good Questions

**Be Specific**:
```
❌ "Explain this"
✅ "Explain how this procedure handles inventory updates 
    and why it uses a transaction"
```

**Provide Context**:
```
❌ "How do I validate?"
✅ "How do I validate customer credit limits in AL before 
    posting a sales order? Show me the BC standard pattern."
```

**Break Down Complex Questions**:
```
Instead of:
"Build complete order management system"

Try:
1. "Show me pattern for order validation"
2. "Now add posting logic"
3. "Add error handling"
4. "Add telemetry"
```

### Using Responses Effectively

**Review Code**:
- Understand what it does
- Verify BC compatibility
- Check best practices
- Test thoroughly

**Learn from Explanations**:
- Read thoroughly
- Try examples yourself
- Ask follow-up questions
- Apply to your own code

**Iterate**:
- Start with basic request
- Refine based on response
- Add requirements gradually
- Build understanding

## Setup & Configuration

### Installation

See [GitHub Copilot Chat Tool Page](../Tools/github-copilot-chat) for detailed setup.

**Quick Start**:
1. Install GitHub Copilot Chat extension
2. Verify Copilot subscription
3. Open chat panel (`Ctrl+Shift+I`)
4. Start asking questions

### Optimizing for AL

**Provide Context**:
- Keep relevant AL files open
- Reference BC objects specifically
- Mention AL version when relevant
- Include app.json configuration

## Pricing

**Included with GitHub Copilot**:
- Individual: $10/month or $100/year
- Business: $19/user/month
- Free for students and OSS maintainers

No separate charge - comes with Copilot subscription.

## Privacy & Security

### What Gets Sent
- Your chat messages
- Selected code snippets
- Context from open files
- Workspace information (limited)

### Best Practices
- Don't paste sensitive data
- Avoid customer information
- Review organization policies
- Use business plan for enterprise controls

## When to Use Copilot Chat

### ✓ Ideal For

- Learning AL and BC concepts
- Understanding existing code
- Debugging issues
- Getting explanations
- Generating complex code
- Exploring patterns
- Documentation creation

### ⚠ Consider Alternatives

- **Quick completions** → Use GitHub Copilot (inline)
- **Very large context** → Use Claude
- **Multi-file refactoring** → Use Cursor
- **Critical decisions** → Consult humans

## Practical Workflow

**Daily Development**:
1. Write code with Copilot inline suggestions
2. Use Chat to explain complex parts
3. Ask Chat for debugging help when stuck
4. Generate tests with Chat
5. Create documentation with Chat

**Learning**:
1. Ask Chat about AL patterns
2. Request examples
3. Get explanations of BC concepts
4. Explore APIs and features

**Code Review**:
1. Select code section
2. Ask Chat to review
3. Get suggestions for improvements
4. Learn better patterns

## Resources

### Official Documentation
- [Copilot Chat Docs](https://docs.github.com/copilot/github-copilot-chat)
- [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat)

### AL Guidelines Resources
- [Effective Prompting](../../gettingstarted/effective-prompting)
- [Best Practices](../../gettingstarted/best-practices)
- [Getting More Examples](../../gettingmore)

## Complementary Tools

**Use With**:
- [GitHub Copilot](github-copilot-agent) - For inline completions
- [AL Language Extension](../Tools/al-language) - For AL support
- [AL Code Analyzers](../Tools/al-codecop) - For quality

**Workflow**:
1. Copilot generates code quickly
2. Chat explains what it does
3. You understand and refine
4. Analyzers validate quality

---

**Next Steps**:
- Install [GitHub Copilot Chat](../Tools/github-copilot-chat)
- Try with [GitHub Copilot](github-copilot-agent)
- Compare with other [AI Agents](./)

**Questions?** Join [GitHub Discussions](https://github.com/microsoft/alguidelines/discussions)
