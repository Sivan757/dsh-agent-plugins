---
name: mcp-setup
description: Onboard an MCP server into a DeepSeek Harness project by writing a validated declaration. Use when the user asks to "add an MCP server", "set up MCP", "connect a tool server", or reports a server that fails to connect.
---

# MCP Setup

Produce a working MCP declaration for the user's scope and verify it.

## Step 1 — Scope

Interview with `ask_user_question`:

1. What the server is (name, what tools it provides) and how it runs: a local command (stdio) or an HTTP endpoint.
2. Destination:
   - User-wide servers: `~/.agents/mcp.json` (applies across sessions).
   - Project servers: the project's `.agents/mcp.json` when the market plugin's project-layout scan is on, or a suite the project installs through the market page.

## Step 2 — Draft

Write the declaration matching the destination's shape. A user `~/.agents/mcp.json` entry looks like:

```json
{
  "mcpServers": {
    "context7": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

Rules:

- stdio servers: `command` plus `args`; never a shell string. HTTP servers: `type: "streamable-http"` (or `"sse"` for legacy endpoints) with `url`.
- Environment values the user must supply: reference a credential by name with `${NAME}`; user-owned files expand credential references through the host's credential service. A portable suite `mcp.json` cannot carry credential references — keep those in the namespace policy or user overrides.
- Secrets are never written as literal values.

## Step 3 — Verify

1. JSON parses; every server has `type`; no unknown top-level keys.
2. `${NAME}` references resolve to configured credentials; a missing one fails closed with a diagnostic, so confirm it before declaring success.
3. stdio command resolves on PATH (or an absolute path); HTTP URL is reachable.

## Step 4 — Handoff

Report which file was written, the server names, and the reload step: new servers attach on the next discovery pass; tell the user to reinstall/refresh the source from the market page when a suite declaration changed.
