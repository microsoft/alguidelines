---
title: "Understanding Limitations"
linkTitle: "Limitations"
weight: 5
description: >
  Know when to use (and not use) AI assistance in AL development
---

## Overview

AI coding assistants are powerful tools, but they have limitations. Understanding these limitations helps you use AI effectively and avoid common pitfalls.

## Knowledge Limitations

### Training Data Cutoff
AI models are trained on data up to a specific date:

**Implication**:
- May not know about the latest AL features
- Might suggest deprecated APIs
- Could miss recent Business Central updates
- May not be aware of new best practices

**What to Do**:
- Verify suggestions against current documentation
- Check for deprecated features
- Stay updated on BC releases yourself
- Supplement AI with official Microsoft docs

### Lack of Real-Time Information
AI doesn't know:
- Your specific BC version and configuration
- Your organization's custom extensions
- Your specific business requirements
- Current state of your codebase

**What to Do**:
- Provide context in your prompts
- Specify BC version when relevant
- Describe dependencies and extensions
- Share organizational standards

### Incomplete AL Knowledge
AI might not fully understand:
- Complex AL compiler behavior
- Subtle differences between AL versions
- Specific BC platform limitations
- Performance characteristics of certain operations

**What to Do**:
- Test generated code thoroughly
- Verify with official documentation
- Profile performance-critical code
- Consult AL experts for complex scenarios

## Code Quality Limitations

### May Generate Suboptimal Code

**Example 1: Inefficient Database Access**
```al
// AI might generate:
procedure CountCustomersInCity(CityName: Text): Integer
var
    Customer: Record Customer;
    Counter: Integer;
begin
    Counter := 0;
    if Customer.FindSet() then
        repeat
            if Customer.City = CityName then
                Counter += 1;
        until Customer.Next() = 0;
    exit(Counter);
end;

// Better approach:
procedure CountCustomersInCity(CityName: Text): Integer
var
    Customer: Record Customer;
begin
    Customer.SetRange(City, CityName);
    exit(Customer.Count);
end;
```

**Example 2: Missing Error Handling**
```al
// AI might generate:
procedure GetCustomerEmail(CustomerNo: Code[20]): Text
var
    Customer: Record Customer;
begin
    Customer.Get(CustomerNo);
    exit(Customer."E-Mail");
end;

// Should include error handling:
procedure GetCustomerEmail(CustomerNo: Code[20]): Text
var
    Customer: Record Customer;
begin
    if not Customer.Get(CustomerNo) then
        Error('Customer %1 does not exist.', CustomerNo);
    
    if Customer."E-Mail" = '' then
        Error('Customer %1 has no email address.', CustomerNo);
    
    exit(Customer."E-Mail");
end;
```

### May Not Follow Your Standards
AI doesn't automatically know:
- Your naming conventions
- Your code organization preferences
- Your error handling patterns
- Your logging standards

**What to Do**:
- Include standards in prompts
- Create prompt templates
- Maintain coding guidelines document
- Review and adapt generated code

### May Create Inconsistent Code
AI might:
- Use different patterns across files
- Mix coding styles
- Apply inconsistent naming
- Vary error handling approaches

**What to Do**:
- Establish clear patterns early
- Refactor for consistency
- Use linters and code analyzers
- Conduct thorough code reviews

## Business Logic Limitations

### No Domain Knowledge
AI doesn't understand:
- Your specific business processes
- Industry regulations you must follow
- Your customers' needs
- Your company's policies

**Example**:
```
You ask: "Create discount calculation logic"

AI generates: 10% flat discount

But you need: 
- Tiered discounts by volume
- Special rates for preferred customers
- Regional pricing variations
- Promotional discounts
- Loyalty program integration
```

**What to Do**:
- Provide detailed business requirements
- Include business rules in prompts
- Review logic for business correctness
- Validate with business stakeholders

### Can't Make Business Decisions
AI shouldn't decide:
- Which features to implement
- How to prioritize requirements
- What trade-offs to make
- Which approach best fits your needs

**You must decide**:
- Architecture and design
- Feature scope
- Performance vs. complexity trade-offs
- User experience choices

## Technical Limitations

### Context Window Limitations
AI can only see:
- A limited amount of code at once
- Recently opened files
- Content you explicitly share

**Implications**:
- Might miss dependencies in other files
- May not see full context of large codebases
- Could suggest code that conflicts with other parts

**What to Do**:
- Keep related files open
- Provide context in prompts
- Reference specific files and procedures
- Review for integration issues

### Can't Execute or Test Code
AI can't:
- Run your code
- Execute tests
- Connect to your database
- Verify actual behavior

**Implications**:
- Might generate syntactically correct but broken code
- Can't verify business logic works
- Won't catch runtime errors
- Can't validate performance

**What to Do**:
- Always test generated code
- Run your test suite
- Verify in actual BC environment
- Profile performance-critical code

### Can't Access External Systems
AI doesn't know about:
- Your database state
- External APIs you integrate with
- Third-party extensions installed
- Network or security constraints

