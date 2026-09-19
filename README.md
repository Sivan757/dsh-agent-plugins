# dsh-agent-plugins

First-party plugin collection for [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market), the DeepSeek Harness plugin that installs agent plugins in place. Every directory under `plugins/` conforms to the [Agent Plugins v1 specification](https://agent-plugins.org/specification): a root `plugin.json`, skills under `skills/`, MCP servers in `mcp.json`, and DeepSeek Harness-specific surfaces (commands, agent roles, hooks, LSP declarations) under the `com.deepseek.harness/` extension namespace.

The repository is the authoring home for these plugins, and the market plugin ships a pre-registered source record pointing at it — a fresh install lists this collection without anyone pasting a URL.

## dsh-toolkit

| Group | Assets |
| --- | --- |
| Creation | `create-plugin`, `create-skill`, `create-command`, `create-agent`, `create-hook`, `create-mcp`, `create-lsp` |
| Validation | `validate-plugin` |
| Project | `init-project`, `create-readme` |
| Every day | `explore`, `verify`, `insight`, `simplify` |
| Commands | `/init`, `/explore`, `/verify` |
| Agent roles | `explorer`, `architect`, `asset-validator` |

Every asset kind a plugin can carry has a creation skill: a plugin, a skill, a slash command, an agent role, a hook, an MCP server, an LSP declaration. `create-plugin` builds the directory and walks the surfaces; each surface skill writes its own files; `validate-plugin` checks the result against the specification.

## Installing

Installing [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) is enough: the market presets a source record for this repository, so refresh that source to fetch the plugin and install it from there. Adding this repository as a source by hand works too — the path for a plugin under development, or for a market whose record was removed.

## Repository rules

- `plugins/<name>/` is one plugin: the directory itself is what the market installs, and it is authored by hand.
- `plugin.json` follows the Agent Plugins v1 schema (`$schema` names the release); the `com.deepseek.harness` namespace is declared in `extensions` and only then is the namespace directory read — see [the namespace guide](docs/com-deepseek-harness-namespace.md).
- Skills live at `skills/<name>/SKILL.md` — one level deep, exactly as the specification fixes discovery.
- Namespaced extension surfaces: `com.deepseek.harness/commands/*.md`, `com.deepseek.harness/agents/*.md`, `com.deepseek.harness/hooks/hooks.json`, `com.deepseek.harness/lsp.json`.
- Credentials are never written as literal values; user-owned `~/.agents/mcp.json` entries reference credentials as `${NAME}`.

## License

[MIT](LICENSE). The bundled `create-skill` skill carries its own [Apache-2.0 license](plugins/dsh-toolkit/skills/create-skill/LICENSE.txt).
