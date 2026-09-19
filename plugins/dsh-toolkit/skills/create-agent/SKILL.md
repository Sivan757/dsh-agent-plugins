---
name: create-agent
description: Create an agent role card for DeepSeek Harness subagents. Use when the user asks to "create an agent", "add a subagent role", "make a reviewer agent", or wants a named agent persona.
---

# Create Agent

Produce one agent role card and verify it before declaring the task done.

## Step 1 — Scope

Interview with `ask_user_question`:

1. What the agent does and when to delegate to it.
2. Location: project `.agents/agents/<name>.md` or user `~/.agents/agents/<name>.md`.

## Step 2 — Draft

Write the card. Frontmatter contract:

- `name`: the role name used to target the agent.
- `description`: when to use this agent; the catalog presents it to the parent model.
- `model` / `provider` / `reasoningEffort`: optional; an exact `provider` + `model` pair routes the child to that model, anything else inherits the parent route. Omit unless the user asks.
- `disabled: true`: optional switch to keep a card without activating it.

Body rules:

- The body is the agent's system prompt: identity, procedure, output contract.
- Self-contained: the child never sees the parent conversation beyond the task prompt it receives.
- State the expected result format so the parent can consume the final message directly.

## Step 3 — Verify

1. Frontmatter parses as YAML; `name` and `description` present.
2. `name` matches `^[a-z0-9][a-z0-9-]*$` and is 3–50 characters.
3. `description` is at least 10 characters and names concrete delegation triggers.
4. Body is at least 20 characters and does not reference parent-only context.
5. `model`/`provider`, when present, are an exact pair or `model: inherit` without a provider.
