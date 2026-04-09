---
title: "Claude Code Agent"
linkTitle: "Claude Code"
weight: 5
description: >
  Anthropic's agentic CLI tool for terminal-native AL development with full file system access and MCP extensibility
---

## Overview

Claude Code is Anthropic's agentic coding tool that runs directly in your terminal. Unlike the Claude web interface, Claude Code has full access to your file system, can run shell commands, make git commits, and edit multiple files autonomously — making it a powerful agent for AL development workflows.

**Developer**: Anthropic  
**Type**: Agentic CLI Tool  
**Primary Use**: Terminal-native agentic development, multi-file editing, code analysis  
**Integration**: Terminal (CLI) + VS Code extension

## What is Claude Code?

Claude Code is an agentic coding assistant that operates in your terminal:

* Reads, writes, and edits files directly in your workspace
* Runs shell commands (build, test, git, etc.)
* Plans and executes multi-step tasks autonomously
* Understands your entire project through file system access
* Extends via Model Context Protocol (MCP) servers
* Integrates with VS Code as an extension
* Maintains project context through CLAUDE.md files

### How It Works

1. **You describe**: What you want to accomplish in natural language
2. **Claude Code plans**: Reads relevant files, understands context
3. **Claude Code acts**: Edits files, runs commands, iterates on errors
4. **You review**: Approve changes, provide feedback, or let it continue

**Key Differentiator**: Unlike chat-based tools where you copy-paste code, Claude Code works directly in your project — reading files, making edits, running the AL compiler, and fixing errors in a loop.

**Example**:

```
You: "Add a Loyalty Points field to the Customer table extension and 
      expose it on the Customer Card page extension. Include proper 
      data classification and a tooltip."

Claude Code:
1. Reads your existing table extension to understand the pattern
2. Adds the field with correct ID, naming, and DataClassification
3. Reads the page extension and adds the field to the layout
4. Adds the tooltip with a Label variable
5. Runs al.build to verify compilation
6. Reports what was done
```

## Key Capabilities

### For AL Development

**Direct File System Access**:

* Reads your AL project files, app.json, and symbols
* Edits multiple files in a single operation
* Creates new AL objects following your existing patterns
* No copy-paste workflow — changes happen in your project

**Shell Command Execution**:

* Runs `al.build` to verify compilation
* Executes git commands for version control
* Runs PowerShell scripts for BC container management
* Can trigger test execution and review results

**Project Context via CLAUDE.md**:

```markdown
# CLAUDE.md (in your project root)

## Project
Customer Loyalty extension for Business Central 26.0.
Object ID range: 50100-50149. Prefix: CLOY.

## Conventions
- Use full table names for record variables (SalesHeader, not SalesHdr)
- All custom fields must include CLOY prefix
- Error messages use Label variables with Comment for translators
- Use SetLoadFields before Get/Find operations

## Architecture
- Table extensions in /src/TableExtensions/
- Page extensions in /src/PageExtensions/
- Codeunits in /src/Codeunits/
```

Claude Code reads this file automatically at the start of every session, giving it your project-specific conventions, ID ranges, and architecture.

**MCP (Model Context Protocol) Extensibility**:

MCP servers extend Claude Code with specialized tools:

* **AL Dependencies MCP**: Look up BC symbol signatures, find references
* **BC Code Intelligence MCP**: Search standard BC source code, find field assignments
* **NAB AL Tools MCP**: Manage .xlf translations, find glossary terms
* **Azure DevOps MCP**: Interact with pipelines and work items
* **Custom MCP servers**: Build your own for team-specific workflows

```json
// .claude/settings.json - configure MCP servers per project
{
  "mcpServers": {
    "al-dependencies": {
      "command": "node",
      "args": ["path/to/al-dependencies-mcp/index.js"],
      "env": { "AL_PACKAGES": ".//.alpackages" }
    }
  }
}
```

> **Note**: The MCP server names above are illustrative examples. See [MCP Tools](/docs/agentic-coding/communityresources/tools/) for available community MCP servers for AL development.

### Agentic Capabilities

**Multi-Step Task Execution**:

Claude Code doesn't just suggest code — it executes complete workflows:

```
You: "Create a test codeunit for the Discount Calculator codeunit. 
      Follow the existing test patterns in the project."

Claude Code:
1. Reads DiscountCalculator.Codeunit.al to understand all procedures
2. Reads existing test files to learn your test patterns
3. Reads app.json for object ID ranges
4. Creates the test codeunit with GIVEN/WHEN/THEN structure
5. Adds Initialize() procedure matching your pattern
6. Creates test procedures for each public procedure
7. Adds appropriate handlers for any UI interactions
8. Runs al.build to verify it compiles
```

