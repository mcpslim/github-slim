# github-slim

> 🚀 Github MCP with **90.4% token reduction** for AI models

[![npm version](https://img.shields.io/npm/v/github-slim.svg)](https://www.npmjs.com/package/github-slim)
[![Test Status](https://img.shields.io/badge/tests-passing-brightgreen)](https://github.com/palan-k/mcpslim)

## Performance

| Metric | Original | Slim | Improvement |
|--------|----------|------|-------------|
| Tools | 26 | 7 | **-56%** |
| Tokens | 3,396 | 327 | **90.4%** |

> **Version Info**
> - Original: `@modelcontextprotocol/server-github@2025.4.8`
> - Slim version synced with original
> - Tokens measured with [tiktoken](https://github.com/openai/tiktoken) v1.0.21 (cl100k_base)

## Installation

```bash
npx github-slim
```

## Usage

### Claude Desktop

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "github-slim"]
    }
  }
}
```

### Claude Code CLI

```bash
claude mcp add github -- npx -y github-slim
```

### Gemini CLI

```bash
gemini mcp add github -- npx -y github-slim
```

### VS Code (Copilot, Cline, etc.)

```bash
code --add-mcp '{"name":"github","command":"npx","args":["-y","github-slim"]}'
```

### Cursor

Add to `.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "github-slim"]
    }
  }
}
```

## How It Works

MCPSlim acts as a **transparent bridge** between AI models and the original MCP server.

```
┌─────────────────────────────────────────────────────────────────┐
│  Without MCPSlim                                                │
│                                                                 │
│  [AI Model] ──── reads 26 tool schemas ────→ [Original MCP]    │
│             (3,396 tokens loaded into context)                    │
├─────────────────────────────────────────────────────────────────┤
│  With MCPSlim                                                   │
│                                                                 │
│  [AI Model] ───→ [MCPSlim Bridge] ───→ [Original MCP]           │
│       │                │                      │                 │
│   Sees 7 grouped      Translates to        Executes actual   │
│   tools only         original call       tool & returns    │
│   (327 tokens)                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Translation Flow

1. **AI reads slim schema** - Only 7 grouped tools instead of 26 (saves tokens)
2. **AI calls grouped tool** - e.g., `page({ action: "navigate", url: "..." })`
3. **MCPSlim translates** - Converts to: `navigate_page({ url: "..." })`
4. **Original MCP executes** - Real server processes the request
5. **Response returned** - Result passes back unchanged

**Zero functionality loss. 90.4% token savings.**

### Tool Groups

- `create`: 4 actions
- `issue`: 2 actions
- `list`: 4 actions
- `repository`: 2 actions
- `search`: 2 actions

- Plus 2 passthrough tools (ungrouped, but description slimmed)

## Compatibility

- ✅ All original `@modelcontextprotocol/server-github` functionality preserved
- ✅ Works with Claude, Gemini, ChatGPT, Qwen, and any MCP-compatible AI
- ✅ Same API - just use grouped action names
- ✅ Schema compatibility verified via automated tests

## License

MIT

---

Powered by [MCPSlim](https://github.com/palan-k/mcpslim) - MCP Token Compression Bridge
