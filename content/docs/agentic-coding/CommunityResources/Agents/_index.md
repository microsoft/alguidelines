---
title: "AI Coding Agents"
linkTitle: "Agents"
weight: 5
description: >
  Learn about different AI coding agents and how to use them for AL development
---

## Overview

AI coding agents are intelligent assistants that help you write, review, and improve AL code for Business Central. This section explains the different types of agents available and how to choose the right one for your needs.

## What are AI Coding Agents?

AI coding agents are more than simple autocomplete tools. They can:
- Understand natural language instructions
- Generate complete code implementations
- Explain existing code
- Refactor and improve code quality
- Help debug issues
- Provide learning and guidance

## Available Agents

Explore detailed information about each AI coding agent:

### [GitHub Copilot](github-copilot-agent)
Microsoft's AI pair programmer integrated into VS Code.

**Best For**: 
- Inline code completion as you type
- Quick code generation
- Learning AL patterns
- Teams already using GitHub

**Key Features**: Real-time suggestions, multi-file context, AL-aware completions

---

### [GitHub Copilot Chat](github-copilot-chat-agent)
Conversational AI assistant from GitHub with deep VS Code integration.

**Best For**:
- Interactive code discussions
- Code explanations and learning
- Debugging assistance
- Complex refactoring tasks

**Key Features**: Natural language chat, context-aware responses, inline chat

---

### [Claude (via API or Claude.ai)](claude-agent)
Anthropic's AI assistant with strong reasoning capabilities.

**Best For**:
- Complex problem solving
- Detailed code analysis
- Architecture discussions
- Large codebase understanding

**Key Features**: Long context window, strong analytical abilities, helpful explanations

---

### [Cursor](cursor-agent)
AI-first code editor built on VS Code with integrated AI assistance.

**Best For**:
- All-in-one AI coding environment
- Teams wanting deep AI integration
- Multi-model AI access
- Codebase-wide AI context

**Key Features**: Multiple AI models, composer mode, codebase indexing, chat + autocomplete

---

## Comparison Matrix

| Feature | GitHub Copilot | Copilot Chat | Claude | Cursor |
|---------|---------------|--------------|---------|---------|
| **Inline Completion** | ✓✓✓ Excellent | Limited | N/A | ✓✓✓ Excellent |
| **Chat Interface** | N/A | ✓✓✓ Excellent | ✓✓✓ Excellent | ✓✓✓ Excellent |
| **AL Awareness** | ✓✓ Good | ✓✓ Good | ✓ Basic | ✓✓ Good |
| **Code Explanation** | Limited | ✓✓✓ Excellent | ✓✓✓ Excellent | ✓✓✓ Excellent |
| **VS Code Integration** | ✓✓✓ Native | ✓✓✓ Native | ✗ Web/API | ✓✓✓ Fork |
| **Context Window** | Medium | Medium | ✓✓✓ Very Large | Large |
| **Multi-file Editing** | Limited | Limited | Manual | ✓✓✓ Excellent |
| **Pricing** | $10-19/mo | Included | Free/Pro | $20/mo |
| **Team Features** | ✓✓ Good | ✓✓ Good | Limited | ✓ Basic |

**Legend**: ✓✓✓ Excellent | ✓✓ Good | ✓ Basic | Limited | ✗ Not Available | N/A Not Applicable

## Choosing the Right Agent

### For Individual Developers

**Just Starting with AI?**
→ Start with [GitHub Copilot](github-copilot-agent) + [Copilot Chat](github-copilot-chat-agent)
- Easy setup
- Great VS Code integration
- Good AL support
- One subscription for both

**Want Maximum AI Power?**
→ Try [Cursor](cursor-agent)
- Multiple AI models
- Strong codebase understanding
- All-in-one solution
- Advanced features

**Need Deep Analysis?**
→ Use [Claude](claude-agent) for complex tasks
- Long context for large codebases
- Excellent reasoning
- Detailed explanations
- Free tier available

### For Teams

**Microsoft/GitHub Ecosystem?**
→ GitHub Copilot for Business
- Centralized management
- License management
- Organization policies
- Familiar tools

**Maximum Flexibility?**
→ Cursor for Teams
- Multiple AI models
- Strong collaboration
- Advanced features
- Modern interface

**Hybrid Approach?**
→ Combine tools:
- GitHub Copilot for daily coding
- Claude for complex analysis
- Cursor for specific projects

## Using Multiple Agents

Many developers use multiple agents for different tasks:

**Daily Coding**: GitHub Copilot (inline suggestions)
**Learning & Debugging**: Copilot Chat or Cursor Chat
**Complex Problems**: Claude (detailed analysis)
**Refactoring**: Cursor (multi-file editing)

**Example Workflow**:
1. Write code with GitHub Copilot inline suggestions
2. Ask Copilot Chat to explain complex BC patterns
3. Use Claude for architecture review of large features
4. Use Cursor for complex multi-file refactoring

## Getting Started

### New to AI Coding?

1. **Start Simple**: [GitHub Copilot](github-copilot-agent)
2. **Learn the Basics**: [Effective Prompting](../../getting-started/effective-prompting)
3. **Try Examples**: [Getting More](../../getting-more)
4. **Explore Others**: Try Claude or Cursor for comparison

### Already Using AI?

**Expand Your Toolkit**:
- If using Copilot → Try Cursor for advanced features
- If using Claude → Add Copilot for inline completion
- If using Cursor → Use Claude for deep analysis

## Common Questions

### Can I use multiple agents?
Yes! Many developers use different agents for different tasks. They complement each other well.

### Which is best for AL development?
GitHub Copilot has the most AL-specific training, but all agents can be effective with good prompting.

### Are these expensive?
Most are $10-20/month for individual use. GitHub Copilot offers free access for students and open source maintainers.

### Do I need internet?
Yes, all current AI agents require internet connectivity to function.

### Will AI replace AL developers?
No. AI agents are tools to augment your capabilities, not replace your expertise and decision-making.

## Privacy & Security

All agents send code to external services. Consider:
- Review your organization's AI usage policy
- Don't include sensitive data in code
- Use business/enterprise plans for better controls
- Understand each tool's data handling policies

See individual agent pages for specific privacy information.

## Learning Path

1. **Read agent pages** to understand capabilities
2. **Choose one** to start with
3. **Follow setup** instructions
4. **Practice** with examples from [Getting More](../../getting-more)
5. **Experiment** with others as needed

## Resources

- [Setup Guide](../../getting-started/setup) - Environment configuration
- [Effective Prompting](../../getting-started/effective-prompting) - Get better results
- [Best Practices](../../getting-started/best-practices) - Use AI effectively
- [Limitations](../../getting-started/limitations) - Understand constraints

---

**Questions?** Join the discussion at [GitHub Discussions](https://github.com/microsoft/alguidelines/discussions)