**Error Recovery Loop**:

When the AL compiler reports errors, Claude Code reads the diagnostics, understands the issue, fixes it, and retries — without you needing to intervene:

```
Claude Code: Created CustomerLoyalty.Table.al
Claude Code: Running al.build...
Error: AL0049 - The namespace 'Microsoft.Finance.Currency' is not found.
Claude Code: Adding missing 'using Microsoft.Finance.Currency;' declaration
Claude Code: Running al.build...
Build succeeded.
```

**Git Integration**:

* Creates meaningful commit messages
* Stages specific files (not blind `git add .`)
* Creates branches and pull requests via `gh` CLI
* Understands diff context for code review

### Hooks & Automation

Claude Code supports hooks — custom scripts that run automatically at specific points:

```json
// .claude/settings.json
{
  "hooks": {
    "postEdit": [{
      "command": "powershell -File ./scripts/run-codecop.ps1 $FILE",
      "description": "Run CodeCop after file edits"
    }]
  }
}
```

This means you can automatically run AL code analyzers, translation checks, or custom validation after every edit Claude Code makes.

## Strengths for AL Development

### Excellent At

**Multi-File AL Development**:

```
"Create a complete purchase approval workflow extension with:
- Setup table and page
- Approval Entry extension
- Event subscribers for purchase document posting
- Notification codeunit
- Test codeunit"

Claude Code creates all files, following your project's naming 
conventions and ID ranges from CLAUDE.md, and verifies compilation.
```

**Bug Fixing with Context**:

```
"The 'Customer No.' field on my custom Invoice Header isn't being 
populated when creating invoices from contracts."

Claude Code:
1. Searches your codebase for field assignments to find where Customer No. is set
2. Reads the relevant codeunit (not just the obviously-named one)
3. Identifies the root cause
4. Makes the minimal fix in the correct location
5. Verifies compilation
```

**Refactoring & Modernization**:

```
"Refactor this codeunit to:
- Add SetLoadFields before every Get/Find
- Replace hardcoded error strings with Label variables
- Add XML documentation to public procedures
- Add Application Insights telemetry"

Claude Code edits the file in place, making targeted changes 
while preserving all existing logic.
```

**Code Review & Analysis**:

```
"Review the changes on this branch for AL best practices, 
performance issues, and missing error handling."

Claude Code:
- Runs git diff to see all changes
- Reads each modified file for full context
- Analyzes against AL patterns and BC conventions
- Reports issues with specific line references and fixes
```

**Test Generation**:

```
"Generate tests for PostingManager.Codeunit.al following 
our test patterns."

Claude Code reads your existing tests to learn patterns, then 
generates comprehensive tests with proper GIVEN/WHEN/THEN 
structure, handlers for all UI interactions, and Library 
Variable Storage for data passing.
```

### Use With Care

**AL-Specific Training**:

* Strong general coding ability but may not know the latest BC 26+ features
* CLAUDE.md files and MCP servers bridge this gap significantly
* Always verify BC-specific patterns against documentation
* Providing AL reference rules improves output quality

**Long-Running Operations**:

* Terminal sessions can time out on very long operations
* Break large tasks into phases for better results
* Use `/compact` to manage context in long sessions

**Real-Time Completion**:

* Not designed for keystroke-by-keystroke completion
* Better for task-level work than line-by-line suggestions
* Pair with GitHub Copilot for inline completions

## Practical AL Examples

### Creating a Complete Extension Feature

**You**:

```
Add feature telemetry tracking to my sales extension. 
Each time an invoice is posted, log a telemetry event with the 
invoice number and customer. Follow Microsoft's FeatureTelemetry pattern.
```

**Claude Code**:

```al
// 1. Reads existing posting code to find the right event
// 2. Creates or updates the event subscriber:

codeunit 50110 "Sales Posting Telemetry"
{
    Access = Internal;
    SingleInstance = true;

    var
        FeatureTelemetry: Codeunit "Feature Telemetry";

    [EventSubscriber(ObjectType::Codeunit, Codeunit::"Sales-Post", 
        OnAfterPostSalesDoc, '', false, false)]
    local procedure OnAfterPostSalesDoc(
        var SalesHeader: Record "Sales Header";
        var GenJnlPostLine: Codeunit "Gen. Jnl.-Post Line";
        SalesShptHdrNo: Code[20];
        RetRcpHdrNo: Code[20];
        SalesInvHdrNo: Code[20];
        SalesCrMemoHdrNo: Code[20])
    var
        CustomDimensions: Dictionary of [Text, Text];
    begin
        if SalesInvHdrNo = '' then
            exit;

        CustomDimensions.Add('InvoiceNo', SalesInvHdrNo);
        CustomDimensions.Add('CustomerNo', SalesHeader."Sell-to Customer No.");
        FeatureTelemetry.LogUsage(
            '0000ABC',
            SalesExtensionTok,
            InvoicePostedTok,
            CustomDimensions);
    end;

    var
        SalesExtensionTok: Label 'Contoso Sales Extension', Locked = true;
        InvoicePostedTok: Label 'Invoice Posted', Locked = true;
}
```

