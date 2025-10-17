---
title: "Cursor Agent"
linkTitle: "Cursor"
weight: 4
description: >
  AI-first code editor with integrated AI assistance and multi-file editing for AL development
---

## Overview

Cursor is an AI-first code editor built on VS Code that integrates AI deeply into every aspect of development. It provides inline suggestions, chat assistance, and advanced features like Composer mode for multi-file editing, making it a powerful tool for AL development.

**Developer**: Anysphere Inc.  
**Type**: AI-Integrated Code Editor  
**Primary Use**: Complete AI-assisted development environment  
**Integration**: Native (is a code editor)

## What is Cursor?

Cursor is a fork of Visual Studio Code with integrated AI capabilities:
- Native AI code completion (like Copilot)
- Built-in AI chat (like Copilot Chat)
- Composer mode for multi-file editing
- Codebase indexing for better context
- Multiple AI model support
- All VS Code extensions work

### Key Differentiator

Unlike tools that add AI to VS Code, Cursor **is** an AI-first editor built from the ground up with AI integration.

**Think of it as**: VS Code + GitHub Copilot + Advanced AI Features + Better Context Understanding

## Key Features

### 1. Tab Completion (Like Copilot)

Real-time AI suggestions as you type:
```al
// Type a comment
/// Validate customer credit limit

// Cursor suggests complete procedure
procedure ValidateCreditLimit(CustomerNo: Code[20]): Boolean
var
    Customer: Record Customer;
begin
    // Full implementation suggested
end;
```

**Plus**:
- Faster than Copilot
- Better context awareness
- Multiple AI models available

### 2. Chat Interface (Like Copilot Chat)

Built-in AI chat in the sidebar:
- Ask questions about code
- Get explanations
- Request code generation
- Debug issues

**Plus**:
- Can use multiple AI models (GPT-4, Claude, etc.)
- Better codebase understanding
- More context awareness

### 3. Cmd+K (Inline Chat)

Quick inline AI assistance:
- Press `Cmd+K` (Mac) or `Ctrl+K` (Windows)
- Ask questions or request changes
- AI suggests edits inline
- Accept, reject, or modify

**Example**:
```al
// Select code, press Cmd+K, type:
"Add error handling and telemetry"

// Cursor shows diff with changes
// Accept to apply
```

### 4. Composer Mode ⭐

**Most Powerful Feature**: Multi-file editing with AI

- Create/edit multiple files simultaneously
- AI understands file relationships
- Generates complete features
- Handles complex refactoring

**Example**:
```
Press Cmd+I (Composer)
Type: "Add a loyalty points system with table, page, and codeunit"

Cursor creates:
- Table 50100 "Loyalty Points Entry"
- Page 50100 "Loyalty Points List"
- Page 50101 "Loyalty Points Card"
- Codeunit 50100 "Loyalty Points Manager"

All properly connected and following AL patterns
```

### 5. Codebase Indexing

Cursor indexes your entire workspace:
- AI understands your project structure
- References existing code
- Follows your patterns
- Suggests consistent code

**Benefit for AL**:
- Knows your table structures
- Understands your naming conventions
- Follows your coding patterns
- References your existing codeunits

### 6. Multiple AI Models

Choose your AI model:
- GPT-4 (OpenAI)
- GPT-4 Turbo
- Claude 3.5 Sonnet (Anthropic)
- Claude 3 Opus
- (More being added)

**Why it matters**:
- Different models have different strengths
- Switch based on task
- Try multiple for comparison
- Use best for each scenario

## Strengths for AL Development

### ✓ Exceptional At

**Multi-File Projects**:
```
Composer: "Create a complete order processing module with:
- Order Header and Line tables
- Card and List pages
- Processing codeunit
- Validation codeunit
- Test codeunit
Follow AL best practices"

Cursor creates all files properly structured and connected.
```

**Large Refactoring**:
```
"Refactor this project to:
- Add telemetry to all procedures
- Standardize error handling
- Add XML documentation
- Update to use new AL patterns"

Cursor makes changes across all relevant files.
```

