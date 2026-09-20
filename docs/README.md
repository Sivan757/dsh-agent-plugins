# Docs

- [Specification](https://agent-plugins.org/specification) — Agent Plugins v1, the format every suite here conforms to.
- [The com.deepseek.harness namespace](com-deepseek-harness-namespace.md) — how this repository declares commands, agent roles, hooks, LSP declarations and per-server MCP policy on top of the portable core; the authoritative contract is the market plugin's [namespace spec](https://github.com/Sivan757/dsh-agent-plugins-market/blob/dev/schemas/com.deepseek.harness/spec.md).
- [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) — the installer plugin that scans, installs, and mounts these suites; its `docs/reference/plugin-development-standard.md` covers the market plugin itself.
- Vendored schemas: [plugin.schema.json (1.0.0)](https://github.com/agentplugins/agent-plugins-spec/tree/main/schemas/1.0.0), [mcp.schema.json (1.0.0)](https://github.com/agentplugins/agent-plugins-spec/tree/main/schemas/1.0.0).
