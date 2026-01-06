# github-slim

> **Github MCP server optimized for AI assistants** — Reduce context window tokens by 72.0% while keeping full functionality. Compatible with Claude, ChatGPT, Gemini, Cursor, and all MCP clients.

[![npm version](https://img.shields.io/npm/v/github-slim.svg)](https://www.npmjs.com/package/github-slim)
[![Test Status](https://img.shields.io/badge/tests-passing-brightgreen)](https://github.com/mcpslim/mcpslim)
[![MCP Compatible](https://img.shields.io/badge/MCP-compatible-blue)](https://modelcontextprotocol.io)

## What is github-slim?

A **token-optimized** version of the Github [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server.

### The Problem

MCP tool schemas consume significant **context window tokens**. When AI assistants like Claude or ChatGPT load MCP tools, each tool definition takes up valuable context space.

The original `@modelcontextprotocol/server-github` loads **26 tools** consuming approximately **~18,216 tokens** — that's space you could use for actual conversation.

### The Solution

`github-slim` intelligently **groups 26 tools into 7 semantic operations**, reducing token usage by **72.0%** — with **zero functionality loss**.

Your AI assistant sees fewer, smarter tools. Every original capability remains available.

## Performance

| Metric | Original | Slim | Reduction |
|--------|----------|------|-----------|
| Tools | 26 | 7 | **-56%** |
| Schema Tokens | 3,396 | 1,114 | **67.2%** |
| Claude Code (est.) | ~18,216 | ~5,104 | **~72.0%** |

> **Benchmark Info**
> - Original: `@modelcontextprotocol/server-github@2025.4.8`
> - Schema tokens measured with [tiktoken](https://github.com/openai/tiktoken) (cl100k_base)
> - Claude Code estimate includes ~570 tokens/tool overhead

## Quick Start

### One-Command Setup (Recommended)

```bash
# Claude Desktop - auto-configure
npx github-slim --setup claude

# Cursor - auto-configure
npx github-slim --setup cursor

# Interactive mode (choose your client)
npx github-slim --setup
```

Done! Restart your app to use github.

> ⚠️ **This MCP requires environment variables.** The setup will add placeholders - update them with your actual values. See [Configuration](#configuration).

### CLI Tools (already have CLI?)

```bash
# Claude Code (creates .mcp.json in project root)
claude mcp add github -s project --env GITHUB_PERSONAL_ACCESS_TOKEN=<YOUR_TOKEN> -- npx -y github-slim

# Windows: use cmd /c wrapper
claude mcp add github -s project --env GITHUB_PERSONAL_ACCESS_TOKEN=<YOUR_TOKEN> -- cmd /c npx -y github-slim

# VS Code (Copilot, Cline, Roo Code)
code --add-mcp '{"name":"github","command":"npx","args":["-y","github-slim"],"env":{"GITHUB_PERSONAL_ACCESS_TOKEN":"<YOUR_TOKEN>"}}'
```

## Configuration

### Required Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `GITHUB_PERSONAL_ACCESS_TOKEN` | GitHub Personal Access Token with repo scope | Yes |

## Manual Setup

<details>
<summary>Click to expand manual configuration options</summary>

### Claude Desktop

Add to your `claude_desktop_config.json`:

| OS | Path |
|----|------|
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "github-slim"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<YOUR_TOKEN>"
      }
    }
  }
}
```

### Cursor

Add to `.cursor/mcp.json` (global) or `<project>/.cursor/mcp.json` (project):

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "github-slim"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<YOUR_TOKEN>"
      }
    }
  }
}
```

</details>

## How It Works

MCPSlim acts as a **transparent bridge** between AI models and the original MCP server:

```
┌─────────────────────────────────────────────────────────────────┐
│  Without MCPSlim                                                │
│                                                                 │
│  [AI Model] ──── reads 26 tool schemas ────→ [Original MCP]    │
│             (~18,216 tokens loaded into context)                 │
├─────────────────────────────────────────────────────────────────┤
│  With MCPSlim                                                   │
│                                                                 │
│  [AI Model] ───→ [MCPSlim Bridge] ───→ [Original MCP]           │
│       │                │                      │                 │
│   Sees 7 grouped      Translates to        Executes actual   │
│   tools only         original call       tool & returns    │
│   (~5,104 tokens)                                              │
└─────────────────────────────────────────────────────────────────┘
```

### How Translation Works

1. **AI reads slim schema** — Only 7 grouped tools instead of 26
2. **AI calls grouped tool** — e.g., `interaction({ action: "click", ... })`
3. **MCPSlim translates** — Converts to original: `browser_click({ ... })`
4. **Original MCP executes** — Real server processes the request
5. **Response returned** — Result passes back unchanged

**Zero functionality loss. 72.0% token savings.**

## Available Tool Groups

| Group | Actions |
|-------|---------|
| `create` | 4 |
| `issue` | 2 |
| `list` | 4 |
| `repository` | 2 |
| `search` | 2 |

Plus **2 passthrough tools** — tools that don't group well are kept as-is with optimized descriptions.

## Compatibility

- ✅ **Full functionality** — All original `@modelcontextprotocol/server-github` features preserved
- ✅ **All AI assistants** — Works with Claude, ChatGPT, Gemini, Copilot, and any MCP client
- ✅ **Drop-in replacement** — Same capabilities, just use grouped action names
- ✅ **Tested** — Schema compatibility verified via automated tests

## FAQ

### Does this reduce functionality?

**No.** Every original tool is accessible. Tools are grouped semantically (e.g., `click`, `hover`, `drag` → `interaction`), but all actions remain available via the `action` parameter.

### Why do AI assistants need token optimization?

AI models have limited context windows. MCP tool schemas consume tokens that could be used for conversation, code, or documents. Reducing tool schema size means more room for actual work.

### Is this officially supported?

MCPSlim is a community project. It wraps official MCP servers transparently — the original server does all the real work.

## License

MIT

---

<p align="center">
  Powered by <a href="https://github.com/mcpslim/mcpslim"><b>MCPSlim</b></a> — MCP Token Optimizer
  <br>
  <sub>Reduce AI context usage. Keep full functionality.</sub>
</p>
