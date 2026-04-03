# 主流 Vibe Coding 工具项目级配置文件总览

本文参考现有的 [Codex 项目级配置清单](./codex-project-config-guide.md) 和 [Claude 项目级配置清单](./claude-project-config-guide.md) 的写法，按截至 **2026-04-03** 我能核验到的官方公开资料，系统整理一份“主流 vibe coding 工具的项目级配置文件总览”。

这里的“项目级配置文件”特指：

- 可以放进仓库并随项目一起版本化
- 会直接影响 AI 的项目上下文、权限、工具、规则、MCP、子代理或提示词行为
- 不是纯本机 UI 偏好，也不是单纯的会话缓存

先给结论：如果你的目标是做一套**跨工具可迁移**的项目级 AI 配置，当前最值得优先掌握的是这 11 类工具：

1. Codex
2. Claude Code
3. Cursor
4. Gemini CLI
5. GitHub Copilot
6. Windsurf
7. Kiro
8. Aider
9. Cline
10. Roo Code
11. Continue

但它们的“项目级配置能力强度”并不一样。真正把“项目级配置”做成一等公民的，主要是：

- Codex
- Claude Code
- Cursor
- Gemini CLI
- Kiro

其余工具要么偏“项目说明 + 规则文件”的组合，要么仍以用户级配置为主，项目级只覆盖其中一部分。

## 1. 一眼看懂：每个工具的项目级配置核心文件

| 工具 | 当前最核心的项目级文件/目录 | 状态判断 |
| --- | --- | --- |
| Codex | `AGENTS.md`、`.codex/config.toml`、`.codex/rules/*.rules`、`.codex/hooks.json`、`.codex/agents/*.toml`、`.agents/skills/**/SKILL.md` | 项目级能力最完整 |
| Claude Code | `CLAUDE.md`、`.claude/settings.json`、`.claude/settings.local.json`、`.mcp.json`、`.claude/agents/*.md`、`.claude/skills/**/SKILL.md` | 项目级能力最完整 |
| Cursor | `AGENTS.md`、`.cursor/rules/*.mdc`、`.cursor/cli.json`、`.cursorrules` | 规则体系强，CLI 有单独项目配置 |
| Gemini CLI | `GEMINI.md`、`.gemini/settings.json`、`.gemini/.env`、`.gemini/sandbox-*` | CLI 侧项目配置非常完整 |
| GitHub Copilot | `.github/copilot-instructions.md`、`.github/instructions/*.instructions.md`、`.github/prompts/*.prompt.md`、`AGENTS.md`/`CLAUDE.md`/`GEMINI.md` | 仓库级提示体系清晰，能力分散 |
| Windsurf | `.windsurf/rules/*.md`、`AGENTS.md` | 以规则文件为主 |
| Kiro | `.kiro/steering/**`、`.kiro/settings/mcp.json`、`.kiro/prompts/**`、`.kiro/agents/**`、`AGENTS.md` | 项目级体系完整，但入口分散 |
| Aider | `.aider.conf.yml`、`.aider.model.settings.yml`、`.aider.model.metadata.json`、`.aiderignore` | 以 YAML 主配置为核心 |
| Cline | `.clinerules/**`、`.clineignore` | 项目规则明确，其他能力较分散 |
| Roo Code | `AGENTS.md`、`.roomodes`、`.roorules`、`.roo/mcp.json` | 项目入口存在，但官方公开资料分散 |
| Continue | `.continuerc.json`、`config.json` | 这两类 workspace 配置已进入 deprecated 轨道，当前更偏用户级配置 |

## 2. 最重要的判断标准

如果你只想知道“某个工具的项目级配置到底强不强”，最关键看 4 个点：

- 有没有**仓库内主配置文件**，而不是只能改个人设置
- 有没有**项目共享 instruction/memory 文件**
- 有没有**项目共享 MCP / tools / hooks / subagents / skills**
- 有没有**deprecated 与 current 入口的清晰替代路径**

