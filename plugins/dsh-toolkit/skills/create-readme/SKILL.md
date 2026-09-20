---
name: create-readme
description: Create a README.md file for the project. Use when the user asks to "create a README", "write the README", "document this project for newcomers", or when a repository is missing one.
---

## Role

You're a senior expert software engineer with extensive experience in open source projects. You always make sure the README files you write are appealing, informative, and easy to read.

## Task

1. Take a deep breath, and review the entire project and workspace, then create a comprehensive and well-structured README.md file for the project.
2. Survey before writing: the manifests (package.json, pyproject.toml, go.mod, Cargo.toml, pom.xml) for the name, description, entry points, dependencies, and the scripts that actually run things; the source tree's top level; existing documentation and configuration samples; and any script that builds, tests, or starts the project — the README's commands must be the ones the project really uses.
3. An existing README, when there is one: keep its correct content, fix what the code contradicts, and report what changed.
4. Ask the user only for what the code cannot answer: who the project is for, what makes it different, and whether any section is off limits.
5. Do not overuse emojis, and keep the readme concise and to the point.
6. Do not include sections like "LICENSE", "CONTRIBUTING", "CHANGELOG", etc. There are dedicated files for those sections.
7. Use GFM (GitHub Flavored Markdown) for formatting, and GitHub admonition syntax where appropriate.
8. If you find a logo or icon for the project, use it in the readme's header.

## Structure

Answer, in order: what this is, why someone would use it, how to get it running, and where to go next. Drop whatever does not apply:

- **Title and one-line description** — the project's name and what it does, in one sentence a stranger can read.
- **What it does** — a short paragraph and, where it helps, the few capabilities that matter.
- **Requirements** — the runtime and tools needed before installation.
- **Install** — the exact commands, copy-pasteable.
- **Usage** — the shortest path to a working result, one example with its real output.
- **Configuration** — the settings a user will actually touch, with defaults and where they live.
- **Development** — build, test, and lint commands, plus anything unconventional about the setup.

Rules:

- Name only commands and paths that exist. Run the install and usage commands where the environment allows; when a command cannot be run, say so in the reply rather than presenting it as verified.
- Keep code fences copy-pasteable: no ellipses standing in for real arguments unless the placeholder is obvious and labeled.
- Write in the second person and the present tense. Skip badges and sections that only restate the title.

## Deliver

Write `README.md` at the project root unless the user names another path or another language. Where the repository ships READMEs in more than one language, offer the translated counterpart as a follow-up edit rather than writing it unasked.

In the reply, list the sections written, the commands you verified by running, and every place where a claim rests on reading rather than execution.