**What to Do**:
- Document external dependencies
- Test integrations thoroughly
- Verify API compatibility
- Check security implications

## Safety and Security Limitations

### Limited Security Awareness
AI might not catch:
- SQL injection vulnerabilities
- Authorization bypass issues
- Data leakage risks
- Insecure data handling

**Example**:
```al
// AI might generate:
procedure RunDynamicQuery(FilterText: Text)
begin
    // Could be SQL injection risk if FilterText comes from user
    Customer.SetFilter(City, FilterText);
end;

// Need to add validation:
procedure RunDynamicQuery(FilterText: Text)
begin
    ValidateFilterInput(FilterText); // Add validation
    Customer.SetFilter(City, FilterText);
end;
```

**What to Do**:
- Security review all generated code
- Validate inputs from users
- Follow security best practices
- Consult security experts

### Privacy Concerns
Be careful not to share:
- Customer data
- Production database content
- API keys or credentials
- Proprietary business logic

**What to Do**:
- Use sample data in prompts
- Sanitize code before sharing
- Review organizational policies
- Use private AI instances if available

## Reliability Limitations

### Inconsistent Results
AI might:
- Give different answers to same question
- Vary quality across generations
- Make occasional "hallucinations"
- Provide confident but wrong information

**What to Do**:
- Verify all suggestions
- Don't assume correctness
- Cross-check with documentation
- Regenerate if quality is poor

### Can Make Mistakes
AI can:
- Misunderstand requirements
- Make logical errors
- Suggest deprecated features
- Create subtle bugs

**Real Examples**:
```al
// AI might confuse similar concepts:
// You ask for "customer balance"
// It generates code for "customer credit limit"

// AI might mix AL versions:
// Suggest AL syntax not available in your BC version

// AI might misapply patterns:
// Use patterns from C# instead of AL conventions
```

**What to Do**:
- Treat AI as a junior developer
- Review everything carefully
- Test thoroughly
- Validate assumptions

## Workflow Limitations

### Can't Handle Complex Refactoring
AI struggles with:
- Large-scale architecture changes
- Multi-file refactoring
- Complex dependency updates
- Breaking changes across modules

**What to Do**:
- Break into smaller steps
- Do complex refactoring manually
- Use AI for individual pieces
- Plan architecture yourself

### Limited Long-Term Memory
AI doesn't remember:
- Previous conversations (in some tools)
- Decisions made earlier in project
- Your preferences over time
- Context from last week

**What to Do**:
- Restate context when needed
- Document decisions
- Include relevant background in prompts
- Don't assume AI remembers

### Can't Collaborate Directly
AI can't:
- Participate in code reviews
- Attend planning meetings
- Discuss with stakeholders
- Make consensus decisions

**What to Do**:
- Use AI for preparation
- Review AI suggestions with team
- Make collaborative decisions yourself
- Document team agreements

## When to Be Extra Careful

### High-Risk Scenarios

**Financial Calculations**
```
Extra vigilance needed for:
- Payment processing
- Tax calculations
- Currency conversions
- Pricing logic
```

**Compliance and Audit**
```
Careful review for:
- Regulatory compliance code
- Audit trail functionality
- Data retention policies
- Access control
```

**Data Integrity**
```
Thorough testing for:
- Database modifications
- Data migrations
- Batch processing
- Transaction handling
```

**Integration Points**
```
Extensive validation for:
- API integrations
- Web service calls
- External system connections
- Data synchronization
```

## Recognizing AI Limitations

### Warning Signs

**The AI:**
- Gives very generic solutions
- Doesn't ask clarifying questions
- Suggests deprecated features
- Provides inconsistent answers
- Seems overly confident about uncertain things
- Generates syntactically correct but illogical code

**What to Do:**
- Seek second opinion
- Consult documentation
- Ask a colleague
- Test more thoroughly
- Provide more context
- Try rephrasing prompt

## Complementing AI with Other Resources

### Use Multiple Sources

**For Learning:**
- Official Microsoft Learn
- BC documentation
- Community blogs
- Training courses

**For Problem Solving:**
- Microsoft Docs
- Community forums
- Stack Overflow
- Colleague expertise

**For Best Practices:**
- AL Guidelines (this site!)
- Microsoft patterns
- Community standards
- Team conventions

**For Validation:**
- Code analyzers
- Test frameworks
- Peer review
- Static analysis tools

## The Bottom Line

### AI is a Tool, Not a Solution
- Use it to augment your skills
- Don't rely on it exclusively
- Maintain your expertise
- Stay critical and thoughtful

### Your Responsibilities Remain
- Understand the code
- Ensure correctness
- Maintain quality
- Make decisions
- Own the results

### Continuous Learning
- AI tools will improve
- Your skills must keep pace
- Learn from AI's mistakes
- Evolve your practices

## Next Steps

Now that you understand AI limitations:
- Apply this knowledge in the [practical examples](../../getting-more)
- See how to work within these limitations in [best practices](../best-practices)
- Explore [community resources](../../community-resources) for more insights
