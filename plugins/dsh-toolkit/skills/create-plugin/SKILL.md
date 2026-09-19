---
name: create-plugin
description: Create a DeepSeek Harness plugin — a directory with a root plugin.json that the market installs and mounts. Use when the user asks to "create a plugin", "scaffold a plugin", "package these as a plugin", or wants to ship skills, commands, agent roles, hooks, MCP servers or LSP declarations as one installable unit.
---

# Create Plugin

Build one installable plugin directory and verify it before delivery. A plugin is a directory with a root `plugin.json`; the market scans it, lists it as an installable suite, and mounts its surfaces into sessions. There is no install-time code: everything a plugin contributes is content the session reads.

## Step 1 — Scope

Interview with `ask_user_question`:

1. The plugin name — kebab-case, 1–64 characters from letters, digits, `.` and `-`, with no `--` or `..`.
2. What the plugin is for, and who installs it.
3. Where it lives: a new directory, or a new subdirectory of an existing plugin collection.
4. Which surfaces it ships: skills, commands, agent roles, hooks, MCP servers, LSP declarations.

## Step 2 — Plan the surfaces

Choose each surface by what it is for, then confirm the plan with the user as a table of surface, count and purpose before writing anything.

| Surface | Reach for it when |
| --- | --- |
| Skill | The session needs knowledge or a repeatable procedure |
| Command | The user invokes the job by name and passes arguments |
| Agent role | The job deserves its own persona, model route or isolated context |
| Hook | Something must run around a tool call, a prompt or a session boundary |
| MCP server | The job needs tools from an external server |
| LSP declaration | The session should have language-server intelligence for a file type |

A plugin that ships only skills is complete and installs the same way; never create a path for a surface the plan does not name.

## Step 3 — Create the structure

```text
<plugin-name>/
├── plugin.json
├── mcp.json                       # only when MCP servers are declared
├── skills/<name>/SKILL.md
└── com.deepseek.harness/
    ├── commands/<name>.md
    ├── agents/<name>.md
    ├── hooks/hooks.json
    └── lsp.json
```

Write `plugin.json`:

```json
{
  "$schema": "https://agent-plugins.org/schemas/1.0.0/plugin.schema.json",
  "name": "<plugin-name>",
  "version": "0.1.0",
  "description": "<one sentence naming what a session gains from it>",
  "author": { "name": "<author>" },
  "license": "MIT",
  "keywords": ["dsh"],
  "extensions": {
    "com.deepseek.harness": { "schemaVersion": "1.0.0" }
  }
}
```

- The name must equal the directory name; the market uses it as the plugin's identity.
- The manifest is closed. Top-level `commands`, `agents`, `hooks` and `mcpServers` keys belong to other dialects and are ignored here; components are discovered by their fixed paths.
- The `com.deepseek.harness/` directory is read only when `extensions["com.deepseek.harness"].schemaVersion` is declared as `"1.0.0"`. Drop the `extensions` block entirely for a plugin that ships skills and MCP servers only.
- Every component path stays inside the plugin root.

## Step 4 — Write the components

Each surface has its own skill; load it and follow it for the files it owns.

| Surface | Skill | Path it writes |
| --- | --- | --- |
| Skill | `create-skill` | `skills/<name>/SKILL.md` |
| Command | `create-command` | `com.deepseek.harness/commands/<name>.md` |
| Agent role | `create-agent` | `com.deepseek.harness/agents/<name>.md` |
| Hook | `create-hook` | `com.deepseek.harness/hooks/hooks.json` |
| MCP server | `create-mcp` | `mcp.json` |
| LSP declaration | `create-lsp` | `com.deepseek.harness/lsp.json` |

A project-owned asset that is not part of a distributed plugin goes to the project's own `.agents/` directory instead — those skills state the paths.

## Step 5 — Validate

1. Run the `validate-plugin` skill over the finished directory and fix every error it reports.
2. Hand the freshly written files to the `asset-validator` agent for the per-file contracts.
3. When the plugin is authored inside a collection a market source already points at, refresh that source in the market page, open the plugin, and confirm the surface counts match the plan.

## Step 6 — Distribute

1. Commit and push the plugin directory to the repository that hosts the collection.
2. In the DeepSeek Harness Web GUI, open the market page: add that repository URL as a source, or refresh the source that already points at it.
3. Install the plugin there and enable the surfaces the session should use. Skills enter the skill catalog, commands enter the slash menu, and MCP and LSP panels report their own connection state.

## Step 7 — Report

Give the plugin's path and name, the surfaces written with their file paths, the validation verdict, and the install step that remains for the user.
