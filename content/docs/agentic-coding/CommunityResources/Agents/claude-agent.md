---
title: "Claude Agent"
linkTitle: "Claude"
weight: 3
description: >
  Anthropic's AI assistant with strong reasoning and large context window for AL development
---

## Overview

Claude is Anthropic's AI assistant known for its strong reasoning capabilities, long context window, and helpful, detailed responses. While not specifically designed for coding, it excels at code analysis, architecture discussions, and complex problem-solving for AL development.

**Developer**: Anthropic  
**Type**: Conversational AI Assistant  
**Primary Use**: Code analysis, learning, complex problem-solving  
**Integration**: Web interface (Claude.ai) or API

## What is Claude?

Claude is a general-purpose AI assistant that:
- Provides detailed, thoughtful analysis
- Handles very large amounts of code
- Reasons through complex problems
- Explains concepts clearly
- Generates well-structured code
- Maintains context across long conversations

### Access Methods

**Claude.ai (Web)**:
- Free tier available
- Pro tier ($20/month) for more usage
- Upload files directly
- Conversation interface
- No IDE integration

**Claude API**:
- Programmatic access
- Can be integrated into tools
- Pay-per-use pricing
- Requires development

**Note**: Unlike Copilot/Cursor, Claude doesn't have native VS Code integration.

## Key Capabilities for AL Development

### Exceptional Context Window

**Claude 3.5 Sonnet**: ~200K tokens
- Can analyze entire AL projects
- Process multiple large files simultaneously
- Maintain context across long conversations
- Reference earlier parts of discussion

**Practical Use**:
```
You can paste:
- Multiple complete AL files
- Entire codeunit implementations
- Full table structures
- Large amounts of documentation

Claude maintains context and can reference any part.
```

### Strong Analytical Abilities

**Code Analysis**:
- Deep understanding of code structure
- Identifies patterns and anti-patterns
- Suggests architectural improvements
- Explains complex logic clearly

**Problem Solving**:
- Reasons through complex scenarios
- Considers multiple approaches
- Explains trade-offs
- Provides detailed rationale

**Learning & Teaching**:
- Patient, clear explanations
- Step-by-step breakdowns
- Answers follow-up questions
- Adapts to your knowledge level

## Strengths for AL Development

### ✓ Exceptional At

**Large Codebase Analysis**:
```
Paste your entire project:
- All codeunits
- Table structures
- Page definitions
- Integration logic

Ask: "Review this AL project for architectural improvements"

Claude can analyze it all and provide comprehensive feedback.
```

**Architecture & Design**:
```
Questions like:
- "Should I use events or direct calls for this integration?"
- "How should I structure this multi-tenant extension?"
- "What's the best way to handle this complex business logic?"

Claude provides thoughtful, reasoned analysis.
```

**Complex Problem Solving**:
```
Describe complex scenarios:
- Multi-step business processes
- Integration challenges
- Performance optimization
- Refactoring strategies

Claude reasons through options and trade-offs.
```

**Learning & Understanding**:
```
Questions like:
- "Explain Business Central's posting architecture"
- "How does AL's event system work?"
- "What are the differences between these BC patterns?"

Claude provides detailed, educational responses.
```

### ⚠ Limitations

**No IDE Integration**:
- Manual copy-paste workflow
- No inline suggestions
- No automatic context
- More context switching

**AL-Specific Training**:
- Not specifically trained on AL
- May not know latest BC features
- Might suggest non-AL patterns
- Requires clear AL context

**Real-Time Coding**:
- Not designed for quick completions
- Better for analysis than generation
- Slower workflow than Copilot
- Best for thoughtful tasks

## Practical AL Examples

### Architecture Review

**You** (paste entire project):
```
I'm building a customer loyalty system for Business Central.
Here are my main files:

[Paste 5-10 AL files totaling thousands of lines]

Review the architecture and suggest improvements.
```

