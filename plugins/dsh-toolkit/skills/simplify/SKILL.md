---
name: simplify
description: Review changed code for cleanup opportunities and apply the fixes. Use when the user asks to "simplify the code", "clean up the diff", "reduce duplication in my changes", or after finishing a feature before committing.
---

# Simplify

Review the current changes for cleanup opportunities and apply them directly. This skill does not look for correctness bugs; correctness review is a separate workflow.

## Phase 1 — Collect the diff

1. Run `git status` and `git diff HEAD` (include staged changes). When the working tree is clean, fall back to the files touched most recently in this conversation.
2. If the diff is empty everywhere, report that and stop.
3. Capture the full diff text once; pass the same text to every reviewer.

## Phase 2 — Parallel review

Launch three reviewers in one message (the workflow tool, or three `subagent_run` calls) so they run concurrently. Give each reviewer the full diff plus this preamble: "Review only. Return findings as a list; for each finding give file, line, the problem, and a concrete suggested fix. Do not modify files. Skip anything that would change behavior."

- **Reviewer 1 — Reuse.** Existing helpers that the new code could call instead; inline logic duplicating a utility that already exists in the repository (hand-rolled string, path, or environment checks); near-identical blocks inside the diff itself.
- **Reviewer 2 — Quality.** Redundant state; parameters that always travel together; copy-paste variants; stringly-typed values with an existing type; comments that explain what the code already says (keep comments that explain why); dead branches.
- **Reviewer 3 — Efficiency.** Repeated computation or repeated reads of the same file; sequential independent operations that can run concurrently; blocking work on a hot path; polling loops that update without a change guard; existence pre-checks followed by the same operation (TOCTOU); overly broad reads.

## Phase 3 — Apply

Wait for all three reviewers. Merge their findings, dropping duplicates. For each remaining finding:

1. Judge it: real issue, or false positive? False positives are skipped silently.
2. Real issues: apply the fix directly, keeping behavior identical.
3. A fix that would change behavior is downgraded to a suggestion in the summary, never applied.

## Phase 4 — Verify and summarize

1. Re-run the project's typecheck and the tests covering the touched files; a failing fix is reverted immediately.
2. Summarize: findings per reviewer, fixes applied (file + one line each), fixes skipped and why.
