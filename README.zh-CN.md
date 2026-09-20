# dsh-agent-plugins

[dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) 的第一方插件集合。market 是 DeepSeek Harness 的插件，负责按原样安装 agent 插件。`plugins/` 下每个目录都符合 [Agent Plugins v1 规范](https://agent-plugins.org/specification)：根 `plugin.json`、`skills/` 技能、`mcp.json` MCP 服务器，以及 DeepSeek Harness 专属面（命令、代理角色、hooks、LSP 声明）放在 `com.deepseek.harness/` 扩展命名空间。

本仓库是这些插件的创作地，market 插件预置了一条指向本仓库的来源记录——全新安装即可在市场里看到这个集合，不需要任何人粘贴地址。

## 插件

### dsh-plugin-dev — 创作工具集

移植自参考生态的插件开发工具集，并融合技能创建器与评估套件。

| 面 | 资产 |
| --- | --- |
| 技能 | `plugin-structure`、`agent-development`、`command-development`、`hook-development`、`mcp-integration`、`plugin-settings` |
| 创建 + 评估 | `skill-creator`（创建、改进、基准测试与触发优化）、`plugin-evals`（评估套件：评分器与无插件基线） |
| 命令 | `/create-plugin` |
| 代理角色 | `agent-creator`、`plugin-validator`、`skill-reviewer` |

### dsh-toolkit — 内置工具集

| 面 | 资产 |
| --- | --- |
| 技能 | `init-project`（AGENTS.md / AGENTS.local.md 初始化）、`explore`（只读代码库探索）、`verify`（运行时验证）、`simplify`（审查与清理，修复阶段交给 code-simplifier 代理）、`insight`（会话洞察报告，由 dsh-session-insights 管线驱动） |
| 命令 | `/init`、`/explore`、`/verify`、`/code-reviewer` |
| 代理角色 | `code-reviewer`（规范、缺陷、质量——带置信度评分）、`code-simplifier`（保持行为不变的精简） |

## 安装

安装 [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) 即可：market 已预置指向本仓库的来源记录，刷新该来源即可拉取插件并安装。手动把本仓库添加为来源同样可行——开发中的插件走这条路，或用于记录已被删除的 market。

## 仓库规则

- `plugins/<name>/` 就是一个插件：目录本身是 market 安装的东西，手工编写。
- `plugin.json` 遵循 Agent Plugins v1 schema（`$schema` 指明版本）；`com.deepseek.harness` 命名空间在 `extensions` 里声明后，命名空间目录才会被读取——见[命名空间指南](docs/com-deepseek-harness-namespace.md)。
- 技能位于 `skills/<name>/SKILL.md`——一层深度，与规范固定的发现规则一致。
- 命名空间扩展面：`com.deepseek.harness/commands/*.md`、`com.deepseek.harness/agents/*.md`、`com.deepseek.harness/hooks/hooks.json`、`com.deepseek.harness/lsp.json`。
- 移植内容以上游原文为基底；适配仅限机械映射——工具名、文件名、路径、厂商字样。
- 凭证绝不写成字面值；用户级 `~/.agents/mcp.json` 条目用 `${NAME}` 引用凭证。

## 许可

[MIT](LICENSE)。`plugins/dsh-plugin-dev` 移植的内容以 Apache-2.0 分发——[LICENSE](plugins/dsh-plugin-dev/LICENSE) 随插件保留，内置的 `skill-creator` 技能保留[自己的副本](plugins/dsh-plugin-dev/skills/skill-creator/LICENSE.txt)。
