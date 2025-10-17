---
title: "Best Practices"
linkTitle: "Best Practices"
weight: 4
description: >
  Guidelines for successful AI-assisted AL development
---

## Overview

AI coding assistants are powerful tools, but they work best when used thoughtfully. This guide provides best practices for integrating AI assistance into your AL development workflow.

## General Principles

### 1. AI Augments, Not Replaces
**You are still the developer.** The AI is a tool to enhance your productivity, not a replacement for your expertise.

✅ **Good Approach**:
- Use AI to generate boilerplate code
- Review and understand all generated code
- Make architectural decisions yourself
- Validate business logic

❌ **Poor Approach**:
- Blindly accept all AI suggestions
- Skip code review for AI-generated code
- Let AI make design decisions
- Assume AI understands your business requirements

### 2. Trust, but Verify
Always review AI-generated code:

```al
// AI might generate this:
procedure CalculateDiscount(Amount: Decimal): Decimal
begin
    exit(Amount * 0.1); // Always 10% discount
end;

// But you need to verify it matches requirements:
// - Is 10% correct for all scenarios?
// - Should it vary by customer type?
// - Are there discount limits?
// - Should it read from setup?
```

### 3. Provide Good Context
Better context = better results:

✅ **Provide**:
- Clear file and folder names
- XML documentation comments
- Descriptive variable names
- Project README with conventions
- Open related files

❌ **Avoid**:
- Generic names like `Temp1`, `DoStuff`
- Undocumented complex logic
- Mixing unrelated code in one file

## Code Generation Best Practices

### Start with Structure
Generate scaffolding first, then refine:

1. **First**: Generate basic structure
```
Create a codeunit skeleton for "Sales Order Processor" with procedures for:
- ValidateOrder
- CalculateTotals  
- ProcessPayment
- PostOrder
```

2. **Then**: Implement each procedure
```
Implement the ValidateOrder procedure with these checks:
- Customer exists
- All lines have positive quantities
- Credit limit not exceeded
```

### Review Generated Code
Check AI-generated code for:

**Correctness**
- Does it do what you asked?
- Are there edge cases not handled?
- Is the logic sound?

**AL Best Practices**
- Proper error handling
- Appropriate use of transactions
- Correct field validations
- No unnecessary database calls

**Business Central Standards**
- Correct use of BC APIs
- Proper event patterns
- Standard naming conventions
- Application area settings

**Performance**
- Efficient database queries
- Appropriate filtering
- Minimal record iterations
- Proper use of FindSet vs FindFirst

### Iterate and Refine
Don't expect perfection on first try:

```
// Initial prompt
Create a procedure to import customers from CSV

// After reviewing generated code
Add validation for required fields: Name and Email

// After further review
Add error logging and return a list of failed imports

// Final refinement
Add telemetry tracking for import metrics
```

## Code Review with AI

### Use AI for Initial Review
AI can catch common issues:

```
Review this code for:
- Potential bugs
- Performance issues
- AL best practice violations
- Missing error handling
```

### Don't Skip Human Review
AI review is a supplement, not a replacement:

- **AI catches**: Syntax issues, common patterns, style violations
- **You catch**: Business logic errors, architectural concerns, context-specific issues

### Review AI's Review
The AI might miss context:

```al
// AI might flag this as inefficient:
Customer.SetRange("No.", CustNo);
if Customer.FindFirst() then
    Customer.Name := NewName;

// But might miss that in your context, you're in a loop
// processing thousands of customers, which is inefficient
```

## Documentation with AI

### Generate Drafts, Then Personalize
Use AI for documentation drafts:

```
Generate XML documentation for this codeunit
```

Then review and enhance:
- Add business context
- Include usage examples
- Document assumptions
- Note dependencies

### Keep Documentation Updated
When AI generates code changes:

```
Update this procedure and its XML documentation to include the new parameter
```

### Create User-Facing Documentation
AI can help with user docs too:

```
Create user documentation explaining how to set up customer discount categories.
Target audience: Business users, not developers.
```

## Testing with AI

### Generate Test Scaffolding
```
Create a test codeunit structure for testing the Sales Order Processor
Include test methods for each public procedure
```

### Create Test Data Setup
```
Create a helper procedure that sets up test data:
- One customer with normal credit limit
- One customer with exceeded credit
- Sample items with prices
- Sales header with lines
```

### Don't Rely Only on AI Tests
AI-generated tests might miss:
- Edge cases specific to your business
- Integration scenarios
- Performance testing needs
- User acceptance criteria

