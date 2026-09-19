---
name: create-command
description: Create a slash command for DeepSeek Harness as a markdown command card. Use when the user asks to "create a command", "add a slash command", "make /something", or wants a reusable prompt shortcut.
---

# Create Command

Produce one command card and verify it before declaring the task done.

## Step 1 — Scope

Interview with `ask_user_question`:

1. The command's name (kebab-case; it becomes `/name`).
2. Location: project `.agents/commands/<name>.md` or user `~/.agents/commands/<name>.md`.
3. Whether it takes arguments.

## Step 2 — Draft

Write the card. Frontmatter contract:

- `description`: what the command does; shown in the command list.
- `argument-hint`: optional, e.g. `[branch]` or `<pr-number>`; shown as the input placeholder.

Body rules:

- Write the prompt the model should receive when the command runs, in imperative voice.
- Substitute arguments with `$ARGUMENTS`; use positional `$1`, `$2` when the hint declares distinct slots.
- Keep one command per file; a subdirectory creates a namespace (`ns/name`).

## Step 3 — Verify

1. Frontmatter parses as YAML; `description` is present.
2. File name matches the intended command name.
3. Every `$ARGUMENTS` / `$N` placeholder has a matching hint or explanation.
4. No credentials or environment-specific hosts in text.

Report the file path and the exact invocation form (for example `/deploy main --dry-run`).