### Fixing a Bug

**You**:

```
Bug: When currency code is set on a sales order, the custom Discount 
Amount field shows the wrong value — it's not converting currencies.
```

**Claude Code**:

```
Searching for "Ext. Discount Amount" field assignments...
Found in DiscountCalculator.Codeunit.al:CalculateDiscount()

Reading file... The issue is on line 45:
  SalesLine."Ext. Discount Amount" := DiscountBase * DiscountPct;

This doesn't account for currency. The SalesHeader has a Currency Code 
but the calculation uses LCY values directly.

Fix:
```

```al
// Before (bug):
SalesLine."Ext. Discount Amount" := DiscountBase * DiscountPct;

// After (fix - uses BC's currency conversion):
if SalesHeader."Currency Code" = '' then
    SalesLine."Ext. Discount Amount" := DiscountBase * DiscountPct
else
    SalesLine."Ext. Discount Amount" :=
        CurrencyExchangeRate.ExchangeAmtLCYToFCY(
            SalesHeader."Posting Date",
            SalesHeader."Currency Code",
            DiscountBase * DiscountPct,
            SalesHeader."Currency Factor");
```

### Event Subscriber Development

**You**:

```
I need to block posting of sales orders where the customer has 
overdue invoices older than 90 days. Subscribe to the right event.
```

**Claude Code**:

```al
// Searches BC standard code for the correct pre-posting event
// Finds OnBeforePostSalesDoc and creates:

[EventSubscriber(ObjectType::Codeunit, Codeunit::"Sales-Post", 
    OnBeforePostSalesDoc, '', false, false)]
local procedure CheckOverdueInvoicesOnPost(
    var SalesHeader: Record "Sales Header";
    var IsHandled: Boolean)
var
    CustLedgerEntry: Record "Cust. Ledger Entry";
    OverdueDays: Integer;
begin
    if IsHandled then
        exit;

    OverdueDays := 90;
    CustLedgerEntry.SetRange("Customer No.", SalesHeader."Sell-to Customer No.");
    CustLedgerEntry.SetRange("Document Type", CustLedgerEntry."Document Type"::Invoice);
    CustLedgerEntry.SetRange(Open, true);
    CustLedgerEntry.SetFilter("Due Date", '<%1', CalcDate(StrSubstNo('<-%1D>', OverdueDays), WorkDate()));
    CustLedgerEntry.SetLoadFields("Document No.", "Due Date", "Remaining Amount");
    if not CustLedgerEntry.IsEmpty() then
        Error(CustomerHasOverdueInvoicesErr, 
            SalesHeader."Sell-to Customer No.", 
            OverdueDays);
end;

var
    CustomerHasOverdueInvoicesErr: Label 'Customer %1 has invoices overdue by more than %2 days. Posting is blocked.', Comment = '%1 = Customer No., %2 = Number of days';
```

### Translation Workflow

**You** (with NAB AL Tools MCP configured):

```
Find all untranslated texts in this extension and add 
translations for our target language using BC standard terminology.
```

**Claude Code**:

```
1. Calls getTextsToTranslate() — finds 12 untranslated strings
2. For each, looks up BC standard translations for key terms
3. Generates translations following BC terminology conventions
4. Calls saveTranslatedTexts() to update the .xlf file
5. Reports: "Translated 12 texts. 3 flagged for review 
   (domain-specific terms not in BC glossary)."
```

## How It Differs from Other Agents

### vs. Claude (Web/API)

**Claude Code**:

* ✓ Direct file system access — no copy-paste
* ✓ Runs shell commands (build, test, git)
* ✓ Multi-file editing in your actual project
* ✓ MCP extensibility for BC-specific tools
* ✓ CLAUDE.md for persistent project context
* ✓ VS Code extension available
* ✗ Terminal-based (not a chat window)
* ✗ Requires local installation

**Claude (Web)**:

* ✓ No installation needed
* ✓ Good for quick questions
* ✓ File upload for analysis
* ✗ Manual copy-paste workflow
* ✗ No file system access
* ✗ No build/test execution

