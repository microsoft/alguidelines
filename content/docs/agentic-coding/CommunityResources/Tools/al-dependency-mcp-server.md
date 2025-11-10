---
title: "AL Dependency MCP Server"
linkTitle: "AL Dependency MCP"
weight: 2
description: >
  Expose AL workspace compiled symbols to AI assistants for intelligent code navigation and dependency analysis
---

## Overview

The AL Dependency MCP Server exposes all compiled AL package symbols (.app files) to AI assistants, enabling them to understand dependencies, navigate code, and provide accurate suggestions based on Microsoft base app and other extension symbols.

**Created by**: [Stefan Maron](https://github.com/StefanMaron)

---

## Key Features

**Symbol Database**:
- O(1) lookup performance via optimized indices
- Streaming JSON parser for large symbol files (handles 50MB+ Microsoft base app)
- Sub-100ms query responses
- <500MB memory usage even with massive symbol databases

**6 Token-Optimized Tools**:
- `al_search_objects` - Search for AL objects across all packages
- `al_get_object_definition` - Get full object definition with all members
- `al_get_object_summary` - Get token-optimized summary (96% smaller)
- `al_get_object_members` - Get object members without full definition
- `al_packages` - List all available AL packages
- `al_get_stats` - Get database statistics and diagnostics

**Auto-Discovery**:
- Automatically finds .alpackages directories in your workspace
- Detects package changes in real-time
- Supports multiple package sources

**Requirements**: Node.js 18+, .NET SDK 8.0+ (for development)

---

## Links

- **GitHub**: https://github.com/StefanMaron/AL-Dependency-MCP-Server
- **npm Package**: https://www.npmjs.com/package/al-mcp-server
