---
title: "Tools & Extensions"
linkTitle: "Tools"
weight: 3
description: >
  VS Code extensions and tools that enhance AI-assisted AL development
---

## Overview

This page catalogs VS Code extensions, utilities, and tools that complement AI coding assistants for Business Central development.

## AI Coding Assistants

### GitHub Copilot
**Publisher**: GitHub  
**Type**: AI Code Completion

**Features**:
- Inline code suggestions
- Chat interface for questions
- Multi-file context awareness
- Code explanation and refactoring

**AL-Specific Benefits**:
- Understands AL syntax and patterns
- Suggests Business Central APIs
- Generates AL-compliant code
- Helps with event subscribers and patterns

**Installation**:
```
Extension ID: GitHub.copilot
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)

**Pricing**: Subscription required (free for students/open source maintainers)

---

### GitHub Copilot Chat
**Publisher**: GitHub  
**Type**: AI Chat Assistant

**Features**:
- Interactive chat in VS Code
- Code explanations
- Debugging assistance
- Inline code chat

**Best For**:
- Asking questions about AL code
- Getting explanations of Business Central patterns
- Debugging assistance
- Code refactoring discussions

**Installation**:
```
Extension ID: GitHub.copilot-chat
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat)

**Requires**: GitHub Copilot subscription

---

## AL Development Extensions

### AL Language
**Publisher**: Microsoft  
**Type**: Language Support

**Why It's Essential**:
- Core AL language support
- Syntax highlighting
- IntelliSense
- Code analysis

**AI Integration**:
- Provides context for AI suggestions
- Enables AL-aware completions
- Works with AL code analyzers

**Installation**:
```
Extension ID: ms-dynamics-smb.al
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-dynamics-smb.al)

**Required**: Yes, for AL development

---

### AL Code Outline
**Publisher**: Community  
**Type**: Code Navigation

**Features**:
- Visual code structure
- Quick navigation
- Object overview
- Procedure list

**AI Enhancement**:
- Helps AI understand code structure
- Easier to reference specific procedures in prompts
- Better context for AI suggestions

**Installation**:
```
Extension ID: davidfeldhoff.al-code-outline
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=davidfeldhoff.al-code-outline)

---

### AL Object Designer
**Publisher**: Community  
**Type**: Object Browser

**Features**:
- Browse all AL objects
- Search functionality
- Quick navigation
- Object creation

**AI Enhancement**:
- Quickly find objects to reference in prompts
- Better workspace navigation
- Context for AI when working with multiple objects

**Installation**:
```
Extension ID: martonsagi.al-object-designer
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=martonsagi.al-object-designer)

---

### AL Variable Helper
**Publisher**: Community  
**Type**: Variable Management

**Features**:
- Auto-declare variables
- Variable suggestions
- Type inference
- Quick fixes

**AI Complement**:
- Use with AI-generated code to clean up variables
- Auto-complete variables from AI suggestions
- Streamline AI-generated code

**Installation**:
```
Extension ID: rasmus.al-var-helper
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=rasmus.al-var-helper)

---

### AL Test Runner
**Publisher**: Community  
**Type**: Test Framework

**Features**:
- Run AL tests from VS Code
- Test results visualization
- Code coverage
- Test debugging

**AI Use Cases**:
- Test AI-generated code
- Verify AI refactoring
- Run tests for AI-written procedures

**Installation**:
```
Extension ID: jamespearson.al-test-runner
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=jamespearson.al-test-runner)

---

## Code Quality Tools

### AL CodeCop
**Type**: Code Analyzer  
**Included In**: AL Language Extension

**What It Does**:
- Enforces AL coding standards
- Identifies best practice violations
- Provides code fixes

**AI Integration**:
- Review AI-generated code
- Ensure AI follows standards
- Auto-fix AI code issues

**Usage**: Enabled in `app.json`:
```json
"codeAnalyzers": ["${CodeCop}"]
```

---

### Business Central Performance Toolkit
**Publisher**: Microsoft  
**Type**: Performance Testing

**Features**:
- Performance scenario testing
- Load testing
- Performance metrics

**AI Use Cases**:
- Test performance of AI-generated code
- Validate AI optimization suggestions
- Benchmark before/after AI refactoring

**Link**: [GitHub Repository](https://github.com/microsoft/BusinessCentralPerfToolkit)

---

## Documentation Tools

### AL XML Documentation
**Publisher**: Community  
**Type**: Documentation Generator

**Features**:
- Generate XML documentation
- Documentation snippets
- Template support

**AI Enhancement**:
- Complement AI-generated docs
- Standardize documentation format
- Quick doc generation

**Installation**:
```
Extension ID: andrzejzwierzchowski.al-xml-doc
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=andrzejzwierzchowski.al-xml-doc)

---

### Markdown All in One
**Publisher**: Community  
**Type**: Markdown Editor

**Features**:
- Markdown preview
- Keyboard shortcuts
- Auto-completion
- Table formatting

**AI Use Cases**:
- Edit AI-generated README files
- Format AI-generated documentation
- Create documentation with AI assistance

**Installation**:
```
Extension ID: yzhang.markdown-all-in-one
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)

---

## Productivity Enhancers

### GitLens
**Publisher**: GitKraken  
**Type**: Git Enhancement

**Features**:
- Code authorship
- Commit history
- Blame annotations
- Git visualization

**AI Context**:
- See who wrote code (human vs AI-assisted)
- Track AI-generated code changes
- Review AI code evolution

**Installation**:
```
Extension ID: eamodio.gitlens
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)

---

### Code Spell Checker
**Publisher**: Street Side Software  
**Type**: Spell Checker

