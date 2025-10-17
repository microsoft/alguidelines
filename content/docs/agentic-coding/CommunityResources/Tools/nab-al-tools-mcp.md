---
title: "NAB AL Tools MCP"
linkTitle: "NAB AL Tools"
weight: 8
description: >
  XLIFF translation and localization tools exposed as an MCP server for AL projects.
---

## Overview

The NAB AL Tools MCP Server exposes XLIFF translation management capabilities (the same core features used by the NAB AL Tools VS Code extension) as a standalone MCP server. It lets AI assistants and other MCP-compatible clients inspect, create, refresh and save XLF files used for AL localization workflows.

**Publisher**: [nabsolutions / jwikman](https://github.com/jwikman)

---

## Key Features

- Comprehensive XLF/XLIFF tooling: refresh, create language XLFs, search by keyword, get untranslated texts, and more
- Proper MCP annotations and Zod input validation for robust error handling
- Configurable: works with npx, global install, or local project install
- Supports pagination, batch saves and performant parsing for large translation files
- Designed to work locally (openWorldHint: false) and respects workspace boundaries

---

## Installation & Usage

Recommended via npx (no install required):

```bash
npx -y @nabsolutions/nab-al-tools-mcp
```

Or install globally:

```bash
npm install -g @nabsolutions/nab-al-tools-mcp
```

MCP client example (npx):

```json
{
  "mcpServers": {
    "nab-al-tools": {
      "command": "npx",
      "args": ["-y", "@nabsolutions/nab-al-tools-mcp"]
    }
  }
}
```

---

## Notable Tools (summary)

- `refreshXlf` — synchronize generated .g.xlf with target XLF
- `getTextsToTranslate` — list untranslated units with pagination
- `getTranslatedTextsMap` — fetch existing translations grouped by source
- `getTranslatedTextsByState` — filter translations by state (needs-review, translated, final)
- `saveTranslatedTexts` — batch save translated units (up to configured limits)
- `createLanguageXlf` — generate new language XLFs (can optionally match base app translations)
- `getTextsByKeyword` — search XLF content by keyword or regex
- `getGlossaryTerms` — return builtin BC glossary pairs for consistent terminology

---

## Requirements

- Node.js >= 20
- npm (if installing)

---

## Links

- **GitHub**: https://github.com/jwikman/nab-al-tools
- **MCP readme (source)**: https://github.com/jwikman/nab-al-tools/blob/main/extension/MCP_SERVER.md
