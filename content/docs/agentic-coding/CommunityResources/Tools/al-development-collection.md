---
title: "AL Development Collection"
linkTitle: "AL Development Collection"
weight: 1
description: >
  AI Native AL Development toolkit with 37 Agent Primitives for systematic Business Central development
---

## Overview

The AL Development Collection provides a complete AI-native development toolkit for Microsoft Dynamics 365 Business Central. Instead of ad-hoc AI usage, you get systematic engineering through 37 Agent Primitives organized across 3 framework layers implementing the AI Native-Instructions Architecture.

**Created by**: [Javier Armesto](https://github.com/javiarmesto)

---

## Key Features

**6 Role-Based Specialist Agents**:
- al-architect 🏗️ (solution design), al-developer 💻 (implementation), al-debugger 🐛 (troubleshooting)
- al-tester ✅ (TDD/quality), al-api 🌐 (API development), al-copilot 🤖 (AI features)

**4 Orchestra System Agents** (Multi-agent TDD):
- al-conductor 🎭 (orchestration), al-planning-subagent 🔍 (research)
- al-implement-subagent 💻 (TDD implementation), al-review-subagent ✅ (validation)

**9 Auto-Applied Instructions**:
- Code style, naming conventions, performance patterns
- Error handling, events, testing standards
- Context-aware loading via `applyTo` patterns

**18 Agentic Workflows**:
- Environment setup (al-initialize, al-build)
- Development (al-events, al-pages, al-permissions)
- Analysis (al-diagnose, al-performance, al-migrate)
- Copilot features (al-copilot-capability, al-copilot-promptdialog, al-copilot-test)

**Smart Complexity Routing (Experimental)**:
- 🟢 LOW → al-developer (direct implementation)
- 🟡 MEDIUM → al-conductor (TDD orchestration)
- 🔴 HIGH → al-architect → al-conductor (full design + TDD)

---

## Links

- **GitHub**: https://github.com/javiarmesto/AL-Development-Collection-for-GitHub-Copilot
- **Quick Start**: https://github.com/javiarmesto/AL-Development-Collection-for-GitHub-Copilot/blob/main/QUICK-START.md
- **Complete Development Flow**: https://github.com/javiarmesto/AL-Development-Collection-for-GitHub-Copilot/blob/main/docs/workflows/complete-development-flow.md
