# dsh-agent-plugins

[dsh-agent-plugins-market](https://github.com/Sivan757/dsh-agent-plugins-market) 的第一方套件集合。market 是 DeepSeek Harness 的插件，负责按原样安装 agent-plugin 套件。`plugins/` 下每个目录都符合 [Agent Plugins v1 规范](https://agent-plugins.org/specification)：根 `plugin.json`、`skills/` 技能、`mcp.json` MCP 服务器，以及 DeepSeek Harness 专属面（命令、代理角色、hooks、LSP 声明）放在 `com.deepseek.harness/` 扩展命名空间。

本仓库同时是 market 的内置演示源：它覆盖 market 挂载的每一类面，其中的套件都是普通的可安装单元——在市场页浏览、安装、启用、停用，与第三方套件没有区别。

## 套件

| 套件 | 面 | 内容 |
| --- | --- | --- |
| [dsh-creator](plugins/dsh-creator/) | skills、commands、agents | 项目初始化、技能/命令/代理创建与 frontmatter 校验、插件校验、MCP 与 LSP 接入、收编的 skill-creator |
| [dsh-review](plugins/dsh-review/) | skills | simplify：对当前变更做三审查代理并行清理并应用修复 |

## 安装

在 dsh-agent-plugins-market 页面把本仓库添加为源，然后安装套件：

1. 打开 DeepSeek Harness Web 界面的市场页。
2. 用本仓库的 git URL 添加源。
3. 安装 `dsh-creator` 或 `dsh-review`；挂载的面在下一轮发现时出现。

## 仓库规则

- `plugins/<name>/` 就是套件：目录本身是 market 安装的东西，手工编写。
- `plugin.json` 遵循 Agent Plugins v1 schema（`$schema` 指明版本）；`com.deepseek.harness` 命名空间在 `extensions` 里声明后，命名空间目录才会被读取——见[命名空间指南](docs/com-deepseek-harness-namespace.md)。
- 技能位于 `skills/<name>/SKILL.md`——一层深度，与规范固定的发现规则一致。
- 命名空间扩展面：`com.deepseek.harness/commands/*.md`、`com.deepseek.harness/agents/*.md`、`com.deepseek.harness/hooks/hooks.json`、`com.deepseek.harness/lsp.json`。
- 凭证绝不写成字面值；用户级 `~/.agents/mcp.json` 条目用 `${NAME}` 引用凭证。

## 许可

[MIT](LICENSE)。内置的 `skill-creator` 技能自带 [Apache-2.0 许可](plugins/dsh-creator/skills/skill-creator/LICENSE.txt)。