**Claude Responds**:
```
I've analyzed your loyalty system implementation. Here's my review:

Overall Architecture:
Your implementation follows a good separation of concerns with:
- Setup tables (Loyalty Tier, Loyalty Rules)
- Transaction table (Loyalty Points Entry)
- Processing codeunits (Points Calculator, Tier Evaluator)
- Integration through event subscribers

Strengths:
1. Good use of events for extensibility
2. Clear separation between setup and transactions
3. Proper use of temporary tables for calculations

Areas for Improvement:

1. Performance Optimization (Table 50101 "Loyalty Points Entry"):
   - Current implementation recalculates totals on every query
   - Recommendation: Add a "Total Points" field on Customer table
   - Use events to update when points are added/removed
   - Reduces database load significantly

2. Transaction Handling (Codeunit 50100):
   Lines 45-67 process points without transaction wrapping.
   This could lead to partial updates if an error occurs.
   
   Suggested pattern:
   procedure AwardPoints(...)
   begin
       // Wrap in Codeunit.Run for transaction
       if not AwardPointsInternal(...) then
           Error(GetLastErrorText());
   end;
   
   procedure AwardPointsInternal(...): Boolean
   begin
       // Processing logic here
   end;

3. Testing Considerations:
   - No test codeunits provided
   - Critical calculation logic in Points Calculator should have unit tests
   - Recommendation: Create test codeunit with scenarios for:
     * Points calculation accuracy
     * Tier upgrades/downgrades
     * Edge cases (negative points, expired points)

4. Telemetry (Missing):
   Consider adding Application Insights telemetry for:
   - Points awarded/redeemed
   - Tier changes
   - Calculation performance
   - Error conditions

5. API Exposure:
   For mobile/web integration, consider:
   - API page for customer's current points balance
   - API page for points history
   - OData for reporting

Would you like me to elaborate on any of these points or show
specific code examples for the improvements?
```

### Complex AL Pattern Explanation

**You**:
```
I found this code in standard BC. Can you explain in detail how
this posting pattern works and why it's structured this way?

[Paste complex BC posting code]
```

**Claude Explains**:
```
This is Business Central's standard document posting pattern.
Let me break it down step by step:

Structure Overview:
1. OnRun trigger (lines 10-15): Entry point
2. Main posting procedure (lines 20-150): Orchestration
3. Helper procedures (lines 155+): Specific tasks
4. Integration events: Extensibility points

Detailed Explanation:

Phase 1: Pre-Posting Validation (lines 22-35)
--------------------------------------------
The code first validates the document is ready to post:

  OnRun()
  begin
      CheckDocumentIsReadyToPost(SalesHeader);
  end;

This separation ensures:
- Validation logic is reusable
- Errors caught before any changes
- Can be tested independently

Key validations:
- Document not already posted
- Required fields populated
- Valid line items exist
- Customer not blocked

Phase 2: Table Locking (lines 40-45)
------------------------------------
  SalesHeader.LockTable();
  SalesLine.LockTable();

Critical for multi-user scenarios:
- Prevents concurrent modifications
- Ensures data consistency
- Follows database transaction best practices

This must happen INSIDE the implicit transaction
(started by OnRun trigger).

Phase 3: Integration Events "Before" (lines 50-55)
-------------------------------------------------
  OnBeforePostSalesDocument(SalesHeader, PostingResult);

Allows extensions to:
- Add additional validation
- Modify posting parameters
- Log custom data
- Integrate with external systems

Extensions subscribe to these events to add functionality
without modifying base code.

Phase 4: Core Posting Logic (lines 60-120)
------------------------------------------
The actual posting work:

  PostSalesLines(SalesHeader);      // Create ledger entries
  PostCustomerLedger(SalesHeader);  // Update customer balance
  UpdateInventory(SalesHeader);     // Adjust inventory
  CreatePostedDocument(SalesHeader);// Archive posted doc

Each step is a separate procedure because:
- Clarity and maintainability
- Reusability
- Easier testing
- Clear transaction boundaries

Phase 5: Integration Events "After" (lines 125-130)
--------------------------------------------------
  OnAfterPostSalesDocument(SalesHeader, PostingResult);

Allows extensions to:
- Trigger follow-up actions
- Send notifications
- Update related data
- Log completion

Phase 6: Cleanup (lines 135-145)
--------------------------------
  DeleteAfterPosting(SalesHeader);
  Commit;

- Removes unposted document
- Commits transaction
- Frees locks

Why This Pattern?

1. Reliability:
   - Explicit transaction boundaries
   - Proper locking prevents conflicts
   - Validation before any changes

2. Extensibility:
   - Events at every critical point
   - Extensions can hook in without modification
   - Standard pattern across all posting routines

3. Maintainability:
   - Clear, linear flow
   - Each procedure has single responsibility
   - Easy to understand and debug

4. Performance:
   - Table locks minimize deadlocks
   - Efficient batch processing
   - Single transaction reduces overhead

5. Error Handling:
   - OnRun trigger provides implicit error handling
   - Transaction rolled back on error
   - Clean state maintained

This is the gold standard for BC posting routines. When creating
your own posting logic, follow this same pattern for:
- Custom documents
- Batch processing
- Any transactional operations

Would you like me to explain any specific part in more detail,
or show how to implement this pattern for a custom document?
```

