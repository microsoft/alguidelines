---
title: "MCP Servers"
linkTitle: "Tools"
weight: 3
description: >
  Model Context Protocol (MCP) servers that enhance AI-assisted AL development
---

## Overview

This section catalogs Model Context Protocol (MCP) servers designed to enhance AI coding assistants for Business Central development. MCP is an open standard that enables AI assistants to connect to external tools, databases, and knowledge sources, making them significantly more powerful for AL development workflows.

**What is MCP?**
- Standard protocol for connecting AI assistants to external capabilities
- STDIO transport for local execution
- Tool-based architecture for exposing features to AI assistants
- Supported by Claude Desktop, GitHub Copilot, Cursor, VS Code extensions, and more

Each MCP server below has its own dedicated page with detailed information, setup instructions, and integration guidance.

---

## AL & Business Central MCP Servers

### [BC Code Intelligence MCP](bc-code-intelligence-mcp.md)

**Key Features**: 
- 14 specialist AI personas for BC development (Sam Coder, Dean Debug, Alex Architect, etc.)
- 20+ MCP tools for knowledge discovery, code analysis, and workflow orchestration
- Seamless specialist handoffs with context preservation
- Integration with GitHub Copilot, Claude Desktop, VS Code

---

### [AL Dependency MCP Server](al-dependency-mcp-server.md)

**Key Features**:
- Exposes AL workspace compiled symbols (.app files) to AI assistants
- 6 token-optimized tools for symbol search and dependency analysis
- O(1) lookups with optimized indexing for 50MB+ symbol files
- Auto-discovers .alpackages directories

---

### [Serena MCP](serena-mcp.md)

**Key Features**:
- Multi-language LSP-based coding assistant with AL support
- AL Language Server integration via VS Code AL extension
- Symbolic operations (find references, definitions, document symbols)
- Supports 20+ languages including AL, Python, TypeScript, Rust, Go

---

### [AL Object ID Ninja MCP](al-objid-mcp-server.md)

**Key Features**:
- AL object ID collision prevention and management
- Two modes: LITE (individual developers) and STANDARD (teams)
- Backend integration for shared ID pools and team collaboration
- Automatic app identification via Git integration

---

## DevOps & Productivity MCP Servers

### [Azure DevOps MCP](azure-devops-mcp.md)

**Key Features**:
- Official Microsoft MCP server for Azure DevOps integration
- 50+ tools covering work items, repos, pipelines, wiki, and advanced security
- Domain filtering to manage tool count and focus
- Multiple authentication options (interactive, azcli, env)

---

### [Clockify MCP](clockify-mcp.md)

**Key Features**:
- Comprehensive Clockify time tracking integration
- 33 tools for workspace, client, project, task, tag, and time entry management
- Timer start/stop functionality and bulk operations
- Full CRUD operations for all Clockify entities

---

### [BC Telemetry Buddy (Waldo)](waldo-bctelemetrybuddy.md)

**Key Features**:
- Simple helpers for collecting Business Central telemetry
- Forward telemetry events to a custom endpoint for analysis
- Lightweight and easy to add to development workflows

---

### [NAB AL Tools MCP](nab-al-tools-mcp.md)

**Key Features**:
- XLIFF/XLF translation tooling exposed as MCP tools
- Rich validation and annotations (Zod schemas)
- Multiple invocation options (npx, global, local)

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

---

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

---

## Related Resources

- [Setup Guide](../../getting-started/setup) - Environment configuration
- [Blog Posts](../articles) - Tool reviews and comparisons
- [Videos](../videos) - Tool demonstrations
- [GitHub Discussions](https://github.com/microsoft/alguidelines/discussions) - Tool recommendations and support
