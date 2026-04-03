# Kiro 项目级配置清单

Kiro 的项目级配置不是“一个总配置文件包打天下”，而是一个多入口目录体系。

如果你把 Kiro 当成项目级 vibe coding 工具，当前最值得优先掌握的是：

- `.kiro/steering/*.md`
- `AGENTS.md`
- `.kiro/settings/mcp.json`
- `.kiro/skills/*/SKILL.md`

推荐结构如下：

```text
repo/
├─ AGENTS.md
└─ .kiro/
   ├─ steering/
   │  ├─ product.md
   │  ├─ tech.md
   │  ├─ structure.md
   │  └─ api-standards.md
   ├─ settings/
   │  └─ mcp.json
   └─ skills/
      └─ pr-review/
         └─ SKILL.md
```

## 1. `.kiro/steering/*.md`

这是 Kiro 当前最核心的项目级长期上下文文件。

官方把它叫做 Steering。

### 1.1 它负责什么

适合放：

- 产品背景
- 技术栈说明
- 项目结构
- API 规范
- 测试规范
- 安全要求
- 部署流程

### 1.2 放在哪里

Kiro 官方当前说明很明确：

- workspace steering 在 `.kiro/steering/`
- global steering 在 `~/.kiro/steering/`
- 冲突时 workspace 优先

### 1.3 Kiro 官方推荐的基础文件

IDE 文档里当前专门提到三类 foundational steering files：

- `product.md`
- `tech.md`
- `structure.md`

### 1.4 inclusion 模式

Kiro 的 steering 文件支持 frontmatter 控制加载方式。

官方当前文档能确认的模式包括：

- `inclusion: always`
- `inclusion: fileMatch`
- `inclusion: manual`

## 2. `AGENTS.md`

Kiro 官方当前明确支持通过 `AGENTS.md` 提供 steering directives。

### 2.1 它的定位

`AGENTS.md` 在 Kiro 里本质上是：

- 一种通用 steering 文件
- Markdown 格式
- 总是包含
- 不支持 steering file 的 inclusion modes

### 2.2 放在哪里

官方说明可放在：

- `~/.kiro/steering/`
- workspace root

对项目级来说，最关键的是：

- 仓库根目录的 `AGENTS.md`

## 3. `.kiro/settings/mcp.json`

这是 Kiro 的项目级 MCP 配置文件。

官方当前 MCP 文档明确说明：

- workspace 级：`.kiro/settings/mcp.json`
- user 级：`~/.kiro/settings/mcp.json`
- 二者合并，workspace 优先

### 3.1 它负责什么

适合放：

- 项目专用 MCP servers
- 数据库 MCP
- GitHub / 文档 / 浏览器 / 内部 API MCP
- 团队共享的 trusted tools

### 3.2 常见字段

官方当前文档能确认的字段包括：

- `mcpServers`
- `command`
- `args`
- `env`
- `disabled`
- `autoApprove`
- `disabledTools`

## 4. `.kiro/skills/*/SKILL.md`

这是 Kiro CLI 的项目级 skills 目录。

官方当前 CLI 文档明确说明：

- workspace skills 放在 `.kiro/skills/`
- global skills 放在 `~/.kiro/skills/`
- 工作区技能优先级高于全局

### 4.1 它负责什么

适合沉淀：

- PR review 流程
- 安全审查流程
- 发布流程
- 数据迁移流程
- 项目专属代码生成流程

## 5. 需要特别说明的几类能力

### 5.1 Custom agents

Kiro CLI 支持 custom agents，并且 agent 配置本身是 JSON 文件。

但官方当前默认位置是：

- `~/.kiro/agents/`

也就是说它默认是用户级，不是固定的标准项目目录。

### 5.2 Hooks

Kiro IDE 有 hooks，CLI 也有 hooks。

但当前要注意：

- IDE hooks 更偏 UI 管理
- CLI hooks 是写在 agent 配置里的
- 官方当前并没有提供一个像 `.kiro/hooks.json` 这样的统一项目文件作为主入口

## 6. 最推荐的目录方案

### 6.1 最小必备版

```text
repo/
├─ AGENTS.md
└─ .kiro/
   └─ steering/
      ├─ product.md
      ├─ tech.md
      └─ structure.md
```

### 6.2 标准实用版

```text
repo/
├─ AGENTS.md
└─ .kiro/
   ├─ steering/
   │  ├─ product.md
   │  ├─ tech.md
   │  ├─ structure.md
   │  └─ testing-standards.md
   ├─ settings/
   │  └─ mcp.json
   └─ skills/
      └─ pr-review/
         └─ SKILL.md
```

### 6.3 进阶版

```text
repo/
├─ AGENTS.md
└─ .kiro/
   ├─ steering/
   │  ├─ product.md
   │  ├─ tech.md
   │  ├─ structure.md
   │  ├─ api-standards.md
   │  ├─ security-policies.md
   │  └─ deployment-workflow.md
   ├─ settings/
   │  └─ mcp.json
   └─ skills/
      ├─ pr-review/
      │  └─ SKILL.md
      └─ deploy/
         └─ SKILL.md
```

## 7. 一句话理解每类文件

- `.kiro/steering/*.md`：Kiro 项目级长期规则与知识
- `AGENTS.md`：Kiro 支持的通用 steering 文件
- `.kiro/settings/mcp.json`：项目级 MCP 配置
- `.kiro/skills/*/SKILL.md`：项目级可复用工作流

## 8. 推荐的落地顺序

1. 先写 `.kiro/steering/product.md`
2. 再补 `tech.md` 和 `structure.md`
3. 再加根目录 `AGENTS.md`
4. 再补 `.kiro/settings/mcp.json`
5. 最后沉淀 `.kiro/skills/*/SKILL.md`

## 9. 官方参考

- [Kiro Steering (IDE)](https://kiro.dev/docs/steering/)
- [Kiro Steering (CLI)](https://kiro.dev/docs/cli/steering/)
- [Kiro MCP configuration (IDE)](https://kiro.dev/docs/mcp/configuration/)
- [Kiro MCP configuration (CLI)](https://kiro.dev/docs/cli/mcp/configuration/)
- [Kiro Agent Skills (CLI)](https://kiro.dev/docs/cli/skills/)
- [Kiro Custom Agents (CLI)](https://kiro.dev/docs/cli/custom-agents/)
