# AGENTS.md

Repository guidance for maintaining the dsh-agent-plugins plugin collection.

## What this repository is

First-party plugin collection for dsh-agent-plugins-market. `plugins/<name>/` directories are Agent Plugins v1 plugins; the market scans this repository, lists each one as an installable suite, and mounts its surfaces into sessions. Nothing here runs at market-install time except skill and command content delivered to the model.

## Plugin contract (Agent Plugins v1)

- Root `plugin.json` declares `$schema` with a supported release (`1.0.0`/`1.1.0`), `name` (kebab-case, 1–64 chars, equal to the directory name), `version`, `description`, `author`, `license`.
- The `com.deepseek.harness` extension namespace is opt-in: declare it under `extensions["com.deepseek.harness"].schemaVersion` or the namespace directory is ignored with a scan note.
- Skills: `skills/<name>/SKILL.md`, one level deep, no deeper discovery.
- Portable `mcp.json`: `$schema` + `mcpServers` top-level keys only; every server carries `type`; placeholders limited to `${PLUGIN_ROOT}` and `${PLUGIN_DATA}`; credential references fail closed here by design.
- Namespace surfaces under `com.deepseek.harness/`: `commands/*.md` (frontmatter `description`, optional `argument-hint`), `agents/*.md` (frontmatter `name`, `description`, optional `model`/`provider`/`reasoningEffort`/`disabled`), `hooks/hooks.json` (event table), `lsp.json` (`lspServers` table; `command` + `extensionToLanguage` required).

## Naming

- Every asset kind a plugin can carry has exactly one creator, named `create-<thing>`: `create-plugin`, `create-skill`, `create-command`, `create-agent`, `create-hook`, `create-mcp`, `create-lsp`.
- Names are lowercase kebab-case, at most two components, and a skill's directory name equals its frontmatter `name`.
- A creator writes the files it owns and defers the rest to the sibling creators.

## Authoring rules

- Ported content keeps its source text as the base; adaptation is mechanical only — tool names, file names, paths, and vendor wording. Do not rewrite structure or drop sections while porting.
- Skill `description` lines decide model triggering: third person, concrete trigger phrases, one to three sentences.
- Skill and command bodies are imperative and self-contained; an agent that has never seen this conversation must be able to follow them.
- Interactive steps go through the `ask_user_question` tool, described in the body as explicit steps.
- Every file a skill body references must exist inside the same plugin; reference them relative to the skill directory.
- No credentials, tokens, or machine-specific paths in any shipped text.

## Validation

Validate a plugin before committing changes to it: load it through the market (local source pointing at this checkout) or run the scan verifier from the market checkout (`node scripts/verify-dsh-agent-plugins-scan.mjs` after `pnpm run build` there), and confirm the surfaces count and empty errors. The repository carries no catalog manifest — discovery walks `plugins/` and reads each plugin's root `plugin.json` (Agent Plugins v1 defines no marketplace format).

## Distribution

This repository is the authoring home for the plugins; dsh-agent-plugins-market ships a source record pointing at it and the user refreshes that source to fetch them. A plugin change therefore lands here and nowhere else — no copy is vendored into the market package, and no sync step exists.

## Repository rules

- Stage changes by path; do not use `git add -A`.
- Version bumps live only in each plugin's `plugin.json` (`version` field, semver).
- Bilingual user-facing surfaces: `README.md` and `README.zh-CN.md` ship as one edit; plugin content (skills, commands, agent cards) is English — it is read by the model.
- `skills/create-skill/` keeps its upstream Apache-2.0 license file; do not fold it into the repository MIT license.
