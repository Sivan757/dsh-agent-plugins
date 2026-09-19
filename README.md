# dsh-agent-plugins

First-party suite collection for [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market), the DeepSeek Harness plugin that installs agent-plugin suites in place. Every directory under `plugins/` conforms to the [Agent Plugins v1 specification](https://agent-plugins.org/specification): a root `plugin.json`, skills under `skills/`, MCP servers in `mcp.json`, and DeepSeek Harness-specific surfaces (commands, agent roles, hooks, LSP declarations) under the `com.deepseek.harness/` extension namespace.

The repository is the authoring home for these suites, and the market plugin ships a pre-registered source record pointing at it — a fresh install lists this collection without anyone pasting a URL.

## Suites

| Suite | Surfaces | Contents |
| --- | --- | --- |
| [dsh-creator](plugins/dsh-creator/) | skills, commands, agents | Project init, skill/command/agent creation with frontmatter verification, plugin validation, MCP and LSP onboarding, the bundled skill-creator |
| [dsh-essentials](plugins/dsh-essentials/) | skills, commands, agents | Everyday capabilities: codebase exploration, runtime verification, session insight reports, README generation, plus the explorer and architect agent roles |
| [dsh-review](plugins/dsh-review/) | skills | Simplify: a three-reviewer parallel pass over the current diff that applies cleanup fixes |

## Installing

Installing [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) is enough: the market presets a source record for this repository, so refresh that source to fetch the suites and install from there. Adding this repository as a source by hand works too — the path for a suite under development, or for a market whose record was removed.

## Repository rules

- `plugins/<name>/` is the suite: the directory itself is what the market installs, and it is authored by hand.
- `plugin.json` follows the Agent Plugins v1 schema (`$schema` names the release); the `com.deepseek.harness` namespace is declared in `extensions` and only then is the namespace directory read — see [the namespace guide](docs/com-deepseek-harness-namespace.md).
- Skills live at `skills/<name>/SKILL.md` — one level deep, exactly as the specification fixes discovery.
- Namespaced extension surfaces: `com.deepseek.harness/commands/*.md`, `com.deepseek.harness/agents/*.md`, `com.deepseek.harness/hooks/hooks.json`, `com.deepseek.harness/lsp.json`.
- Credentials are never written as literal values; user-owned `~/.agents/mcp.json` entries reference credentials as `${NAME}`.

## License

[MIT](LICENSE). The bundled `skill-creator` skill carries its own [Apache-2.0 license](plugins/dsh-creator/skills/skill-creator/LICENSE.txt).
