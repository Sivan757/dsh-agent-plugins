---
description: Initialize this project for DeepSeek Harness by generating AGENTS.md through a guided survey
argument-hint: [--local]
---
Initialize the project instruction documents for dsh. Follow the project-init skill's phases exactly:

1. If `AGENTS.md` already exists with dsh-style content, propose targeted improvements as a diff and stop after the user accepts or declines.
2. Otherwise survey the repository, interview me about everything the code cannot answer, then write `AGENTS.md` (and `AGENTS.local.md` when the arguments include `--local`, adding it to `.gitignore`).

Apply every rule from the project-init skill: instruction lines only where removal would cause an agent mistake, commands recorded verbatim, deeper detail referenced rather than copied.