**Best Practice**: Claude Code for active development, Claude.ai for quick questions and exploration

### vs. GitHub Copilot

**Claude Code**:

* ✓ Agentic multi-step execution
* ✓ Understands entire project (not just open files)
* ✓ Runs commands and reacts to errors
* ✓ MCP tools for BC-specific intelligence
* ✓ CLAUDE.md for project rules
* ✗ No keystroke-level inline completion
* ✗ Separate terminal workflow

**GitHub Copilot**:

* ✓ Real-time inline suggestions
* ✓ Fast for quick completions
* ✓ Seamless VS Code experience
* ✗ Limited to suggestion/accept model
* ✗ No command execution
* ✗ Less context awareness

**Best Practice**: Copilot for inline completion while typing, Claude Code for tasks that span files or need iteration

### vs. Cursor

**Claude Code**:

* ✓ Terminal-native — works in any environment
* ✓ MCP extensibility for specialized tools
* ✓ CLAUDE.md project context files
* ✓ Full shell access and error recovery
* ✓ Works with standard VS Code (via extension)
* ✗ Not a visual editor
* ✗ No integrated diff preview

**Cursor**:

* ✓ Visual editor with inline diffs
* ✓ Multiple AI model selection
* ✓ Composer mode for visual multi-file editing
* ✓ Codebase indexing
* ✓ MCP extensibility (recent addition)
* ✓ Terminal command execution
* ✗ Separate editor from VS Code

**Best Practice**: Cursor for visual development, Claude Code for terminal-native agentic workflows and CI/CD-adjacent tasks

## Setup & Configuration

### Installation

**Via npm (recommended)**:

```bash
npm install -g @anthropic-ai/claude-code
```

**First Run**:

```bash
cd your-al-project
claude
```

Claude Code will prompt you to authenticate with your Anthropic account on first launch.

### VS Code Extension

Claude Code also has a VS Code extension for integrated use:

1. Open VS Code Extensions marketplace
2. Search "Claude Code"
3. Install the extension
4. Use `Ctrl+Shift+P` → "Claude Code" to open the panel
5. Or use the terminal directly within VS Code

### Project Setup for AL Development

**1. Create CLAUDE.md**:

```markdown
# CLAUDE.md

## Project
Extension: Contoso Loyalty (ID range: 50100-50149, prefix: CLOY)
BC Version: 26.0
Runtime: 14.0

## Conventions
- Record variables: full table name (SalesHeader, not SalesHdr)
- Custom fields: always prefix with CLOY
- Error strings: Label variables with Comment for translator context
- Performance: SetLoadFields before Get/Find, CalcSums over loops
- Events: IntegrationEvent with var IsHandled: Boolean pattern

## Build
- Compile: al.build (Ctrl+Shift+B in VS Code)
- Test: Run via BC test runner or PowerShell

## File Structure
- src/Tables/, src/Pages/, src/Codeunits/, src/Enums/
- test/TestCodeunits/
```

**2. Configure MCP Servers** (optional but recommended):

```json
// .claude/settings.json
{
  "mcpServers": {
    "al-dependencies": {
      "command": "node",
      "args": ["./tools/al-dependencies-mcp/index.js"]
    },
    "bc-code-search": {
      "command": "node", 
      "args": ["./tools/bc-code-search-mcp/index.js"]
    }
  }
}
```

**3. Add .claude/ to .gitignore** (for personal settings):

```
# Claude Code personal settings
.claude/settings.local.json
```

### Useful Slash Commands

| Command | Description |
|---------|-------------|
| `/compact` | Compress conversation context |
| `/clear` | Start fresh conversation |
| `/help` | Show available commands |
| `/init` | Generate a CLAUDE.md for your project |

## Best Practices

### Writing Effective Prompts

**Provide Context**:

```
Good:
"In src/Codeunits/DiscountCalculator.Codeunit.al, fix the currency 
conversion bug in CalculateDiscount. The SalesHeader."Currency Code" 
is being ignored when computing the discount amount on line 45."

Poor:
"Fix the currency bug"
```

**Be Specific About Scope**:

```
Good:
"Add SetLoadFields to all Get/Find calls in 
PostingManager.Codeunit.al. Only load the fields actually 
used in each procedure."

Poor:
"Optimize the code"
```

**Use CLAUDE.md for Recurring Rules**:

Don't repeat project conventions in every prompt — put them in CLAUDE.md once and Claude Code reads them automatically.

### Leverage MCP Servers

For AL development, MCP servers are a force multiplier:

