# dsh-agent-plugins

First-party suite collection for [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market), the DeepSeek Harness plugin that installs agent-plugin suites in place. Every directory under `plugins/` conforms to the [Agent Plugins v1 specification](https://agent-plugins.org/specification): a root `plugin.json`, skills under `skills/`, MCP servers in `mcp.json`, and DeepSeek Harness-specific surfaces (commands, agent roles, hooks, LSP declarations) under the `com.deepseek.harness/` extension namespace.

The repository serves as the market's built-in demo source: it exercises every surface the market mounts, and its suites are ordinary installable units — browse the market page, install, enable, disable like any third-party suite.

## Suites

| Suite | Surfaces | Contents |
| --- | --- | --- |
| [dsh-creator](plugins/dsh-creator/) | skills, commands, agents | Project init, skill/command/agent creation with frontmatter verification, plugin validation, MCP and LSP onboarding, the bundled skill-creator |
| [dsh-review](plugins/dsh-review/) | skills | Simplify: a three-reviewer parallel pass over the current diff that applies cleanup fixes |

## Installing

Add this repository as a source in the dsh-agent-plugins-market page, then install suites from it:

1. Open the market page in the DeepSeek Harness web UI.
2. Add source with this repository's git URL.
3. Install `dsh-creator` or `dsh-review`; mounted surfaces appear on the next discovery pass.

## Repository rules

- `plugins/<name>/` is the suite: the directory itself is what the market installs, and it is authored by hand.
- `plugin.json` follows the Agent Plugins v1 schema (`$schema` names the release); the `com.deepseek.harness` namespace is declared in `extensions` and only then is the namespace directory read — see [the namespace guide](docs/com-deepseek-harness-namespace.md).
- Skills live at `skills/<name>/SKILL.md` — one level deep, exactly as the specification fixes discovery.
- Namespaced extension surfaces: `com.deepseek.harness/commands/*.md`, `com.deepseek.harness/agents/*.md`, `com.deepseek.harness/hooks/hooks.json`, `com.deepseek.harness/lsp.json`.
- Credentials are never written as literal values; user-owned `~/.agents/mcp.json` entries reference credentials as `${NAME}`.

## License

[MIT](LICENSE). The bundled `skill-creator` skill carries its own [Apache-2.0 license](plugins/dsh-creator/skills/skill-creator/LICENSE.txt).
