---
name: create-readme
description: Write a project README that explains what the project is, how to run it, and what a newcomer needs. Use when the user asks to "create a README", "write the README", "document this project for newcomers", or when a repository is missing one.
---

# Create README

Survey the project, then write the README it actually needs. A README answers, in order: what this is, why someone would use it, how to get it running, and where to go next.

## Step 1 — Survey

Read before writing:

1. The manifests (package.json, pyproject.toml, go.mod, Cargo.toml, pom.xml) for the name, description, entry points, dependencies, and the scripts that actually run things.
2. The source tree's top level and the main directories, to say what the project contains in the reader's terms.
3. Existing documentation, configuration samples, and any script that builds, tests, or starts the project — the README's commands must be the ones the project really uses.
4. An existing README, when there is one: keep its correct content, fix what the code contradicts, and report what changed.

Ask the user only for what the code cannot answer: who the project is for, what makes it different, and whether any section is off limits.

## Step 2 — Write

Structure, dropping whatever does not apply:

- **Title and one-line description** — the project's name and what it does, in one sentence a stranger can read.
- **What it does** — a short paragraph and, where it helps, the few capabilities that matter.
- **Requirements** — the runtime and tools needed before installation.
- **Install** — the exact commands, copy-pasteable.
- **Usage** — the shortest path to a working result, one example with its real output.
- **Configuration** — the settings a user will actually touch, with defaults and where they live.
- **Development** — build, test, and lint commands, plus anything unconventional about the setup.
- **License** — the license, named, with a link to the file.

Rules:

- Name only commands and paths that exist. Run the install and usage commands where the environment allows; when a command cannot be run, say so in the reply rather than presenting it as verified.
- Keep code fences copy-pasteable: no ellipses standing in for real arguments unless the placeholder is obvious and labeled.
- Write in the second person and the present tense. Skip badges, emoji headers, and sections that only restate the title.
- Leave license, contribution, and changelog text to their own files: link them instead of reproducing them.

## Step 3 — Deliver

Write `README.md` at the project root unless the user names another path or another language. Where the repository ships READMEs in more than one language, offer the translated counterpart as a follow-up edit rather than writing it unasked.

In the reply, list the sections written, the commands you verified by running, and every place where a claim rests on reading rather than execution.