* **Before writing new code**: Use code search to find if the concept already exists
* **Before adding fields**: Search for existing fields that model the same concept
* **When subscribing to events**: Search BC standard code for the right event
* **For translations**: Use NAB AL Tools to manage .xlf files programmatically

### Review Workflow

```
1. Claude Code makes changes
2. Review the diff (git diff or VS Code source control)
3. Run AL build to verify
4. Run tests
5. Ask Claude Code to fix any issues
6. Commit when satisfied
```

### Context Management

For long sessions, manage context to keep Claude Code effective:

* Use `/compact` when context grows large
* Break large features into focused tasks
* Use CLAUDE.md for information that should persist across sessions
* Start new sessions for unrelated tasks

## Pricing

**Claude Pro** ($20/month):

* Includes Claude Code access
* Good for individual developers
* Standard usage limits

**Claude Max** ($100/month or $200/month):

* Higher usage limits for Claude Code
* Ideal for heavy daily use
* Priority access

**API-Based**:

* Pay-per-token for programmatic use
* Configurable spend limits
* Team usage tracking

**Link**: [Claude Code](https://claude.ai/claude-code) | [Anthropic Pricing](https://www.anthropic.com/pricing)

## Privacy & Security

### What Gets Sent

* File contents that Claude Code reads
* Shell command outputs
* Your conversation messages
* Project structure information

### What Stays Local

* Files Claude Code doesn't read are never sent
* CLAUDE.md lets you control what context is shared
* MCP servers run locally — their tool output is sent to the API for reasoning
* Git credentials are never transmitted

### Best Practices

* Don't include secrets in files Claude Code reads
* Use `.claudeignore` to exclude sensitive directories
* Review organization AI usage policies
* Use CLAUDE.md to document what should not be shared

## When to Use Claude Code

### Ideal For

* **Multi-file AL development**: Creating complete features across tables, pages, codeunits
* **Bug fixing**: Searching code, finding root cause, applying minimal fix
* **Refactoring**: Updating patterns across an entire extension
* **Test generation**: Creating test codeunits that follow your existing patterns
* **Code review**: Analyzing changes for best practices and issues
* **Translation workflows**: Managing .xlf files with MCP tools
* **CI/CD adjacent tasks**: Git operations, build verification, PR creation

### Consider Alternatives

* **Inline code completion** → GitHub Copilot (real-time as you type)
* **Visual multi-file editing** → Cursor (see diffs visually)
* **Quick questions** → Claude.ai or Copilot Chat
* **No local installation** → Claude.ai web interface

## Complementary Tools

**Use With**:

* [GitHub Copilot](/docs/agentic-coding/communityresources/agents/github-copilot-agent/) - For inline completions while typing
* [AL Language Extension](https://marketplace.visualstudio.com/items?itemName=ms-dynamics-smb.al) - For AL language support in VS Code
* AL Code Analyzers - CodeCop, AppSourceCop, etc. for quality checks
* MCP Servers - For BC-specific intelligence (see [Tools](/docs/agentic-coding/communityresources/tools/))

**Example Daily Workflow**:

1. Open VS Code with Copilot for inline completion
2. For a complex task, open terminal and run `claude`
3. Describe the task — Claude Code reads files, makes changes, builds
4. Review the diff in VS Code source control
5. Ask Claude Code to fix any issues or refine
6. Use Copilot Chat for quick questions while coding
7. Commit and create PR through Claude Code

## Resources

### Official

* [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code/overview)
* [Claude Code GitHub](https://github.com/anthropics/claude-code)
* [MCP Specification](https://modelcontextprotocol.io/)
* [CLAUDE.md Guide](https://docs.anthropic.com/en/docs/claude-code/memory)

### AL Guidelines

* [Setup Guide](/docs/agentic-coding/gettingstarted/setup/)
* [Effective Prompting](/docs/agentic-coding/gettingstarted/effective-prompting/)
* [Best Practices](/docs/agentic-coding/gettingstarted/best-practices/)
* [MCP Tools](/docs/agentic-coding/communityresources/tools/)

### Community

* [Claude Code Discord](https://discord.gg/claudeai)
* [MCP Server Registry](https://github.com/modelcontextprotocol/servers)

---

**Next Steps**:

* Install [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview)
* Create a CLAUDE.md for your AL project
* Configure [MCP servers](/docs/agentic-coding/communityresources/tools/) for BC intelligence
* Compare with other [AI Agents](/docs/agentic-coding/communityresources/agents/)

**Questions?** Join [GitHub Discussions](https://github.com/microsoft/alguidelines/discussions)
