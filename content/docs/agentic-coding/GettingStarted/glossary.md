---
title: "Glossary"
linkTitle: "Glossary"
weight: 6
description: >
  Common terms and concepts in AI-assisted development
---

## AI & Coding Assistant Terms

### Agent / Agentic AI
An AI system that can take actions autonomously, make decisions, and use tools to accomplish tasks. In coding, an agentic AI can read files, write code, run commands, and iterate on solutions without constant human intervention.

### AI Assistant / Coding Assistant
Software that uses artificial intelligence to help you write code. Examples include GitHub Copilot, Claude, ChatGPT, Cursor, and Windsurf.

### Context
Information the AI has access to when responding to your request. This can include:
- Your current file and selection
- Open files in your workspace
- Previous conversation messages
- Project structure and files
- Tools and external data sources

**Why it matters**: The more relevant context the AI has, the better its responses. Limited context can lead to generic or incorrect suggestions.

### Context Window
The maximum amount of text (measured in tokens) an AI can process at once. Think of it as the AI's "working memory."

**Example**: A 200K token context window can hold roughly 150,000 words of text—about 300 pages.

### Hallucination
When an AI generates information that sounds plausible but is incorrect or fabricated. This can include:
- Non-existent AL objects or methods
- Made-up API endpoints
- Incorrect syntax or patterns

**How to avoid**: Always verify AI suggestions, especially for critical code or unfamiliar APIs.

### Inference
The process of an AI model generating a response to your input. Each time you send a prompt and get a response, that's one inference.

### Large Language Model (LLM)
The AI technology powering coding assistants. LLMs are trained on vast amounts of text (including code) to understand and generate human-like responses.

**Examples**: GPT-4, Claude 3.5 Sonnet, Llama, Gemini

### Model
The specific AI system you're interacting with. Different models have different capabilities, strengths, and context windows.

**Examples**: 
- Claude 3.5 Sonnet (good at code and reasoning)
- GPT-4o (fast, multimodal)
- o1 (optimized for complex reasoning)

---

## Prompting & Communication

### Prompt
Your input or question to the AI. A prompt can be:
- A question: "How do I post a sales invoice in AL?"
- A command: "Add error handling to this function"
- A request: "Refactor this code to use modern AL patterns"

**Tip**: Clear, specific prompts get better results than vague ones.

### System Prompt / Instructions
Background instructions that guide the AI's behavior and personality. You typically don't see these, but they tell the AI how to respond (e.g., "You are a Business Central expert," "Be concise," "Provide code examples").

### Few-Shot / One-Shot Prompting
Providing examples in your prompt to guide the AI's response format.

**Example**:
```
Create getter methods like this example:
procedure GetCustomerName(): Text[100]
begin
    exit("Customer Name");
end

Now create a getter for "Customer Email"
```

### Chain of Thought
Asking the AI to explain its reasoning step-by-step before providing an answer. This often improves accuracy for complex problems.

**Example**: "Let's think through how to design this posting routine step by step..."

---

## Technical Terms

### Token
The basic unit of text that AI models process. Roughly:
- 1 token ≈ 4 characters in English
- 1 token ≈ ¾ of a word
- 100 tokens ≈ 75 words

**Why it matters**: Context windows, pricing, and API limits are measured in tokens.

### Tool / Tool Calling / Function Calling
External capabilities the AI can use to perform actions beyond text generation:
- Read and write files
- Run terminal commands
- Search the web
- Query databases
- Execute MCP server tools

**Example**: When you ask "What's in my app.json?", the AI uses a "read file" tool rather than guessing.

### MCP (Model Context Protocol)
An open standard for connecting AI assistants to external tools and data sources. MCP servers expose capabilities (like AL symbol databases, Azure DevOps, time tracking) that AI assistants can use.

**Example**: The AL Dependency MCP Server lets your AI assistant search compiled AL packages.

### RAG (Retrieval-Augmented Generation)
A technique where the AI retrieves relevant information from external sources before generating a response. This helps provide accurate, up-to-date information beyond the AI's training data.

**Example**: BC Code Intelligence MCP uses RAG to fetch specific Business Central knowledge topics.

### Temperature
A setting that controls how creative or deterministic the AI's responses are:
- **Low temperature (0.0-0.3)**: Focused, consistent, predictable—good for code generation
- **High temperature (0.7-1.0)**: Creative, varied, exploratory—good for brainstorming

### Embeddings
Mathematical representations of text that capture semantic meaning. Used to find relevant information by similarity rather than exact keyword matches.

**Example**: Searching for "customer posting" would find content about "posting customer transactions" even without exact word matches.

---

## AL & Business Central Specific

### AL Language Server
A background service that provides intelligent code features for AL:
- Code completion
- Go to definition
- Find references
- Syntax checking

**Note**: Some MCP servers (like Serena) integrate with the AL Language Server to give AI assistants these capabilities.

### Symbol
In AL, a symbol is any named code element:
- Objects (tables, pages, codeunits)
- Fields
- Procedures
- Variables

### .app Package
A compiled AL extension package containing symbols and metadata. AI assistants can't read these directly, which is why tools like AL Dependency MCP Server exist.

### Object ID
The numeric identifier for AL objects (tables, pages, codeunits, etc.). Managing these IDs across teams requires coordination to avoid conflicts—that's where AL Object ID Ninja MCP helps.

---

## Development Workflow

### Pair Programming
A development practice where two people work together on the same code. With AI assistants, you're essentially pair programming with an AI partner.

### Code Review
Examining code to find issues, ensure quality, and share knowledge. AI assistants can help with code review by analyzing patterns, suggesting improvements, and catching common mistakes.

### Refactoring
Improving code structure and readability without changing its behavior. AI assistants excel at refactoring tasks like renaming, extracting methods, and modernizing patterns.

### Test-Driven Development (TDD)
Writing tests before writing the code that satisfies them. AI assistants can help generate test cases and implementations.

---

## Common Acronyms

| Term | Meaning |
|------|---------|
| **AI** | Artificial Intelligence |
| **LLM** | Large Language Model |
| **MCP** | Model Context Protocol |
| **NLP** | Natural Language Processing |
| **RAG** | Retrieval-Augmented Generation |
| **TDD** | Test-Driven Development |
| **LSP** | Language Server Protocol |
| **IDE** | Integrated Development Environment |
| **API** | Application Programming Interface |
| **CRUD** | Create, Read, Update, Delete |
| **CLI** | Command Line Interface |
| **PAT** | Personal Access Token |

---

## Tips for Learning the Language

**Don't worry about knowing everything!** Start with these core concepts:
- **Prompt**: What you say to the AI
- **Context**: What information the AI can see
- **Token**: How AI text is measured
- **Hallucination**: When AI makes things up
- **Tool**: Actions the AI can take (like reading files)

As you work with AI assistants, you'll naturally pick up more terminology. The important thing is understanding how to communicate effectively and knowing when to verify AI suggestions.

---

## Related Resources

- **[What is Agentic Coding](../what-is-agentic-coding)** - Core concepts explained
- **[Effective Prompting](../effective-prompting)** - How to communicate with AI
- **[Understanding Limitations](../limitations)** - What AI can and can't do
- **[Tools & MCP Servers](../../communityresources/tools)** - Extending AI capabilities