### Migration Strategy

**You**:
```
We have 50,000 lines of NAV 2018 C/AL code to migrate to BC AL.
What's the best strategy?

[Include examples of your NAV code]
```

**Claude Provides**:
```
Migrating 50K lines of C/AL to AL is substantial. Here's a strategic approach:

Phase 1: Assessment & Planning (Week 1-2)
-----------------------------------------

1. Code Analysis:
   - Categorize by object type
   - Identify custom vs modified standard objects
   - Map dependencies
   - Find external integrations

2. Risk Assessment:
   HIGH RISK (manual migration priority):
   - Heavy .NET interop
   - Automation/OCX controls  
   - Client-side code
   - File system access
   
   MEDIUM RISK (tool + manual):
   - Standard modifications
   - Reports
   - Complex business logic
   
   LOW RISK (mostly automated):
   - Simple tables
   - Basic pages
   - Straightforward codeunits

3. Tool Selection:
   - AL Object Designer for conversion
   - txt2al for initial conversion
   - Manual for complex cases

Phase 2: Automated Conversion (Week 3-4)
----------------------------------------

1. Run automated tools on LOW/MEDIUM risk code
2. Fix compilation errors
3. Address obvious issues
4. Don't spend time on perfect conversion yet

Phase 3: Manual Review & Refactoring (Week 5-12)
-----------------------------------------------

Priority order:
1. Core business logic (highest value)
2. Integration points (highest risk)
3. Reports (user-facing)
4. Supporting code (lower priority)

For each module:
a) Understand business purpose
b) Review automated conversion
c) Refactor to AL patterns
d) Add error handling
e) Add telemetry
f) Create tests

Phase 4: Testing (Week 13-16)
-----------------------------

Comprehensive testing:
- Unit tests for calculations
- Integration tests for workflows
- User acceptance testing
- Performance testing
- Migration testing (data)

Phase 5: Incremental Deployment (Week 17+)
------------------------------------------

Consider:
- Parallel run period
- Module-by-module rollout
- Rollback procedures
- User training

Specific Code Migration Tips:

1. C/AL Automation → AL:
   Your NAV code uses Word automation.
   Replace with:
   - BC Word Report Layouts (preferred)
   - External API calls if complex
   - Azure Functions for processing

2. Client-Side Code → AL:
   NAV Page code that ran on client.
   Move to:
   - Page triggers (server-side)
   - Page extensions
   - Control add-ins (if really needed)

3. Record.SETCURRENTKEY → AL:
   NAV: Rec.SETCURRENTKEY(Date, Amount);
   AL:  Rec.SetCurrentKey(Date, Amount);
   
   Note: Case-sensitive in AL!

4. Reports:
   NAV RDLC reports need:
   - AL report object
   - Updated dataset
   - Layout regeneration
   
   Consider: RDL → Word layout migration

5. .NET Interop:
   NAV: DotNet variables
   AL: Check if:
   - Available in AL
   - Use BC API instead
   - Create Azure Function if needed

AI-Assisted Migration Strategy:

Use AI tools effectively:
1. GitHub Copilot:
   - Quick syntax fixes
   - Pattern replacement
   - Boilerplate generation

2. Claude (me!):
   - Complex logic understanding
   - Architecture decisions
   - Refactoring strategies

3. Manual effort:
   - Business logic validation
   - Testing
   - Critical decisions

Realistic Timeline:
- Simple migration: 4-6 months
- Complex migration: 6-12 months
- Very complex: 12-18 months

Budget for:
- 30% automated conversion
- 40% manual refactoring
- 30% testing and validation

Would you like me to:
1. Review specific NAV code samples?
2. Elaborate on any phase?
3. Discuss specific technical challenges?
4. Create a detailed project plan template?
```

## How to Use Claude Effectively

### Workflow

**1. Preparation**:
- Gather all relevant AL code
- Prepare specific questions
- Include context and requirements

**2. Interaction**:
- Paste code into Claude.ai
- Ask clear, specific questions
- Provide examples of what you want

**3. Iterate**:
- Ask follow-up questions
- Clarify unclear points
- Request alternatives
- Explore trade-offs

**4. Implementation**:
- Copy suggested code
- Adapt to your project
- Test thoroughly
- Refine as needed

### Best Practices

