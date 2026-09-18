---
name: plugin-validate
description: Validate a suite or plugin directory against the Agent Plugins v1 specification and dsh layout conventions. Use when the user asks to "validate my plugin", "check my suite", "verify plugin.json", or reports a suite that fails to appear in the market.
---

# Plugin Validate

Check one suite directory and report every problem with its fix. Read-only: never modify the suite under validation.

## Step 1 — Locate

Accept a directory path from the user; otherwise ask. Confirm the shape:

- `plugin.json` at the directory root (Agent Plugins v1), or
- `.claude-plugin/plugin.json` (Claude Code layout), or
- a skill collection without a manifest.

## Step 2 — Manifest checks (v1)

For a v1 suite, verify against the specification:

1. `$schema` names a supported release (`1.0.0` / `1.1.0`) and matches the `$schema` inside `mcp.json` when that file exists.
2. `name`: 1–64 chars, `^[a-z0-9][a-z0-9.-]*[a-z0-9]$`, no `--` or `..` anywhere.
3. No unknown top-level fields: v1 is a closed schema; `commands`, `agents`, `hooks`, `mcpServers` inline keys belong to other dialects and are ignored here.
4. `extensions["com.deepseek.harness"].schemaVersion`, when present, is `"1.0.0"`; the extension directory `com.deepseek.harness/` is only read when the manifest declares it.
5. Every component path (namespace commands, agents, hooks, LSP declarations) stays inside the suite root; flag any `..` segment or symlink escape.

## Step 3 — Component checks

1. `skills/`: one `SKILL.md` per immediate subdirectory; deeper nesting is not discovered.
2. `mcp.json`: `$schema` + `mcpServers` only; every server has `type` matching one closed variant; `${VAR}` expansion covers only `${PLUGIN_ROOT}` and `${PLUGIN_DATA}` — flag any other placeholder, including credential references (the portable file keeps them literal by design; credentials belong in the namespace policy or user overrides).
3. `com.deepseek.harness/commands/*.md`: frontmatter `description` present; body carries the prompt.
4. `com.deepseek.harness/agents/*.md`: `name` matches `^[a-z0-9][a-z0-9-]*$`; `description` present.
5. `com.deepseek.harness/hooks/hooks.json`: top-level `hooks` object keyed by event names (`PreToolUse`, `PostToolUse`, `SessionStart`, `UserPromptSubmit`, `Stop`, `SubagentStart`, `SubagentStop`).
6. `com.deepseek.harness/lsp.json`: `lspServers` table or bare server map; every entry has `command` and `extensionToLanguage`.

## Step 4 — Report

Group findings as errors (the component will not load) and warnings (loads with reduced behavior). For each, name the file, the violated rule, and the concrete fix. End with the loadable surfaces count: skills, MCP servers, commands, agents, hooks, LSP declarations.
