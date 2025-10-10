---
title: "Setting Up Your Environment"
linkTitle: "Setup"
weight: 2
description: >
  Configure your development environment for optimal AI-assisted AL development
---

## Overview

To get the most out of AI-powered coding assistance for AL development, you'll need to set up your environment properly. This guide covers the essential tools and configurations.

## Prerequisites

### Required
- **Visual Studio Code**: The primary IDE for AL development
- **AL Language Extension**: Microsoft's official AL extension for VS Code
- **AI Coding Assistant**: One of the following:
  - GitHub Copilot
  - GitHub Copilot Chat
  - Other compatible AI assistants

### Recommended
- **Git**: For version control and change tracking
- **AL Test Runner**: For running and managing tests
- **Business Central Docker Container**: For local development and testing

## Installing GitHub Copilot

GitHub Copilot is one of the most popular AI assistants for coding:

1. **Sign up for GitHub Copilot**
   - Visit [GitHub Copilot](https://github.com/features/copilot)
   - Choose a subscription plan (free trial available)

2. **Install the VS Code Extension**
   - Open VS Code
   - Go to Extensions (Ctrl+Shift+X)
   - Search for "GitHub Copilot"
   - Install both:
     - GitHub Copilot
     - GitHub Copilot Chat

3. **Sign In**
   - Click "Sign in to GitHub" when prompted
   - Authorize the extension

## Configuring VS Code for AL + AI

### Workspace Settings

Add these settings to your workspace `.vscode/settings.json`:

```json
{
  // AL Language settings
  "al.enableCodeAnalysis": true,
  "al.codeAnalyzers": ["${CodeCop}", "${PerTenantExtensionCop}", "${UICop}"],
  
  // GitHub Copilot settings
  "github.copilot.enable": {
    "*": true,
    "al": true
  },
  
  // Editor settings for better AI integration
  "editor.inlineSuggest.enabled": true,
  "editor.quickSuggestions": {
    "other": true,
    "comments": true,
    "strings": true
  }
}
```

### AL Project Structure

Organize your AL project for better AI context:

```
MyExtension/
├── .vscode/
│   ├── settings.json
│   └── launch.json
├── src/
│   ├── Tables/
│   ├── Pages/
│   ├── Codeunits/
│   ├── Reports/
│   └── ...
├── test/
│   └── ...
├── app.json
└── README.md
```

Clear folder organization helps AI assistants understand your project structure and provide more relevant suggestions.

## Optimizing Context for AI

AI assistants work better when they have good context. Here's how to provide it:

### 1. Use Descriptive File Names
```
❌ Page1.al
✅ CustomerListPage.al

❌ Cod50100.al
✅ SalesOrderProcessor.codeunit.al
```

### 2. Maintain a Good README
Create a `README.md` in your project root with:
- Project purpose and overview
- Key features and functionality
- Naming conventions
- Architecture decisions

### 3. Use XML Documentation
Document your procedures and functions:
```al
/// <summary>
/// Calculates the total amount for a sales order including tax
/// </summary>
/// <param name="SalesHeader">The sales header record</param>
/// <returns>The total amount including tax</returns>
procedure CalculateTotalWithTax(var SalesHeader: Record "Sales Header"): Decimal
```

### 4. Keep Related Code Together
Place related functionality in the same files or nearby files. AI assistants can see open files and nearby code.

## Testing Your Setup

To verify everything is working:

1. **Open an AL file** in your project
2. **Start typing** a procedure declaration
3. **Check for suggestions** - You should see inline suggestions appear
4. **Open Copilot Chat** (if using GitHub Copilot)
   - Press Ctrl+Shift+I (or Cmd+Shift+I on Mac)
   - Try asking: "Explain this AL code"

## Recommended Extensions

Install these VS Code extensions to complement your AI assistant:

- **AL Language**: Microsoft's official AL extension (required)
- **AL Object Designer**: Navigate AL objects easily
- **AL Code Outline**: View code structure
- **AL Test Runner**: Run and manage AL tests
- **AL Variable Helper**: Manage variable declarations
- **GitLens**: Enhanced git integration

## Workspace Best Practices

### Open Relevant Files
- Keep related files open in tabs
- AI assistants can use open files for context

### Use Multi-Root Workspaces (When Appropriate)
If you have dependencies or multiple related projects:
```json
{
  "folders": [
    { "path": "./MyMainExtension" },
    { "path": "./MyDependencyExtension" }
  ]
}
```

### Organize by Feature
Consider organizing code by business feature rather than object type for complex projects:
```
src/
├── SalesOrderProcessing/
│   ├── SalesOrder.table.al
│   ├── SalesOrderPage.page.al
│   ├── SalesOrderProcessor.codeunit.al
├── CustomerManagement/
│   └── ...
```

## Security and Privacy Considerations

### What Gets Sent to AI Services
- Code snippets from your workspace
- Currently open files
- Your prompts and questions

### What You Should NOT Include
- Sensitive credentials or passwords
- Customer data
- Proprietary business logic (if restricted)

### Best Practices
- Review your organization's AI usage policy
- Use `.gitignore` and `.copilotignore` files appropriately
- Be mindful of what code is in your workspace
- Consider using GitHub Copilot for Business for enterprise controls

## Troubleshooting

### AI Suggestions Not Appearing
- Verify the AI extension is installed and enabled
- Check you're signed in to your AI service
- Ensure `editor.inlineSuggest.enabled` is true
- Restart VS Code

### Poor Quality Suggestions
- Improve code context (better file names, comments)
- Open related files for more context
- Use more descriptive variable and function names
- Add XML documentation comments

### Performance Issues
- Close unnecessary tabs/files
- Disable AI for specific file types if needed
- Check your system resources

## Next Steps

Now that your environment is set up:
- Learn [effective prompting techniques](../effective-prompting)
- Review [best practices](../best-practices) for AI-assisted development
- Try the [practical examples](../../getting-more) to see AI assistance in action
