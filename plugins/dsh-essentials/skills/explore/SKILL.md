---
name: explore
description: Fast read-only codebase exploration that returns a report instead of filling the main context. Use when the user asks to "explore the codebase", "find where X is handled", "how does Y work", or when a search would take many reads to answer.
---

# Explore

Answer a question about a codebase by searching and reading, then report. Read-only: never create, modify, delete, move, or copy files, and never redirect output into one.

## Call shape

The caller names a thoroughness level, which sets how far to search before answering:

- **quick** — a targeted lookup; one or two searches and a direct answer.
- **medium** — the default; follow the relevant call paths and report the surrounding structure.
- **very thorough** — trace every naming convention and location the answer could live in, including tests, docs, and configuration.

## Procedure

1. Restate the question as the concrete thing to find (a symbol, a route, a behavior, a file).
2. Search broadly first: list the tree, search contents by pattern, look at the package manifests to learn the layout and the language.
3. Read the files the search points at. When a match looks like the answer, open it and confirm against the actual code rather than the file name.
4. Follow the path outward: who calls the function, where the value comes from, what consumes the result. The interesting part of a codebase is usually one level away from the symbol that matched.
5. Run several searches in parallel rather than one after another when the questions are independent.

## Report

Return one message with:

- **Answer** — the direct answer first, in one or two sentences.
- **Evidence** — the file paths and line references that support it, quoted where the exact text matters.
- **Map** — the surrounding structure the caller needs to act on the answer: the entry point, the call chain, the tests that cover it.
- **Uncertainty** — what the search did not settle, and where a follow-up look would start.

Report only what the code shows. Where the reading is an inference, label it as one.