按这 4 个维度来看：

- **Codex / Claude Code / Gemini CLI / Kiro**：偏“体系化配置平台”
- **Cursor / Windsurf / Cline / GitHub Copilot**：偏“规则与说明驱动”
- **Aider**：偏“YAML + 命令行行为配置”
- **Continue**：当前更偏“用户级配置中心”，项目级入口相对弱

## 3. Codex

详细版已经单独整理在 [codex-project-config-guide.md](./codex-project-config-guide.md)，这里给总览版。

### 3.1 核心项目文件

- `AGENTS.md`
- `.codex/config.toml`
- `.codex/rules/*.rules`
- `.codex/hooks.json`
- `.codex/agents/*.toml`
- `.agents/skills/**/SKILL.md`
- `requirements.toml`：更偏企业治理和受控环境

### 3.2 每类文件负责什么

- `AGENTS.md`：仓库级工作说明、架构、命令、风格、禁区
- `.codex/config.toml`：模型、审批、沙箱、MCP、provider、features、skills 搜索路径
- `.codex/rules/*.rules`：命令前缀级 allow / prompt / forbidden 决策
- `.codex/hooks.json`：生命周期钩子编排
- `.codex/agents/*.toml`：项目级子代理定义
- `.agents/skills/**/SKILL.md`：项目级可复用技能
- `requirements.toml`：企业侧限制允许的 approval / sandbox / rules / features

### 3.3 什么时候最适合选 Codex

如果你需要的是：

- 严格的仓库级权限边界
- 细粒度命令规则
- 项目内技能、子代理、MCP 一起版本化
- 团队对“可控性”和“自动化”要求高

那 Codex 的项目级配置深度目前是第一梯队。

### 3.4 最小落地方案

```text
repo/
├─ AGENTS.md
└─ .codex/
   ├─ config.toml
   └─ rules/
      └─ default.rules
```

## 4. Claude Code

详细版已经单独整理在 [claude-project-config-guide.md](./claude-project-config-guide.md)，这里保留总览。

### 4.1 核心项目文件

- `CLAUDE.md`
- `.claude/settings.json`
- `.claude/settings.local.json`
- `.mcp.json`
- `.claude/agents/*.md`
- `.claude/skills/**/SKILL.md`
- `.claude/output-styles/*.md`
- `.claude/commands/*.md`：兼容旧机制
- `CLAUDE.local.md`：已 deprecated

### 4.2 每类文件负责什么

- `CLAUDE.md`：项目共享记忆与工作说明
- `.claude/settings.json`：项目共享 settings 主入口
- `.claude/settings.local.json`：个人机器在当前仓库的本地覆盖
- `.mcp.json`：项目共享 MCP server
- `.claude/agents/*.md`：subagent 定义
- `.claude/skills/**/SKILL.md`：项目技能与工作流
- `.claude/output-styles/*.md`：输出风格定制

### 4.3 关键判断

Claude Code 的项目级配置体系很成熟，但要特别注意两件事：

- 当前推荐主线已经从 `commands` 转向 `skills`
- `CLAUDE.local.md` 仍兼容，但官方明确不建议再当主方案

### 4.4 最小落地方案

```text
repo/
├─ CLAUDE.md
└─ .claude/
   └─ settings.json
```

## 5. Cursor

Cursor 当前的项目级配置，要分成两套理解：

1. 编辑器/Agent 的项目规则体系
2. Cursor CLI 的项目配置体系

### 5.1 核心项目文件

- `AGENTS.md`
- `.cursor/rules/*.mdc`
- `.cursorrules`：旧单文件机制，迁移时会遇到
- `.cursor/cli.json`：Cursor CLI 项目配置

### 5.2 每类文件负责什么