**Features**:
- Spell checking in code
- Supports AL/BC terms
- Custom dictionaries

**AI Complement**:
- Catch spelling errors in AI-generated code
- Improve AI-generated documentation
- Ensure consistent terminology

**Installation**:
```
Extension ID: streetsidesoftware.code-spell-checker
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker)

---

### Better Comments
**Publisher**: Community  
**Type**: Comment Enhancement

**Features**:
- Color-coded comments
- TODO highlighting
- Comment categories

**AI Use Cases**:
- Mark AI-generated code sections
- Highlight AI code for review
- Track AI TODOs

**Installation**:
```
Extension ID: aaron-bond.better-comments
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments)

---

## Specialized Tools

### AL Toolbox
**Publisher**: Community  
**Type**: Utility Collection

**Features**:
- Code snippets
- Quick actions
- AL utilities
- Productivity commands

**AI Enhancement**:
- Complements AI code generation
- Quick fixes for AI code
- Utilities for AI-generated projects

**Installation**:
```
Extension ID: BartPermentier.al-toolbox
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=BartPermentier.al-toolbox)

---

### AZ AL Dev Tools
**Publisher**: Community  
**Type**: Development Tools

**Features**:
- Code generators
- AL object wizards
- Development utilities
- Code analysis

**AI Complement**:
- Generate boilerplate for AI to enhance
- Analyze AI-generated code structure
- Quick object creation

**Installation**:
```
Extension ID: andrzejzwierzchowski.az-al-dev-tools-vscode
```

**Link**: [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=andrzejzwierzchowski.az-al-dev-tools-vscode)

---

## Recommended Extension Packs

### Essential AL + AI Setup
Minimal setup for AI-assisted AL development:

1. **AL Language** (Microsoft)
2. **GitHub Copilot** (GitHub)
3. **GitHub Copilot Chat** (GitHub)
4. **AL Code Outline** (Community)
5. **AL Object Designer** (Community)

### Complete AL Development Suite
Comprehensive setup:

- All from Essential Setup
- AL Variable Helper
- AL Test Runner
- AL XML Documentation
- GitLens
- Code Spell Checker
- Better Comments

### Team Development
Recommended for development teams:

- Complete Suite extensions
- AL Toolbox
- AZ AL Dev Tools
- Business Central Performance Toolkit

## Configuration Tips

### Optimizing for AI
Add to your `settings.json`:

```json
{
  // Enable GitHub Copilot for AL
  "github.copilot.enable": {
    "*": true,
    "al": true
  },
  
  // Better inline suggestions
  "editor.inlineSuggest.enabled": true,
  "editor.suggest.showMethods": true,
  "editor.suggest.showFunctions": true,
  
  // Code analysis
  "al.enableCodeAnalysis": true,
  "al.codeAnalyzers": ["${CodeCop}", "${UICop}", "${PerTenantExtensionCop}"],
  
  // Auto-save for better AI context
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000
}
```

### Keyboard Shortcuts
Useful shortcuts when working with AI:

- **Trigger Suggestion**: `Ctrl+Space` (Windows/Linux) or `Cmd+Space` (Mac)
- **Accept Suggestion**: `Tab`
- **Next Suggestion**: `Alt+]`
- **Previous Suggestion**: `Alt+[`
- **Open Copilot Chat**: `Ctrl+Shift+I`

## Tool Integrations

### Combining Tools Effectively

**AI + Code Analysis**:
1. Generate code with AI
2. Run CodeCop to check standards
3. Fix issues with AI assistance
4. Verify with test runner

**AI + Documentation**:
1. Write code with AI
2. Generate XML docs with AI
3. Format with AL XML Documentation extension
4. Create README with Markdown All in One

**AI + Version Control**:
1. Develop features with AI
2. Review changes with GitLens
3. Commit with clear AI-related messages
4. Track AI productivity over time

## Community-Created Tools

### AL Prompt Templates
**Type**: Snippet Collection  
**What**: Pre-written prompts for common AL tasks

**Repository**: [Example - To be created by community]

---

### BC AI Helper Scripts
**Type**: PowerShell Scripts  
**What**: Automation scripts for AI-assisted BC development

**Repository**: [Example - To be created by community]

---

## Contributing Tools

**Created a tool for AI-assisted AL development?**

Share it with the community!

**Submission Guidelines**:
- Must be useful for AL/BC development
- Should enhance AI-assisted workflows
- Open source preferred
- Well documented

**How to Submit**: See [Contributing](../../../contributing) section

## Tool Safety & Privacy

### Privacy Considerations

**What Gets Shared**:
- Code in your workspace (with AI assistants)
- File names and structure
- Your prompts and questions

**Best Practices**:
- Review extension permissions
- Understand data handling
- Use organization-approved tools
- Don't include sensitive data in code

### Security

**Verify Extensions**:
- Check publisher reputation
- Read reviews
- Review permissions requested
- Keep extensions updated

## Updates

Extensions are frequently updated. Check for updates regularly:

- VS Code: `Ctrl+Shift+X` → Click update icon
- Auto-update: Enable in VS Code settings

---

## Placeholder Notice

> **Note**: Some tools listed may be examples for community-created utilities. As real tools are developed and published, they should be added here. The core Microsoft and established community extensions (GitHub Copilot, AL Language, etc.) are real and available now.

---

## Related Resources

- [Setup Guide](../../gettingstarted/setup) - Environment configuration
- [Blog Posts](../articles) - Tool reviews and comparisons
- [Videos](../videos) - Tool demonstrations
- [Discussions](../discussions) - Tool recommendations and support