## Refactoring with AI

### Safe Refactoring Steps

1. **Ensure Tests Exist**
```
Create tests for this procedure before we refactor it
```

2. **Refactor with AI**
```
Refactor this procedure to extract the discount calculation into a separate function
```

3. **Verify Tests Still Pass**
Run your test suite to confirm behavior unchanged

4. **Review Changes**
Understand what changed and why

### When to Refactor with AI
✅ **Good for**:
- Extracting methods
- Renaming variables
- Applying consistent formatting
- Adding error handling
- Modernizing deprecated APIs

❌ **Be Careful with**:
- Complex business logic changes
- Architectural changes
- Database schema modifications
- Integration point changes

## Learning from AI

### Use AI as a Learning Tool

**Ask for Explanations**:
```
Explain why this code uses Commit instead of direct posting
```

**Request Alternatives**:
```
Show me three different ways to implement this validation,
with pros and cons of each
```

**Learn Patterns**:
```
Show me the standard AL pattern for implementing a document posting routine
```

### Build Your Knowledge
Don't become dependent:
- Understand the code, don't just use it
- Learn the patterns being used
- Research unfamiliar APIs or techniques
- Practice writing code without AI assistance

## Performance Considerations

### AI and Code Performance
AI doesn't automatically write optimal code:

```al
// AI might generate this:
for i := 1 to Customer.Count do begin
    Customer.Get(i);
    ProcessCustomer(Customer);
end;

// You should refactor to:
if Customer.FindSet() then
    repeat
        ProcessCustomer(Customer);
    until Customer.Next() = 0;
```

### Review for Performance
Always check AI-generated code for:
- Database query efficiency
- Unnecessary loops
- Proper use of filters
- Appropriate use of temporary tables

## Security Considerations

### Don't Share Sensitive Data
Be careful what's in your workspace:
- Production connection strings
- Customer data
- API keys or secrets
- Proprietary algorithms

### Review Security Aspects
AI might not catch security issues:

```al
// AI might generate this:
procedure ExecuteSQL(SQLStatement: Text)
begin
    // Direct SQL execution - potential SQL injection!
end;

// You need to catch security concerns
```

## Collaboration Best Practices

### Team Standards
Establish team guidelines:
- When to use AI assistance
- Required review process for AI code
- Documentation requirements
- Testing standards

### Code Review Process
For AI-generated code:
1. Mark commits that include AI-generated code
2. Extra scrutiny during review
3. Explain AI usage in PR descriptions
4. Share learnings with the team

### Knowledge Sharing
Help your team:
- Share effective prompts
- Document successful patterns
- Discuss AI limitations found
- Teach AI-assisted techniques

## When NOT to Use AI

### AI is Not Ideal For:

**Critical Security Code**
- Authentication and authorization
- Encryption implementations
- Security-sensitive validations

**Highly Specialized Logic**
- Unique business rules requiring deep domain knowledge
- Complex calculations with many edge cases
- Industry-specific compliance requirements

**Exploration and Learning**
- When you're trying to learn a new concept
- When you need to deeply understand the solution
- When the journey is as important as the destination

**Quick, Simple Tasks**
- You can type it faster than explaining it
- It's simpler to do it yourself
- The prompt would be longer than the code

## Measuring Success

### Track Your Productivity
Monitor how AI affects your work:
- Time saved on boilerplate code
- Reduction in syntax errors
- Faster documentation creation
- More time for design and testing

### Quality Metrics
Ensure quality isn't suffering:
- Bug rates in AI-assisted code
- Code review findings
- Test coverage
- Performance benchmarks

### Continuous Improvement
- Refine your prompting skills
- Learn from unsuccessful attempts
- Share successes with your team
- Update your practices as AI tools evolve

## Quick Reference: Do's and Don'ts

### ✅ Do
- Review all AI-generated code
- Provide clear, specific prompts
- Use AI for boilerplate and repetitive tasks
- Learn from AI-generated examples
- Test AI-generated code thoroughly
- Keep documentation updated
- Share knowledge with your team

### ❌ Don't
- Blindly accept AI suggestions
- Skip code review for AI code
- Include sensitive data in prompts
- Rely on AI for architectural decisions
- Use AI-generated code you don't understand
- Assume AI knows your business requirements
- Let AI replace your expertise

## Next Steps

- Understand [AI limitations](../limitations) to know when caution is needed
- Try the [practical examples](../../getting-more) to apply these best practices
- Explore [community resources](../../community-resources) for more tips and techniques
