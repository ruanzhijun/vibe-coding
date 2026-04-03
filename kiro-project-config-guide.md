# Kiro 项目级配置清单

Kiro 的项目级配置不是一个总配置文件，而是一个多入口目录体系。

如果你把 Kiro 当成项目级 vibe coding 工具，当前最值得优先掌握的是：

- `.kiro/steering/*.md`
- `AGENTS.md`
- `.kiro/settings/mcp.json`
- `.kiro/skills/*/SKILL.md`
- `.kiro/prompts/*.md`

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
   ├─ skills/
   │  └─ pr-review/
   │     └─ SKILL.md
   └─ prompts/
      └─ release-check.md
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
- `inclusion: auto`

这意味着 steering 不只是项目文档，而是真正的项目级规则系统。

### 1.5 文件引用

Kiro 当前官方文档还明确支持在 steering 文件里引用工作区文件：

```md
#[[file:api/openapi.yaml]]
```

这让 steering 可以直接绑定 repo 里的活文档，而不需要复制内容。

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

### 4.2 最关键的字段

当前官方文档能明确确认的关键元数据是：

- `name`
- `description`

其中 `description` 直接影响 Kiro 何时自动激活这个 skill。

## 5. `.kiro/prompts/*.md`

这是 Kiro CLI 当前正式支持的本地项目 prompts 目录。

这一点是上一版遗漏最明显的地方之一。

官方当前文档明确说明：

- 本地 workspace prompts 存在 `.kiro/prompts/`
- 全局 prompts 存在 `~/.kiro/prompts/`
- 本地优先级高于全局和 MCP prompts

### 5.1 它负责什么

适合放：

- 高频复用 prompt
- 发布前检查 prompt
- 项目 onboarding prompt
- 文档生成 prompt
- review prompt

### 5.2 它怎么使用

当前官方文档说明：

- 通过 `/prompts` 管理
- 通过 `@prompt-name` 在聊天中调用

所以 `.kiro/prompts/*.md` 本质上是项目级可复用 prompt 资产。

## 6. 需要特别说明的几类能力

### 6.1 Custom agents

Kiro CLI 支持 custom agents，并且 agent 配置本身是 JSON 文件。

但官方当前默认位置是：

- `~/.kiro/agents/`

也就是说：

- 它默认是用户级
- 不是当前最标准的项目级主入口

### 6.2 Hooks

Kiro IDE 有 hooks，CLI 也有 hooks。

但当前要注意：

- IDE hooks 是 Kiro 的核心能力之一
- CLI hooks 是定义在 agent configuration 里的
- 官方当前并没有提供一个像 `.kiro/hooks.json` 这样的标准项目主文件

因此从“项目级配置文件”角度，Kiro 目前最稳的主线仍然是：

1. steering
2. `AGENTS.md`
3. MCP
4. skills
5. prompts

### 6.3 Powers

Kiro 现在已经有 Powers 体系，但 powers 的安装、分发和启用主要通过 IDE / marketplace / GitHub URL 进行，并不是一个约定俗成的项目根固定配置文件机制。

所以在“项目级配置文件清单”里，powers 应该理解为：

- 相关生态能力
- 不是当前最标准的 repo 内主配置文件

## 7. 最推荐的目录方案

### 7.1 最小必备版

```text
repo/
├─ AGENTS.md
└─ .kiro/
   └─ steering/
      ├─ product.md
      ├─ tech.md
      └─ structure.md
```

### 7.2 标准实用版

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
   ├─ skills/
   │  └─ pr-review/
   │     └─ SKILL.md
   └─ prompts/
      └─ release-check.md
```

### 7.3 进阶版

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
   ├─ skills/
   │  ├─ pr-review/
   │  │  └─ SKILL.md
   │  └─ deploy/
   │     └─ SKILL.md
   └─ prompts/
      ├─ release-check.md
      └─ docs-audit.md
```

## 8. 一句话理解每类文件

- `.kiro/steering/*.md`：Kiro 项目级长期规则与知识
- `AGENTS.md`：Kiro 支持的通用 steering 文件
- `.kiro/settings/mcp.json`：项目级 MCP 配置
- `.kiro/skills/*/SKILL.md`：项目级可复用工作流
- `.kiro/prompts/*.md`：项目级本地复用 prompt

## 9. 推荐的落地顺序

1. 先写 `.kiro/steering/product.md`
2. 再补 `tech.md` 和 `structure.md`
3. 再加根目录 `AGENTS.md`
4. 再补 `.kiro/settings/mcp.json`
5. 再沉淀 `.kiro/skills/*/SKILL.md`
6. 最后再补 `.kiro/prompts/*.md`

## 10. 官方参考

- [Kiro Steering (IDE)](https://kiro.dev/docs/steering/)
- [Kiro Steering (CLI)](https://kiro.dev/docs/cli/steering/)
- [Kiro MCP configuration (IDE)](https://kiro.dev/docs/mcp/configuration/)
- [Kiro MCP configuration (CLI)](https://kiro.dev/docs/cli/mcp/configuration/)
- [Kiro Agent Skills (CLI)](https://kiro.dev/docs/cli/skills/)
- [Kiro Manage Prompts (CLI)](https://kiro.dev/docs/cli/chat/manage-prompts/)
- [Kiro Custom Agents (CLI)](https://kiro.dev/docs/cli/custom-agents/)
- [Kiro Powers (IDE)](https://kiro.dev/docs/powers/)