**Provide Full Context**:
```
Good:
"I'm building an AL extension for BC v22 that integrates with Shopify.
Here's my current implementation: [paste code]
I need to handle rate limiting from the Shopify API.
Show me the best AL pattern for this."

vs.

Poor:
"How do I handle rate limiting?"
```

**Ask Specific Questions**:
```
Good:
"Review this AL posting codeunit for performance issues,
specifically focusing on database operations."

vs.

Poor:
"Is this good?"
```

**Use for Complex Tasks**:
- Architecture decisions
- Code review of large modules
- Learning complex concepts
- Debugging intricate issues

## How It Differs from Other Agents

### vs. GitHub Copilot

**Claude**:
- ✓ Much larger context window
- ✓ Better at analysis and reasoning
- ✓ More detailed explanations
- ✗ No IDE integration
- ✗ No inline suggestions
- ✗ Manual workflow

**GitHub Copilot**:
- ✓ IDE integration
- ✓ Real-time suggestions
- ✓ Fast workflow
- ✗ Limited context
- ✗ Less analytical

**Use Both**: Copilot for daily coding, Claude for deep analysis

### vs. Cursor

**Claude**:
- ✓ Larger context window
- ✓ Better analytical depth
- ✓ More thoughtful responses
- ✗ No IDE integration
- ✗ Manual copy-paste

**Cursor**:
- ✓ IDE integration
- ✓ Multiple AI models (including Claude!)
- ✓ Direct code editing
- ✗ Smaller context per interaction
- ✗ Different editor

**Note**: Cursor can use Claude as its AI model, giving you Claude's capabilities with IDE integration!

## Access & Pricing

### Claude.ai (Web)

**Free Tier**:
- Limited messages per day
- Claude 3.5 Sonnet access
- Good for occasional use
- No payment required

**Pro Tier** ($20/month):
- 5x more usage
- Priority access
- Claude 3 Opus (most capable model)
- Early feature access

### Claude API

**Pay-per-use**:
- Billed by tokens
- Integration into tools
- Programmatic access
- See Anthropic pricing page

**Link**: [Claude.ai](https://claude.ai) | [Anthropic Pricing](https://www.anthropic.com/pricing)

## Privacy & Security

### What Gets Sent
- Your messages and questions
- Code you paste
- Files you upload
- Conversation history

### Data Usage
- Not used for training (as of current policy)
- Processed for improving responses
- Retained per Anthropic's policy
- Review privacy policy for details

### Best Practices
- Don't paste sensitive data
- Avoid customer information
- Review organization policies
- Use sanitized code examples

## When to Use Claude

### ✓ Ideal For

- **Large code reviews**: Paste entire projects
- **Architecture discussions**: Complex design decisions
- **Learning**: Detailed explanations of AL/BC concepts
- **Problem solving**: Complex scenarios with many variables
- **Migration planning**: NAV to BC conversions
- **Refactoring strategies**: Large-scale code improvements
- **API design**: Thoughtful API architecture

### ⚠ Consider Alternatives

- **Quick completions** → GitHub Copilot
- **IDE integration** → Copilot or Cursor
- **Real-time coding** → Copilot or Cursor
- **Multi-file editing** → Cursor

## Complementary Tools

**Use With**:
- GitHub Copilot for daily coding
- VS Code for development
- AL analyzers for code quality
- Version control for safety

**Workflow Example**:
1. Code with Copilot in VS Code
2. Review architecture with Claude
3. Implement improvements in VS Code
4. Test and validate

## Tips for AL Development

**Provide AL Context**:
```
"I'm working in AL for Business Central version 22.
[Your question]"
```

**Reference BC Concepts**:
```
"Using BC's standard posting pattern..."
"Following BC event subscriber patterns..."
```

**Ask for Alternatives**:
```
"Show me 3 different approaches to this problem,
with pros and cons of each."
```

**Request Explanations**:
```
"Explain this like I'm familiar with C# but new to AL"
```

## Resources

### Official
- [Claude.ai](https://claude.ai)
- [Anthropic Documentation](https://docs.anthropic.com)
- [API Documentation](https://docs.anthropic.com/claude/reference)

### AL Guidelines
- [Effective Prompting](../../getting-started/effective-prompting)
- [Best Practices](../../getting-started/best-practices)
- [Code Review Examples](../../getting-more/code-review)

---

**Next Steps**:
- Try [Claude.ai](https://claude.ai) for free
- Compare with [other AI agents](./)
- Use alongside [GitHub Copilot](github-copilot-agent)

**Questions?** Join [GitHub Discussions](https://github.com/microsoft/alguidelines/discussions)
