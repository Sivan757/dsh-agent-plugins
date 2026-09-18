---
name: skill-create
description: Create a new agent skill for DeepSeek Harness and verify its frontmatter. Use when the user asks to "create a skill", "write a skill", "add a skill", or wants to turn a repeated workflow into a SKILL.md.
---

# Skill Create

Produce one skill directory and verify it before declaring the task done.

## Step 1 — Scope the skill

Interview the user with `ask_user_question`:

1. What the skill does and its success criteria.
2. Where it lives: project `.agents/skills/<name>/SKILL.md` or user `~/.agents/skills/<name>/SKILL.md`. Ask when ambiguous; default to project scope.
3. Trigger scenarios: when the skill should and should not activate.

## Step 2 — Draft

Write `SKILL.md` with this frontmatter contract:

- `name`: lowercase kebab-case, identical to the directory name.
- `description`: one to three sentences in third person, naming both the action and its trigger phrases. This is the only line that decides whether the model invokes the skill, so it must carry the concrete trigger words.

Rules for the body:

- Imperative steps a competent agent can follow without the conversation that produced the draft.
- Reference sibling files (`references/`, `scripts/`) relative to the skill directory; never invent paths outside the suite.
- Keep the body under ~500 lines; push reference detail into `references/*.md` files and load them on demand.

## Step 3 — Verify

Run through this checklist and fix every failure before finishing:

1. Frontmatter parses as YAML and carries `name` + `description`.
2. Directory name matches the `name` field.
3. `description` names concrete trigger phrases.
4. Every file the body references exists in the skill directory.
5. No credentials, tokens, or environment-specific hosts in text.

## Step 4 — Evaluate (optional)

When the user wants triggering measured: write 3–5 test prompts, run each in a fresh subagent with the skill available, and report whether the skill activated. Iterate on `description` wording until activation is reliable.
