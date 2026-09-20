# dsh-agent-plugins

[dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) 的第一方插件集合。market 是 DeepSeek Harness 的插件，负责按原样安装 agent 插件。`plugins/` 下每个目录都符合 [Agent Plugins v1 规范](https://agent-plugins.org/specification)：根 `plugin.json`、`skills/` 技能、`mcp.json` MCP 服务器，以及 DeepSeek Harness 专属面（命令、代理角色、hooks、LSP 声明）放在 `com.deepseek.harness/` 扩展命名空间。

本仓库是这些插件的创作地，market 插件预置了一条指向本仓库的来源记录——全新安装即可在市场里看到这个集合，不需要任何人粘贴地址。

## dsh-toolkit

| 分组 | 资产 |
| --- | --- |
| 创建 | `create-plugin`、`create-skill`、`create-command`、`create-agent`、`create-hook`、`create-mcp`、`create-lsp` |
| 评估 | `eval-plugin` |
| 项目 | `init-project`、`create-readme` |
| 日常 | `explore`、`verify`、`simplify`、`batch`、`insight` |
| 校验 | `validate-plugin` |
| 命令 | `/init`、`/explore`、`/verify` |
| 代理角色 | `explorer`、`architect`、`asset-validator` |

插件能承载的每一种资产都有对应的创建技能：插件、技能、斜杠命令、代理角色、hook、MCP 服务器、LSP 声明。`create-plugin` 建目录并规划各个面，每个面的技能写自己的文件，`validate-plugin` 按规范校验结果。`eval-plugin` 度量结果：评估用例、评分器、多次运行与无插件基线。

## 安装

安装 [dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) 即可：market 已预置指向本仓库的来源记录，刷新该来源即可拉取插件并安装。手动把本仓库添加为来源同样可行——开发中的插件走这条路，或用于记录已被删除的 market。

## 仓库规则

- `plugins/<name>/` 就是一个插件：目录本身是 market 安装的东西，手工编写。
- `plugin.json` 遵循 Agent Plugins v1 schema（`$schema` 指明版本）；`com.deepseek.harness` 命名空间在 `extensions` 里声明后，命名空间目录才会被读取——见[命名空间指南](docs/com-deepseek-harness-namespace.md)。
- 技能位于 `skills/<name>/SKILL.md`——一层深度，与规范固定的发现规则一致。
- 命名空间扩展面：`com.deepseek.harness/commands/*.md`、`com.deepseek.harness/agents/*.md`、`com.deepseek.harness/hooks/hooks.json`、`com.deepseek.harness/lsp.json`。
- 凭证绝不写成字面值；用户级 `~/.agents/mcp.json` 条目用 `${NAME}` 引用凭证。

## 许可

[MIT](LICENSE)。内置的 `create-skill` 技能自带 [Apache-2.0 许可](plugins/dsh-toolkit/skills/create-skill/LICENSE.txt)。
