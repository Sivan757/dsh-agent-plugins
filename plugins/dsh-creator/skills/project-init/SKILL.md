---
name: project-init
description: Initialize a DeepSeek Harness project by generating AGENTS.md and AGENTS.local.md from a guided codebase survey. Use when the user asks to "initialize the project", "generate AGENTS.md", "set up project instructions", or runs /project-init.
---

# Project Init

Generate the instruction documents a dsh session reads: project `AGENTS.md` (shared, committed) and `AGENTS.local.md` (personal, git-ignored). Follow the phases in order; use the `ask_user_question` tool for every interactive step.

## Phase 1 — Scope

Ask which files to create:

- Project AGENTS.md only
- Project AGENTS.md + personal AGENTS.local.md
- Both, plus a starter skill under `.agents/skills/`

Stop if a dsh-style `AGENTS.md` already exists and the user did not ask to rewrite it: instead propose targeted improvements as a diff and skip to Phase 4.

## Phase 2 — Survey

Explore the repository before asking anything the code can answer:

1. Read `package.json` / `pyproject.toml` / `go.mod` / `Cargo.toml` / `pom.xml` (whichever exist), README files, Makefile / justfile / task runners, and CI configuration.
2. Detect: build, lint, test, and single-test commands; language and package manager; monorepo layout; formatter config.
3. Read existing instruction files: nested `AGENTS.md` files, `CLAUDE.md`, `.cursor/rules`, `.github/copilot-instructions.md`, `CONTRIBUTING.md`. Record their content so the generated file can reference rather than duplicate them.
4. Note every question the code cannot answer (team conventions, release process, branch policy) as an interview question for Phase 3.

## Phase 3 — Interview

Ask the recorded questions with `ask_user_question`. Questions about the team go to the project scope; questions about personal workflow go to the local scope. Do not mark any option as recommended for team-convention questions.

## Phase 4 — Write

Write `AGENTS.md` with these rules:

- Every line must pass the test: "would removing it cause an agent to make a mistake?" Drop anything the code already answers.
- Record commands (build / lint / test / single test), boundaries the agent must not cross, and pointers to deeper docs instead of copying them.
- Start the file with the header `# AGENTS.md` and a one-line description of what the document is for.
- Propose the file as a diff first; write only after the user accepts.

If Phase 1 included `AGENTS.local.md`: write it, add it to `.gitignore`, and keep it to personal preferences only. In a git worktree whose sibling directories share the same repository, place shared personal instructions in `~/.dsh/AGENTS.md` and leave a one-line `@import`-style pointer in the local file.

## Phase 5 — Report

Summarize what was created, list the commands recorded, and mention that project-level skills live in `.agents/skills/` and hooks in `.agents/hooks/hooks.json` (loaded when the market plugin's project-layout scan is enabled).