- `AGENTS.md`：Cursor 官方已支持把它当作项目级 instruction 文件
- `.cursor/rules/*.mdc`：当前主流 Project Rules 目录
- `.cursorrules`：历史单文件规则，老项目常见，但不是推荐新写法
- `.cursor/cli.json`：Cursor CLI 的项目级配置，官方文档明确提到这里可以放配置与权限

### 5.3 `.cursor/rules/*.mdc` 的定位

Project Rules 是 Cursor 当前最核心的仓库内配置能力。它解决的是：

- 项目说明
- 编码规范
- 特定目录或语言的规则注入
- agent 在某些上下文中的自动附加规则

它比旧的 `.cursorrules` 更适合多人协作和模块拆分。

### 5.4 `.cursor/cli.json` 的定位

如果你们团队同时用 Cursor CLI，那么项目根下的 `.cursor/cli.json` 很关键。官方说明里明确把它作为项目级配置入口，尤其用于：

- CLI 权限
- 项目级 CLI 行为控制
- 与全局 `~/.cursor/cli.json` 的分层覆盖

### 5.5 最小落地方案

```text
repo/
├─ AGENTS.md
└─ .cursor/
   └─ rules/
      └─ project.mdc
```

如果团队还在用 Cursor CLI，再补：

```text
repo/
└─ .cursor/
   └─ cli.json
```

## 6. Gemini CLI

Gemini CLI 是当前少数把“项目 settings + 项目 memory + 项目 MCP + 项目 sandbox 资料”都公开成正式机制的工具之一。

### 6.1 核心项目文件

- `GEMINI.md`
- `.gemini/settings.json`
- `.gemini/.env`
- `.gemini/sandbox-macos-*.sb`
- `.gemini/sandbox.Dockerfile`

另外，`contextFileName` 可以把项目说明文件名改成：

- `AGENTS.md`
- 你自定义的其他文件名

### 6.2 每类文件负责什么

- `GEMINI.md`：项目 instruction / memory 文件，支持层级扫描与 `@path` 导入
- `.gemini/settings.json`：项目级 settings 主入口
- `.gemini/.env`：项目级环境变量补充
- `.gemini/sandbox-*`：项目自定义沙箱配置资料

### 6.3 `.gemini/settings.json` 能管什么

官方当前文档里能确认的项目级能力包括：

- `general.*`：默认审批模式、编辑器、会话保留
- `ui.*`：主题、banner、tips、footer
- `tools.*`：工具启用、sandbox、tool discovery / call
- `mcpServers`：项目 MCP
- `telemetry.*`
- `privacy.*`
- `model.*`
- `context.*`：上下文文件名、includeDirectories、memory 加载策略
- `security.*`
- `policyPaths`

这意味着 Gemini CLI 的项目配置并不只是“写个 `GEMINI.md`”，而是真有完整的 `.gemini/settings.json` 配置面。

### 6.4 一个容易忽略的点

Gemini CLI 当前文档明确说明：

- 默认项目 context 文件名是 `GEMINI.md`
- 但可以通过 `contextFileName` 改成 `AGENTS.md`
- context 文件会按层级从项目根、父目录、子目录一起扫描

这让 Gemini CLI 很适合和其他工具共享一份“中性 instruction 文件”。

### 6.5 最小落地方案

```text
repo/
├─ GEMINI.md
└─ .gemini/
   └─ settings.json
```

## 7. GitHub Copilot

GitHub Copilot 的项目级配置没有 Codex / Claude 那么集中，而是分散在 `.github/` 下的几类文件中。

### 7.1 核心项目文件

- `.github/copilot-instructions.md`
- `.github/instructions/*.instructions.md`
- `.github/prompts/*.prompt.md`
- `AGENTS.md`
- `CLAUDE.md`
- `GEMINI.md`

### 7.2 每类文件负责什么

- `.github/copilot-instructions.md`：整个仓库默认指令
- `.github/instructions/*.instructions.md`：更细粒度、可复用或路径相关的指令文件
- `.github/prompts/*.prompt.md`：可复用 prompt 文件，目前属于较新的机制
- `AGENTS.md` / `CLAUDE.md` / `GEMINI.md`：GitHub.com 上的 Copilot coding agent 当前也会读取这几类通用 agent 指令文件

