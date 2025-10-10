---
title: "Azure DevOps MCP"
linkTitle: "Azure DevOps MCP"
weight: 4
description: >
  Official Microsoft MCP server for comprehensive Azure DevOps integration with AI assistants
---

## Overview

The Azure DevOps MCP Server is Microsoft's official Model Context Protocol implementation for Azure DevOps, providing AI assistants with comprehensive access to work items, repositories, pipelines, wiki, search, and advanced security features.

**Publisher**: [Microsoft](https://github.com/microsoft)

---

## Key Features

**50+ Tools Across All Azure DevOps Services**:
- Core (projects, teams, identities)
- Work (iterations, capacity)
- Work Items (CRUD, queries, comments, linking)
- Repositories (repos, branches, PRs, commits, file contents)
- Pipelines (builds, logs, artifacts, triggering)
- Wiki (pages, content, CRUD operations)
- Search (code, wiki, work items)
- Advanced Security (alerts, findings)

**Domain Filtering**:
- Enable only needed domains to reduce tool count
- Example: `-d work-items -d repositories` for focused workflows

**Multiple Authentication Options**:
- Interactive (default) - Browser-based OAuth
- Azure CLI - Use existing `az login` session
- Environment variable - PAT via `AZURE_DEVOPS_PAT`


---

## Links

- **GitHub**: https://github.com/microsoft/azure-devops-mcp
- **npm Package**: https://www.npmjs.com/package/@azure-devops/mcp
- **Getting Started**: https://github.com/microsoft/azure-devops-mcp/blob/main/docs/GETTINGSTARTED.md

