---
name: lsp-setup
description: Onboard a language server for DeepSeek Harness by writing a validated LSP declaration. Use when the user asks to "set up LSP", "add a language server", "enable diagnostics", or reports that go-to-definition or diagnostics do not work.
---

# LSP Setup

Produce a working LSP declaration for the user's scope and verify the server binary.

## Step 1 — Detect

1. From the project's manifests and file tree, list the languages in use (package.json → TypeScript/JavaScript, go.mod → Go, pyproject.toml → Python, and so on).
2. For each language, check whether a language server binary is available: `typescript-language-server`, `gopls`, `pyright-langserver`, `rust-analyzer`, `lua-language-server`. Suggest installing the missing ones with the project's package manager.

## Step 2 — Scope

Ask where the declaration goes:

- User-wide servers: `~/.agents/lsp.json` (applies across sessions).
- A suite the project installs through the market page: the suite's own `com.deepseek.harness/lsp.json`.

## Step 3 — Draft

Write the declaration. Shape (bare server map or `{"lspServers": ...}` both load):

```json
{
  "lspServers": {
    "typescript": {
      "command": "typescript-language-server",
      "args": ["--stdio"],
      "extensionToLanguage": { ".ts": "typescript", ".tsx": "typescript", ".js": "javascript" }
    }
  }
}
```

Rules:

- `command` is required and must not contain spaces; pass flags through `args`.
- `extensionToLanguage` is required: file extension → LSP language ID. The first server registered for an extension wins.
- Optional: `env`, `initializationOptions`, `settings`, `workspaceFolder`, `startupTimeoutMs`.

## Step 4 — Verify

1. JSON parses; every entry has `command` and `extensionToLanguage`; no empty `extensionToLanguage`.
2. The binary resolves on PATH: run `command -v <server>` for each.
3. Start the server once with its stdio handshake (send `initialize` over stdin) or simply report that the `lsp` tool answers a hover request after the next session start.

Report the file path, the servers declared, and the extensions each covers.