### 7.3 关键判断

Copilot 的强项不在“单一总配置文件”，而在：

- 仓库内 instruction 文件可以细粒度拆分
- GitHub.com coding agent 逐渐向通用 agent 文件靠拢
- 与 `.github/` 下的工作流、PR 模板、issue 模板天然共存

### 7.4 最小落地方案

```text
repo/
└─ .github/
   └─ copilot-instructions.md
```

如果你希望跨工具复用，再额外放一个：

```text
repo/
└─ AGENTS.md
```

## 8. Windsurf

Windsurf 当前的项目级配置，核心是 **Project Rules**。

### 8.1 核心项目文件

- `.windsurf/rules/*.md`
- `AGENTS.md`

### 8.2 每类文件负责什么

- `.windsurf/rules/*.md`：当前推荐的项目规则目录
- `AGENTS.md`：官方文档已明确提到，如果仓库里存在 `AGENTS.md`，Windsurf 会把它纳入规则上下文

### 8.3 `.windsurf/rules/*.md` 的定位

官方当前文档显示，Windsurf Project Rules 具备这些特点：

- 用 frontmatter 标记行为
- 常见写法会带 `alwaysApply: true`
- 会从当前目录、父目录直到 git 根，以及相关子目录进行发现

这意味着 Windsurf 的项目规则比老式“单个根文件”更灵活，更适合 monorepo 和大仓库。

### 8.4 最小落地方案

```text
repo/
├─ AGENTS.md
└─ .windsurf/
   └─ rules/
      └─ project.md
```

## 9. Kiro

Kiro 的项目级配置目前是一个“多入口目录体系”，没有把所有能力都塞进一个总配置文件。

### 9.1 核心项目文件/目录

- `.kiro/steering/**`
- `.kiro/settings/mcp.json`
- `.kiro/prompts/**`
- `.kiro/agents/**`
- `AGENTS.md`

### 9.2 每类文件负责什么

- `.kiro/steering/**`：项目 steering，负责长期项目约束和方向性说明
- `.kiro/settings/mcp.json`：项目级 MCP 共享配置
- `.kiro/prompts/**`：项目 prompt 模板
- `.kiro/agents/**`：项目自定义 agent
- `AGENTS.md`：官方文档已说明 chat agent 会自动纳入该文件内容

### 9.3 关键判断

Kiro 的项目级能力其实很强，尤其是：

- steering
- project MCP
- project prompts
- custom agents

但它的入口比 Codex / Claude 更分散，所以团队落地时更需要先设计目录约定。

### 9.4 最小落地方案

```text
repo/
├─ AGENTS.md
└─ .kiro/
   ├─ steering/
   │  └─ project.md
   └─ settings/
      └─ mcp.json
```

## 10. Aider

Aider 的项目级配置以 YAML 为主，整体更接近“命令行工具配置”而不是“agent platform”。

### 10.1 核心项目文件

- `.aider.conf.yml`
- `.aider.model.settings.yml`
- `.aider.model.metadata.json`
- `.aiderignore`
- `.env`

### 10.2 每类文件负责什么

- `.aider.conf.yml`：项目或仓库使用的主配置
- `.aider.model.settings.yml`：针对模型行为的高级设置
- `.aider.model.metadata.json`：模型元数据覆盖
- `.aiderignore`：项目中文件过滤
- `.env`：密钥与环境变量

### 10.3 关键判断

Aider 的优势是：

- 结构简单
- 容易与 shell / git 工作流结合
- YAML 主配置清晰

但它不像 Codex / Claude 那样把 subagent、skills、project hooks 都做成一整套仓库内配置体系。

### 10.4 最小落地方案

```text
repo/
├─ .aider.conf.yml
└─ .aiderignore
```

