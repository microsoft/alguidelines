---
title: "AL Object ID Ninja MCP"
linkTitle: "AL Object ID Ninja"
weight: 5
description: >
  AL object ID collision prevention and management for Business Central development teams
---

## Overview

The AL Object ID Ninja MCP Server manages AL object IDs to prevent collisions in Business Central development. It provides two modes: LITE for individual developers and STANDARD for teams with shared ID pools and backend integration.

**Created by**: [SShadowS (Torben Leth)](https://github.com/SShadowS)

---

## Key Features

**Two Modes**:
- **LITE Mode** (4 tools) - Individual developers: authorization, config, allocate_id, analyze_workspace
- **STANDARD Mode** (8 tools) - Teams: adds pool management, consumption reports, backend sync, activity logs

**Collision Prevention**:
- Automatic ID conflict detection
- Smart ID allocation with preview/reserve/reclaim
- Git integration for automatic app identification
- Real-time workspace analysis

**Team Collaboration** (STANDARD):
- Shared ID pools across team members
- Backend integration with AL Object ID Ninja service
- Consumption tracking and reporting
- Audit trail with activity logs

**Configuration**:
Set `MCP_MODE` environment variable to `lite` or `standard` (default: lite)

---

## Links

- **GitHub**: https://github.com/SShadowS/al-objid-mcp-server
- **npm Package**: https://www.npmjs.com/package/@sshadows/objid-mcp
- **Backend Service**: AL Object ID Ninja (vjekocom-alext-weu.azurewebsites.net)