**Project Generation**:
```
"Create a BC extension for customer surveys with:
- Setup tables
- Survey and response tables
- Pages for all tables
- Email sending logic
- API for mobile access"

Cursor scaffolds entire extension.
```

**Codebase Understanding**:
- Knows your entire project
- References existing patterns
- Follows your conventions
- Suggests consistent code

### ⚠ Consider Limitations

**Learning Curve**:
- Different from VS Code workflow
- New shortcuts to learn
- Composer mode takes practice
- More features to understand

**Cost**:
- $20/month (more than Copilot's $10)
- No free tier for individuals
- Worth it for heavy AI use

**AL-Specific Training**:
- Not specifically trained on AL
- Requires good AL context
- May need explicit AL patterns
- Works best with symbols downloaded

## Practical AL Examples

### Composer Mode: Complete Feature

**Command** (in Composer, `Cmd+I`):
```
Create a complete customer discount management system:

Tables:
- Discount Group Setup (code, description, percentage)
- Customer Discount Assignment (customer no, discount group)

Pages:
- List and Card pages for each table

Codeunit:
- Calculate discount based on customer's group
- Apply discount to sales lines
- Event subscribers for sales line validation

Include:
- Proper AL naming conventions
- XML documentation
- Error handling
- Data classification
```

**Cursor Creates** (all files):

```
Created Files:
✓ Tables/DiscountGroupSetup.Table.al
✓ Tables/CustomerDiscountAssignment.Table.al
✓ Pages/DiscountGroupSetupList.Page.al
✓ Pages/DiscountGroupSetupCard.Page.al
✓ Pages/CustomerDiscountAssignmentList.Page.al
✓ Codeunits/DiscountManager.Codeunit.al

All files properly structured with:
- AL best practices
- XML documentation
- Event subscribers
- Error handling
```

### Chat: Code Review

**You** (in Chat):
```
Review this codeunit for performance and best practices:

[Paste codeunit]

Specifically check:
- Database operations
- AL patterns
- Error handling
- Code analyzers compliance
```

**Cursor Analyzes**:
```
Found several issues:

1. Performance Issue (Lines 45-52):
   Multiple FindSet without SetLoadFields
   
   Current:
   SalesLine.SetRange("Document No.", OrderNo);
   if SalesLine.FindSet() then
   
   Recommended:
   SalesLine.SetRange("Document No.", OrderNo);
   SalesLine.SetLoadFields(Quantity, "Unit Price", Amount);
   if SalesLine.FindSet() then
   
2. Missing Error Handling (Lines 67-70):
   No try-catch for posting operation
   
   [Shows specific improvements]

3. CodeCop Violations:
   - Procedure names don't follow AL naming
   - Missing XML documentation
   
   [Lists specific fixes needed]

Would you like me to apply these fixes?
```

### Inline Edit (Cmd+K)

**Select procedure, press Cmd+K**:
```
"Add Application Insights telemetry to track execution time and parameters"
```

**Cursor Shows Diff**:
```al
- procedure ProcessOrder(OrderNo: Code[20])
+ procedure ProcessOrder(OrderNo: Code[20])
  var
      SalesHeader: Record "Sales Header";
+     Telemetry: Codeunit Telemetry;
+     CustomDimensions: Dictionary of [Text, Text];
+     StartTime: DateTime;
  begin
+     StartTime := CurrentDateTime;
+     CustomDimensions.Add('OrderNo', OrderNo);
+     
+     Telemetry.LogMessage('ORDER-001', 'Processing order started',
+         Verbosity::Normal, DataClassification::SystemMetadata,
+         TelemetryScope::ExtensionPublisher, CustomDimensions);
+     
      // Existing code...
+     
+     CustomDimensions.Add('Duration', Format(CurrentDateTime - StartTime));
+     Telemetry.LogMessage('ORDER-002', 'Processing order completed',
+         Verbosity::Normal, DataClassification::SystemMetadata,
+         TelemetryScope::ExtensionPublisher, CustomDimensions);
  end;
```

Accept with `Cmd+Enter`, reject with `Cmd+Backspace`

### Codebase Chat

**You**:
```
@codebase How is customer validation currently implemented in this project?
```

**Cursor** (searches entire project):
```
Found customer validation in 3 places:

1. Codeunit 50100 "Customer Validator" (Lines 23-45):
   - Main validation logic
   - Checks blocked status
   - Validates credit limit
   
2. Table Extension 50100 "Customer Extension" (Lines 15-20):
   - OnValidate trigger for custom field
   
3. Event Subscriber in Codeunit 50105 (Lines 67-80):
   - Subscribes to Customer OnAfterValidate
   - Adds additional custom validation

Recommendation: Consolidate validation logic into single codeunit
for better maintainability.

[Shows code snippets from each location]
```

## How It Differs from Other Agents

### vs. GitHub Copilot

**Cursor**:
- ✓ Multiple AI models (including GPT-4)
- ✓ Composer mode (multi-file editing)
- ✓ Better codebase understanding
- ✓ More advanced features
- ✗ Higher cost ($20 vs $10)
- ✗ Different editor (learning curve)

**Copilot**:
- ✓ Lower cost
- ✓ Standard VS Code
- ✓ Familiar workflow
- ✗ Single AI model
- ✗ No composer mode
- ✗ Less context awareness

### vs. Claude

**Cursor**:
- ✓ IDE integration
- ✓ Multi-file editing
- ✓ Direct code application
- ✓ Can use Claude as AI model!
- ✗ Smaller context per interaction

**Claude**:
- ✓ Larger context window
- ✓ Better for analysis
- ✓ Web interface
- ✗ No IDE integration
- ✗ Manual copy-paste

**Note**: Cursor can use Claude as its AI model, giving you best of both!

### vs. VS Code + Extensions

**Cursor**:
- ✓ Native AI integration
- ✓ Optimized for AI workflow
- ✓ Advanced features
- ✓ All VS Code extensions work
- ✗ Different app (not VS Code)
- ✗ Subscription required

**VS Code + Copilot**:
- ✓ Standard VS Code
- ✓ Familiar environment
- ✓ Established workflow
- ✗ Less AI integration
- ✗ Fewer AI features

## Setup & Configuration

### Installation

1. **Download Cursor**
   - Visit [cursor.sh](https://cursor.sh)
   - Download for your OS
   - Install application

2. **Sign Up**
   - Create account
   - Choose subscription plan
   - Verify email

3. **Configure AL Development**
   - Install AL Language extension
   - Import VS Code settings (optional)
   - Download BC symbols
   - Open your AL project

### Migrating from VS Code

**Import Settings**:
```
Cursor > Settings > Import Settings from VS Code
```

**Your Extensions**:
- All VS Code extensions work
- Install AL Language
- Install AL Object Designer
- Install other AL tools

**Keyboard Shortcuts**:
- Most VS Code shortcuts work
- Learn Cursor-specific shortcuts:
  - `Cmd+K`: Inline edit
  - `Cmd+L`: Chat
  - `Cmd+I`: Composer

### Optimizing for AL

**Workspace Setup**:
- Keep `app.json` well-configured
- Download symbols first
- Organize files clearly
- Use descriptive naming

**AI Model Selection**:
- GPT-4 for general coding
- Claude for analysis
- Experiment to find preference

## Best Practices

### Using Composer Mode

**Clear Instructions**:
```
Good:
"Create AL customer loyalty system with:
- Tier setup table (code, name, min points, discount %)
- Customer points table (customer no, points, tier code)
- List and card pages for both
- Codeunit to calculate and assign tiers
- Event subscriber to update on purchase
Follow AL naming conventions and add XML docs"

Poor:
"Make a loyalty system"
```

**Iterative Development**:
1. Start with basic structure
2. Review generated files
3. Ask for refinements
4. Add features incrementally

### Using Chat Effectively

**Reference Files**:
```
@filename.al What does this procedure do?
@codebase How is posting handled in this project?
```

**Specific Questions**:
```
"Review CustomerProcessor.codeunit.al for:
- Performance issues
- AL best practices
- Missing error handling"
```

### Using Inline Edit

**Targeted Changes**:
- Select specific code
- Request specific improvements
- Review diff carefully
- Accept or modify

## Pricing

**Pro Plan**: $20/month
- Unlimited AI completions
- Unlimited chat
- Composer mode
- All AI models
- Priority support

**Business Plan**: Custom pricing
- Team features
- Organization management
- Usage analytics
- Enhanced security

**Free Trial**: 14 days (typically)

**Link**: [Cursor Pricing](https://cursor.sh/pricing)

## Privacy & Security

### What Gets Sent
- Code you're working on
- Files in your workspace
- Chat messages
- User interactions

### Privacy Controls
- Can disable AI features
- Control what's indexed
- Configure model usage
- Review privacy settings

### Best Practices
- Don't include sensitive data
- Review organization policies
- Use privacy mode when needed
- Understand data handling

## When to Use Cursor

### ✓ Ideal For

- **New Projects**: Build from scratch with AI
- **Large Refactoring**: Multi-file changes
- **Learning**: Explore AL patterns
- **Rapid Development**: Build features quickly
- **Experimentation**: Try different approaches
- **Team Development**: Consistent patterns

### ⚠ Consider Alternatives

- **Quick edits** → GitHub Copilot faster
- **Just need VS Code** → Stick with Copilot
- **Budget constrained** → Copilot cheaper
- **Prefer standard tools** → VS Code + Copilot

## Practical Workflows

### Starting New Extension

1. **Composer Mode**: Generate project structure
2. **Chat**: Refine and improve
3. **Inline Edit**: Add features
4. **Tab**: Complete code quickly

### Refactoring Existing Code

1. **Chat**: "Analyze this project for improvements"
2. **Review**: Understand suggestions
3. **Composer**: Apply multi-file changes
4. **Inline**: Fix specific issues

### Learning AL Patterns

1. **Generate example** with Composer
2. **Ask Chat** to explain
3. **Experiment** with variations
4. **Apply** to real project

## Complementary Tools

**Use With**:
- AL Language extension (required)
- AL analyzers for quality
- Git for version control
- BC symbols for context

**Workflow**:
1. Cursor for development
2. AL analyzers for validation
3. Git for safety
4. Claude (via Cursor) for analysis

## Tips for AL Development

**Provide AL Context**:
```
"Generate AL code for Business Central v22..."
"Follow AL naming conventions..."
"Use BC standard posting patterns..."
```

**Use Codebase Context**:
```
@codebase Reference existing table structures
@CustomerTable.al Follow this naming pattern
```

**Leverage Models**:
- GPT-4 for code generation
- Claude for analysis
- Try both for comparison

**Iterate**:
- Generate basic structure
- Review and refine
- Add complexity gradually
- Test thoroughly

## Resources

### Official
- [Cursor Website](https://cursor.sh)
- [Cursor Documentation](https://docs.cursor.sh)
- [Community Discord](https://discord.gg/cursor)

### AL Guidelines
- [Effective Prompting](../../getting-started/effective-prompting)
- [Best Practices](../../getting-started/best-practices)
- [Getting More Examples](../../getting-more)

## Learning Cursor

### Start Simple
1. Try tab completion (like Copilot)
2. Use chat for questions
3. Experiment with Cmd+K
4. Practice Composer on small tasks

### Progress to Advanced
1. Multi-file projects with Composer
2. Codebase-wide refactoring
3. Multiple AI model usage
4. Advanced keyboard shortcuts

### Master Features
1. Understand when to use each mode
2. Optimize prompts for better results
3. Integrate into daily workflow
4. Share patterns with team

---

**Next Steps**:
- Download [Cursor](https://cursor.sh)
- Try free trial
- Compare with [other AI agents](./)
- Use alongside your existing tools

**Questions?** Join [GitHub Discussions](https://github.com/microsoft/alguidelines/discussions)
