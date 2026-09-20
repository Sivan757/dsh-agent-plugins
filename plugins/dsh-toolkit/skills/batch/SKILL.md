---
name: batch
description: Research and plan a large mechanical change, then execute it as 5–30 isolated worktree agents running in parallel, each opening its own PR. Use when the user runs /batch with an instruction for a migration, refactor, or bulk rename across many files.
---

# Batch

Execute one large, mechanically decomposable change — a migration, a refactor, a bulk rename — as parallel isolated work units. Decompose, run one background agent per unit in its own git worktree, and collect a PR from each.

This skill runs only on the user's explicit invocation with an instruction. When invoked without an instruction:

```text
Provide an instruction describing the batch change you want to make.

Examples:
  /batch migrate from react to vue
  /batch replace all uses of lodash with native equivalents
  /batch add type annotations to all untyped function parameters
```

When the current directory is not a git repository:

```text
This is not a git repository. The `/batch` command requires a git repo because it spawns agents in isolated git worktrees and creates PRs from each. Initialize a repo first, or run this from inside an existing one.
```

## Phase 1: Plan (plan mode)

1. Enter plan mode first — the user approves the decomposition before anything runs.
2. Launch foreground research subagents to survey the change: every file and call site the instruction touches, and the code conventions the workers must follow.
3. Decompose into independent units. A unit qualifies only when all three hold:
   - it can be implemented alone in an isolated git worktree,
   - it can merge alone, without depending on a sibling unit's PR,
   - its size is roughly even with the others.
   Split by directory or module, never an arbitrary file list. Scale the count to the work: a handful of files lands near **5** units, hundreds of files approaches **30** (5–30).
4. Fix the end-to-end verification recipe before writing the plan. Look, in order, for: browser automation, a CLI or tmux verification skill, a dev-server-plus-curl pattern, or an existing e2e suite. If none exists, ask the user to choose one with `ask_user_question` — do not skip e2e.
5. The plan file carries: the research summary, the numbered work units (title, file scope, one-line change description), the e2e recipe, and the complete work-instruction template that goes to every agent.

Present the plan for approval and stop until the user accepts.

## Phase 2: Launch the workers

After the plan is approved, launch one background agent per unit — every launch in the same message, each with worktree isolation and background execution, so they all run in parallel.

Each worker prompt must be fully self-contained:

- the overall goal,
- this unit's task, copied verbatim from the plan,
- the code conventions the research found,
- the e2e recipe, or the reason this unit skips it,
- the worker instructions below, verbatim:

```text
After you finish implementing the change:
1. **Simplify** — Invoke the `Skill` tool with `skill: "simplify"` to review and clean up your changes.
2. **Run unit tests** — Run the project's test suite (check for package.json scripts, Makefile targets, or common commands like `npm test`, `bun test`, `pytest`, `go test`). If tests fail, fix them.
3. **Test end-to-end** — Follow the e2e test recipe from the coordinator's prompt (below). If the recipe says to skip e2e for this unit, skip it.
4. **Commit and push** — Commit all changes with a clear message, push the branch, and create a PR with `gh pr create`. Use a descriptive title. If `gh` is not available or the push fails, note it in your final message.
5. **Report** — End with a single line: `PR: <url>` so the coordinator can track it. If no PR was created, end with `PR: none — <reason>`.
```

## Phase 3: Track and report

Render the initial status table (unit number, description, status, PR). As each background agent's completion notice arrives, parse the `PR: <url>` line and re-render the table with `done` or `failed`; keep a short failure note for units that produced no PR.

When every agent has reported, render the final table and a one-line summary.

This skill writes nothing itself: the workers change code inside their own worktrees and open the PRs.