## 11. Cline

Cline 当前能稳定确认的项目级配置核心，是 rules 与 ignore。

### 11.1 核心项目文件

- `.clinerules/**`
- `.clineignore`

此外，官方 prompt engineering 资料也明确提到 Cline 能兼容或导入以下通用规则来源：

- `AGENTS.md`
- `.cursorrules`
- `.windsurfrules`

### 11.2 每类文件负责什么

- `.clinerules/**`：项目规则目录
- `.clineignore`：排除不希望进入上下文或工作流的内容

### 11.3 关键判断

Cline 官方首页已经把 customization 写成：

- rules
- skills
- workflows
- hooks
- `.clineignore`

但当前最稳、最适合入库版本化的项目级入口，仍然优先是 `.clinerules/**` 与 `.clineignore`。

### 11.4 最小落地方案

```text
repo/
├─ .clineignore
└─ .clinerules/
   └─ project.md
```

## 12. Roo Code

Roo Code 的项目级配置入口是存在的，但官方公开资料比前几类工具更分散。

### 12.1 当前可核验的项目级文件

- `AGENTS.md`
- `.roomodes`
- `.roorules`
- `.roo/mcp.json`

### 12.2 每类文件负责什么

- `AGENTS.md`：项目 instruction / shared memory
- `.roomodes`：项目自定义模式
- `.roorules`：项目规则
- `.roo/mcp.json`：项目级 MCP

### 12.3 关键判断

如果你团队主要用 Roo Code，建议把“项目约束、模式、MCP”拆开管理，而不是指望一个总配置文件全包。Roo Code 目前更像“多入口配置组合”，而不是“单主配置文件”。

### 12.4 最小落地方案

```text
repo/
├─ AGENTS.md
├─ .roomodes
└─ .roo/
   └─ mcp.json
```

## 13. Continue

Continue 要单独说明，因为它的项目级配置路线这两年变化较大。

### 13.1 当前结论

如果你的目标是“像 Codex / Claude 那样，把完整 AI 行为配置直接跟仓库一起提交”，Continue 目前并不是最强的那一类。

### 13.2 能确认到的项目级/历史入口

- `.continuerc.json`
- `config.json`

但官方文档已明确把它们放进 **deprecated** 轨道，当前主线更偏：

- 用户级 `config.yaml`
- Hub / assistants / rules 的中心化管理

### 13.3 应该怎么理解

所以在 Continue 里：

- 历史上确实有 workspace 级配置文件
- 但“当前推荐主线”不是继续往项目根堆更多 JSON
- 如果你需要强项目级版本化，Continue 需要更多依赖团队约定，而不是单靠官方当前主配置文件体系

## 14. 如果你只想做“一套能跨工具复用”的仓库结构

我更推荐下面这套中性结构：

```text
repo/
├─ AGENTS.md
├─ .github/
│  └─ copilot-instructions.md
├─ .cursor/
│  └─ rules/
│     └─ project.mdc
├─ .windsurf/
│  └─ rules/
│     └─ project.md
├─ .codex/
│  ├─ config.toml
│  └─ rules/
│     └─ default.rules
├─ .claude/
│  └─ settings.json
├─ .gemini/
│  └─ settings.json
└─ .kiro/
   └─ steering/
      └─ project.md
```

### 14.1 为什么这套结构实用

- `AGENTS.md` 正在成为越来越多工具都能认的“中性项目说明文件”
- 各工具再用各自的专属目录承载强能力
- 你可以把“通用原则”写在 `AGENTS.md`
- 把“工具专属能力”分别放进 `.codex`、`.claude`、`.cursor`、`.gemini`、`.windsurf`、`.kiro`

## 15. 最值得优先掌握的配置文件

如果你没有时间全都学，优先级我建议是：

