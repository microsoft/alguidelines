# Vibe Coding Rules - Contribution Guide

This directory contains AI-optimized coding rules for AL development. Each rule set is organized into markdown files that can be easily consumed by AI coding assistants.

## Directory Structure

```
vibe-coding/
├── _index.md                    # Main landing page
├── README.md                    # This file - contribution guide
├── al-guidelines-rules.md       # Complete rules file with references to all categories
├── al-code-style.md             # Code style, formatting, and documentation rules
├── al-naming-conventions.md     # File naming, object naming, and variable naming
├── al-performance.md            # Performance optimization and query guidelines
├── al-error-handling.md         # Error handling patterns and troubleshooting
├── al-events.md                 # Event-driven development and extensibility
├── al-testing.md                # AL-Go workspace structure and testing guidelines
```

## How to Add New Rules

### 1. Choose the Right Category
Select the appropriate category for your rules, or create a new category if needed.

### 2. Follow the Standard Format
Each rule file should follow this structure:

```markdown
---
title: "AL [Category Name] Rules"
description: >
  Brief description of the rule category for AL development
globs: ["*.al", "*.json"]  # File types this rule applies to
alwaysApply: true|false    # Whether to always apply these rules
---

# AL [Category Name] Rules

Brief introduction to the category and its importance in AL development.

## Rule 1: [Descriptive Title]

### Intent
What this rule aims to achieve, including implementation details and guidance for AI assistants.

### Examples

```al
// Good example
[code example]
```

You can also add a bad example to show what to avoid, but this is optional.
```al
// Bad example (avoid)
[code example]
```

## Rule 2: [Next Rule]
[Continue with same format...]
```

### 3. Update the Index
After adding new rule files:
- Update `al-guidelines-rules.md` to include `@your-new-file.md` reference
- Update `_index.md` to include links to new categories
- Ensure all cross-references are updated

### 4. Test Your Rules
Before submitting, test your rules with AI assistants to ensure they:
- Are clearly understood by AI agents
- Produce the expected code patterns
- Don't conflict with existing rules

## Contribution Workflow

1. **Fork** the alguidelines repository
2. **Create** your rule files in the appropriate category
3. **Test** the rules with your preferred AI assistant
4. **Submit** a pull request with:
   - Clear description of the rules added
   - Examples of how the rules improve code quality
   - Any testing results with AI assistants (optional)

## Best Practices for Rule Writing

### Make Rules AI-Friendly
- Use clear, unambiguous language specific to AL development
- Provide specific AL code examples with proper syntax
- Include both positive and negative examples, when applicable
- Structure content consistently

## Questions?

If you have questions about contributing rules, please:
- Open a discussion in the GitHub repository
- Join the Business Central community Discord
- Contact the initiative maintainers

---

*This README is part of the Vibe Coding for AL initiative - enhancing AL development through AI-optimized guidelines.* 