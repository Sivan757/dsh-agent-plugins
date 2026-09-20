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

Extract the core intent first: the fundamental purpose, key responsibilities, and success criteria — both the explicit requirements and the implicit needs (for a code-review agent, assume recently written code unless the user says otherwise).

Write the card. Frontmatter contract:

- `name`: the role name used to target the agent. Lowercase letters, numbers, and hyphens; typically 2–4 words joined by hyphens, 3–50 characters; clearly indicates the agent's primary function; avoids generic terms like "helper" or "assistant".
- `description`: when to use this agent; the catalog presents it to the parent model, and this line alone decides delegation.
- `model` / `provider` / `reasoningEffort`: optional; an exact `provider` + `model` pair routes the child to that model, anything else inherits the parent route. Omit unless the user asks.
- `disabled: true`: optional switch to keep a card without activating it.

Body rules:

- The body is the agent's system prompt: identity, procedure, output contract.
- Design an expert persona that embodies deep domain knowledge relevant to the task; the persona guides the agent's decision-making.
- Architect the instructions: clear behavioral boundaries, specific methodologies for task execution, edge cases anticipated, quality-control and self-verification steps, and a clear escalation or fallback path.
- Self-contained: the child never sees the parent conversation beyond the task prompt it receives.
- State the expected result format so the parent can consume the final message directly.

Draft 2–4 `<example>` blocks while writing, and fold what they teach into the final `description` — the description is what the parent model actually reads. Each example shows a different phrasing of the same intent, and covers both explicit and proactive triggering:

```
<example>
Context: the situation the user is in
user: "<what the user says>"
assistant: "<how the parent responds, naming this agent>"
<commentary>
Why this agent should trigger here.
</commentary>
</example>
```

## Step 3 — Verify

1. Frontmatter parses as YAML; `name` and `description` present.
2. `name` matches `^[a-z0-9][a-z0-9-]*$` and is 3–50 characters.
3. `description` is at least 10 characters, in third person ("Use this agent when..." rather than "Load this agent when..."), and names the concrete delegation triggers the examples showed.
4. Body is at least 20 characters and does not reference parent-only context.
5. `model`/`provider`, when present, are an exact pair or `model: inherit` without a provider.