1. `AGENTS.md`
2. `.codex/config.toml`
3. `.claude/settings.json`
4. `.cursor/rules/*.mdc`
5. `.gemini/settings.json`
6. `.github/copilot-instructions.md`
7. `.windsurf/rules/*.md`
8. `.kiro/steering/**`
9. `.aider.conf.yml`

原因很简单：

- 这些文件代表了当前最主流工具的“真正在用的项目级入口”
- 它们覆盖 instruction、权限、规则、MCP、工具、模型和目录级约束
- 其他文件大多是在这些主入口之上的细化扩展

## 16. 一句话总结每个工具

- Codex：最强的是 `.codex/config.toml` + `.codex/rules/*.rules` + `AGENTS.md`
- Claude Code：最强的是 `CLAUDE.md` + `.claude/settings.json` + `.mcp.json`
- Cursor：最强的是 `.cursor/rules/*.mdc`，CLI 再补 `.cursor/cli.json`
- Gemini CLI：最强的是 `GEMINI.md` + `.gemini/settings.json`
- GitHub Copilot：最强的是 `.github/copilot-instructions.md` + `.github/instructions/*.instructions.md`
- Windsurf：最强的是 `.windsurf/rules/*.md` + `AGENTS.md`
- Kiro：最强的是 `.kiro/steering/**` + `.kiro/settings/mcp.json`
- Aider：最强的是 `.aider.conf.yml`
- Cline：最强的是 `.clinerules/**` + `.clineignore`
- Roo Code：最强的是 `.roomodes` + `.roorules` + `.roo/mcp.json`
- Continue：历史上有 `.continuerc.json` / `config.json`，但当前主线已不再主推项目根配置文件

## 17. 官方参考

### Codex

- https://developers.openai.com/codex/config-basic
- https://developers.openai.com/codex/config-advanced
- https://developers.openai.com/codex/config-reference
- https://developers.openai.com/codex/rules
- https://developers.openai.com/codex/hooks
- https://developers.openai.com/codex/subagents
- https://developers.openai.com/codex/skills

### Claude Code

- https://code.claude.com/docs/en/settings
- https://code.claude.com/docs/en/memory
- https://code.claude.com/docs/en/sub-agents
- https://code.claude.com/docs/en/skills
- https://code.claude.com/docs/en/mcp
- https://code.claude.com/docs/en/output-styles

### Cursor

- https://docs.cursor.com/context/rules
- https://docs.cursor.com/context/agents
- https://docs.cursor.com/en/cli/reference/configuration

### Gemini CLI

- https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/configuration.md
- https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/configuration.md

### GitHub Copilot

- https://docs.github.com/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot
- https://docs.github.com/copilot/customizing-copilot/adding-instructions-files-to-your-repository
- https://docs.github.com/copilot/customizing-copilot/adding-reusable-prompt-files-for-github-copilot
- https://docs.github.com/en/copilot/how-tos/agents/copilot-coding-agent/customizing-the-development-environment-for-copilot-coding-agent

### Windsurf

- https://docs.windsurf.com/windsurf/cascade/memories
- https://docs.windsurf.com/context-awareness/rules

### Kiro

- https://kiro.dev/docs/steering/
- https://kiro.dev/docs/mcp/
- https://kiro.dev/docs/cli/configuration/
- https://kiro.dev/docs/agent-hooks/
- https://kiro.dev/docs/agents/

### Aider

- https://aider.chat/docs/config/aider_conf.html
- https://aider.chat/docs/config/options.html
- https://aider.chat/docs/config/dotenv.html
- https://aider.chat/docs/config/adv-model-settings.html

### Cline

- https://docs.cline.bot/features/prompt-engineering/cline-rules
- https://docs.cline.bot/features/prompt-engineering/clinerules-bank
- https://docs.cline.bot/

### Roo Code

- https://docs.roocode.com/features/prompting/agents-md
- https://github.com/RooCodeInc/Roo-Code-Docs

### Continue

- https://docs.continue.dev/customize/deep-dives/configuration
