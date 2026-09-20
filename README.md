# dsh-agent-plugins

First-party plugin collection for [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market), the DeepSeek Harness plugin that installs agent plugins in place. Every directory under `plugins/` conforms to the [Agent Plugins v1 specification](https://agent-plugins.org/specification): a root `plugin.json`, skills under `skills/`, MCP servers in `mcp.json`, and DeepSeek Harness-specific surfaces (commands, agent roles, hooks, LSP declarations) under the `com.deepseek.harness/` extension namespace.

The repository is the authoring home for these plugins, and the market plugin ships a pre-registered source record pointing at it — a fresh install lists this collection without anyone pasting a URL.

## Plugins

### dsh-plugin-dev — authoring toolkit

Ported and adapted from the plugin-development toolkit of the reference ecosystem, with the skill creator and eval suites fused in.

| Surface | Assets |
| --- | --- |
| Skills | `plugin-structure`, `agent-development`, `command-development`, `hook-development`, `mcp-integration`, `plugin-settings` |
| Creation + evaluation | `skill-creator` (create, improve, benchmark, and optimize skill triggering), `plugin-evals` (eval suites with graders and a no-plugin baseline) |
| Commands | `/create-plugin` |
| Agent roles | `agent-creator`, `plugin-validator`, `skill-reviewer` |

### dsh-toolkit — built-in toolset

| Surface | Assets |
| --- | --- |
| Skills | `init-project` (AGENTS.md / AGENTS.local.md setup), `explore` (read-only codebase exploration), `verify` (runtime verification), `simplify` (review and cleanup, delegating the fix pass to the code-simplifier agent), `insight` (session insight reports driven by the dsh-session-insights pipeline) |
| Commands | `/init`, `/explore`, `/verify`, `/code-reviewer` |
| Agent roles | `code-reviewer` (guidelines, bugs, quality — confidence-scored findings), `code-simplifier` (behavior-preserving refinement) |

## Installing

Installing [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) is enough: the market presets a source record for this repository, so refresh that source to fetch the plugins and install them from there. Adding this repository as a source by hand works too — the path for a plugin under development, or for a market whose record was removed.

## Repository rules

- `plugins/<name>/` is one plugin: the directory itself is what the market installs, and it is authored by hand.
- `plugin.json` follows the Agent Plugins v1 schema (`$schema` names the release); the `com.deepseek.harness` namespace is declared in `extensions` and only then is the namespace directory read — see [the namespace guide](docs/com-deepseek-harness-namespace.md).
- Skills live at `skills/<name>/SKILL.md` — one level deep, exactly as the specification fixes discovery.
- Namespaced extension surfaces: `com.deepseek.harness/commands/*.md`, `com.deepseek.harness/agents/*.md`, `com.deepseek.harness/hooks/hooks.json`, `com.deepseek.harness/lsp.json`.
- Ported content keeps its source text as the base; adaptation is mechanical only — tool names, file names, paths, and vendor wording.
- Credentials are never written as literal values; user-owned `~/.agents/mcp.json` entries reference credentials as `${NAME}`.

## License

[MIT](LICENSE). `plugins/dsh-plugin-dev` ports content distributed under the Apache-2.0 license — its [LICENSE](plugins/dsh-plugin-dev/LICENSE) travels with the plugin, and the bundled `skill-creator` skill keeps [its own copy](plugins/dsh-plugin-dev/skills/skill-creator/LICENSE.txt).
