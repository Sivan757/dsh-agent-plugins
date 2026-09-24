# AGENTS.md

Repository guidance for maintaining the dsh-agent-plugins plugin collection.

## What this repository is

First-party plugin collection for dsh-agent-plugins-market. `plugins/<name>/` directories are Agent Plugins v1 plugins; the market scans this repository, lists each one as an installable suite, and mounts its surfaces into sessions. Nothing here runs at market-install time except skill and command content delivered to the model.

Three plugins, three goals:

- `plugin-dev` — the authoring toolkit for people building plugins (structure guidance, component development skills, the skill creator with its evaluation tooling, plugin eval suites, the guided create-plugin command, and the reviewer agents).
- `toolkit` — the built-in toolset for user projects (project init, exploration, verification, simplification, session insights, code review, AGENTS.md auditing, automation recommendations).
- `engineering` — the engineering workflows for building and modernizing systems (guided feature development, legacy modernization with workflow scripts, interactive HTML playgrounds, frontend design direction, security review).

## Plugin contract (Agent Plugins v1)

- Root `plugin.json` declares `$schema` with a supported release (`1.0.0`/`1.1.0`), `name` (kebab-case, 1–64 chars, equal to the directory name), `version`, `description`, `author`, `license`.
- The `com.deepseek.harness` extension namespace is opt-in: declare it under `extensions["com.deepseek.harness"].schemaVersion` or the namespace directory is ignored with a scan note.
- Skills: `skills/<name>/SKILL.md`, one level deep, no deeper discovery.
- Portable `mcp.json`: `$schema` + `mcpServers` top-level keys only; every server carries `type`; placeholders limited to `${PLUGIN_ROOT}` and `${PLUGIN_DATA}`; credential references fail closed here by design.
- Namespace surfaces under `com.deepseek.harness/`: `commands/*.md` (frontmatter `description`, optional `argument-hint`), `agents/*.md` (frontmatter `name`, `description`, optional `model`/`provider`/`reasoningEffort`/`disabled`), `hooks/hooks.json` (event table), `lsp.json` (`lspServers` table; `command` + `extensionToLanguage` required).
- A plugin may also carry data directories at its root (`workflows/`, `scripts/`, `assets/`); commands and hooks reach them through `${PLUGIN_ROOT}`. The market reads none of them.

## Naming

- Ported skills keep their source names (`plugin-structure`, `skill-creator`, …); a skill's directory name equals its frontmatter `name`.
- Names are lowercase kebab-case, changed away from a vendor word only when the upstream name carries one (`claude-md-improver` → `agents-md-audit`, `claude-automation-recommender` → `automation-recommender`).
- Every asset kind a plugin can carry has exactly one creator at most; a creator writes the files it owns and defers the rest.

## Authoring rules

- Ported content keeps its source text as the base; adaptation is mechanical only — tool names, file names, paths, frontmatter keys, and vendor wording. Do not rewrite structure or drop sections while porting.
- Adaptation mappings: `Agent`/`Task` → `subagent_run`, `Skill` → `skill`, `AskUserQuestion` → `ask_user_question`, `TodoWrite` → `todo_write`, `KillShell` → `job_kill`, `BashOutput`/`TaskOutput` → `job_output`, `ExitPlanMode` → `exit_plan_mode`, `CLAUDE.md` → `AGENTS.md`, `${CLAUDE_PLUGIN_ROOT}` → `${PLUGIN_ROOT}`, `${CLAUDE_PROJECT_DIR}` → `${DSH_PROJECT_DIR}`, `.claude/` → `.agents/`, `.claude-plugin/plugin.json` → `plugin.json`, `commands/`+`agents/`+`hooks/` → `com.deepseek.harness/…`, `.mcp.json` → `mcp.json`; hook events are the seven the host bridge supports (`SessionStart`, `UserPromptSubmit`, `PreToolUse`, `PostToolUse`, `Stop`, `SubagentStart`, `SubagentStop`); model shorthand (`sonnet`/`haiku`/`opus`) is replaced by `inherit` or an exact `provider` + `model` pair; the Artifact tool becomes a local file handed over with `present`.
- A Claude Code `Workflow` script ports to the harness `workflow` tool by splitting it: the `export const meta = { … }` block moves to `<name>.meta.json` and is passed as the `meta` argument, while the remaining body is passed unchanged as `script`. The hook vocabulary is identical (`agent()`, `pipeline()`, `parallel()`, `phase()`, `log()`, `args`).
- Frontmatter values are quoted strings whenever the value starts with `[`, `{`, `*`, `&`, `!`, `%`, `@`, `>`, `|`, or contains `: `. The `argument-hint` key in particular must always be quoted: `argument-hint: "[question] [quick|medium]"`. Unquoted, a bracket value is a YAML flow sequence — two of them in one value is a parse error that stops the command from loading, and a single one becomes a list, which the market reads as no hint at all.
- Only these frontmatter keys do anything: skills read `name`/`description` (and `disable-model-invocation`/`user-invocable`), commands read `description`/`argument-hint`, agent cards read `name`/`description`/`model`/`provider`/`reasoningEffort`/`disabled`. Keys ported from another platform (`allowed-tools`, `context`, `agent`, `color`, `effort`, `initialPrompt`, `permissionMode`, `tools`) are inert here: map `effort` to `reasoningEffort`, fold `initialPrompt` into the body, and leave the rest out of new content.
- Skill `description` lines decide model triggering: third person, concrete trigger phrases, one to three sentences.
- Skill and command bodies are imperative and self-contained; an agent that has never seen this conversation must be able to follow them.
- Interactive steps go through the `ask_user_question` tool, described in the body as explicit steps.
- Every file a skill body references must exist inside the same plugin; reference them relative to the skill directory.
- No credentials, tokens, or machine-specific paths in any shipped text.

## Licensing

Port only content whose license permits redistribution and use here. The plugins under `claude-plugins-official` are Apache-2.0 except `claude-security`, which carries a proprietary notice (no distribution; no use with a non-Anthropic product). `claude-security` is therefore excluded from this collection; the security capability here is our own `security-reviewer` agent card.

- `plugin-dev` and `engineering` each carry the Apache-2.0 license of the content they port; do not fold it into the repository MIT license.
- The bundled `skill-creator` skill keeps its own license copy.
- `toolkit`'s `insight` skill vendors MIT-licensed analysis and report code into `skills/insight/engine/` and `skills/insight/assets/`, and reads session logs through the host's MIT-licensed `@deepseek-ai/dsh-session-query` and persistence packages at run time. Keep its `LICENSE` attribution with the vendored files; when the upstream project publishes a new release, re-check the engine it replaces.

## Validation

Validate a plugin before committing changes to it: load it through the market (local source pointing at this checkout) or run the scan verifier from the market checkout (`node scripts/verify-dsh-agent-plugins-scan.mjs` after `pnpm run build` there), and confirm the surfaces count and empty errors. The repository carries no catalog manifest — discovery walks `plugins/` and reads each plugin's root `plugin.json` (Agent Plugins v1 defines no marketplace format).

## Distribution

This repository is the authoring home for the plugins; dsh-agent-plugins-market ships a source record pointing at it and the user refreshes that source to fetch them. A plugin change therefore lands here and nowhere else — no copy is vendored into the market package, and no sync step exists.

## Repository rules

- Stage changes by path; do not use `git add -A`.
- Version bumps live only in each plugin's `plugin.json` (`version` field, semver).
- Bilingual user-facing surfaces: `README.md` and `README.zh-CN.md` ship as one edit; plugin content (skills, commands, agent cards) is English — it is read by the model.
