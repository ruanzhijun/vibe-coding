# Continue 项目级配置清单

Continue 这一份需要单独纠正。

上一版把它写成“现在几乎只有用户级 `config.yaml`”，这个判断已经不够新。

更准确的说法是：

- Continue 当前主配置仍然是用户级 `~/.continue/config.yaml`
- 但当前官方已经明确支持多类仓库级项目文件
- 历史上的 `.continuerc.json` / `config.json` 确实 deprecated，但这不等于 Continue 没有项目级配置

## 1. 当前结论先说

如果你要整理 Continue 当前的项目级配置文件，真正应该列出来的是：

- `.continue/rules/*.md`
- `.continue/mcpServers/*.yaml`
- `.continue/checks/*.md`
- `.agents/checks/*.md`

而这几类需要单独标注为历史入口：

- `.continuerc.json`
- `config.json`

用户级主配置仍然是：

- `~/.continue/config.yaml`

## 2. Continue 当前的“项目级”和“用户级”要分开看

### 2.1 用户级主配置

当前官方主线依然是：

```text
~/.continue/
└─ config.yaml
```

它负责：

- models
- context
- rules
- prompts
- docs
- mcpServers
- data

也就是说 Continue 的“总配置中心”还是用户级 YAML。

### 2.2 仓库级项目文件

但在项目根，官方现在已经明确支持这些本地目录：

```text
repo/
├─ .continue/
│  ├─ rules/
│  │  ├─ backend.md
│  │  └─ security.md
│  ├─ mcpServers/
│  │  └─ playwright-mcp.yaml
│  └─ checks/
│     └─ migration-safety.md
└─ .agents/
   └─ checks/
      └─ security-review.md
```

所以现在更准确的理解应该是：

- Continue 的总开关在用户级 `config.yaml`
- Continue 的项目规则 / 项目 MCP / 项目 checks 已经可以入库

## 3. `.continue/rules/*.md`

这是 Continue 当前正式支持的本地项目规则目录。

官方当前文档明确说明：

- 本地规则放在 `.continue/rules`
- 会自动在使用 Hub config 时出现
- 直接存在于文件系统里
- 进入版本控制

### 3.1 它负责什么

适合放：

- 编码规范
- 安全规范
- 数据库变更约束
- 特定框架约定
- PR review 规则

### 3.2 规则格式

Continue 当前文档明确说明：

- 规则现在推荐 Markdown
- 也兼容旧 YAML 规则

当前文档明确列出的常见属性包括：

- `name`
- `globs`
- `regex`

这意味着 `.continue/rules/*.md` 不是纯文档，而是真正按上下文自动附加的本地规则系统。

### 3.3 规则加载顺序

Continue 当前 rules 深潜文档还明确说明了顺序：

1. Hub assistant rules
2. `config.yaml` 里 `uses:` 引入的 Hub rules
3. `.continue/rules/` 本地规则
4. `~/.continue/rules/` 全局规则

这一点说明本地项目规则已经是正式主线，而不是补丁机制。

## 4. `.continue/mcpServers/*.yaml`

这是 Continue 当前正式支持的本地项目 MCP 目录。

官方当前文档明确给出的 quick start 是：

1. 在项目根创建 `.continue/mcpServers`
2. 放入一个 `*.yaml`
3. 在其中定义 `mcpServers`

### 4.1 它负责什么

适合放：

- Playwright MCP
- Continue docs MCP
- 数据库 MCP
- 浏览器 MCP
- 其他项目专用工具 MCP

### 4.2 文件结构

官方当前文档能确认的本地 MCP YAML 外层字段包括：

- `name`
- `version`
- `schema`
- `mcpServers`

而 `mcpServers` 里的常见字段包括：

- `name`
- `command`
- `args`
- `env`
- `cwd`
- `requestOptions`
- `connectionTimeout`

### 4.3 适用边界

官方明确说明：

- MCP 只能在 agent mode 中使用

所以 `.continue/mcpServers/*.yaml` 是项目级工具扩展入口，但它的真正生效前提取决于 agent mode。

## 5. `.continue/checks/*.md`

这是 Continue 当前最正式、最核心的 repo 内 AI check 文件目录之一。

官方首页现在甚至直接把 Continue 定义成：

- Each check is a markdown file in your repo

### 5.1 它负责什么

适合放：

- Migration safety
- Security review
- API compatibility
- Test policy
- Release checklist

### 5.2 文件格式

官方当前 `Check File Reference` 明确说明：

- check 文件是 Markdown
- 顶部带 YAML frontmatter
- 正文是系统 prompt

必需/常见字段包括：

- `name`
- `description`
- `model`

### 5.3 扫描规则

官方当前文档还明确说明：

- 只扫描仓库根的 `.continue/checks/`
- 也扫描仓库根的 `.agents/checks/`
- 不扫描子目录
- 同名时 `.continue/checks/` 优先

## 6. `.agents/checks/*.md`

这是 Continue 当前官方明确承认的另一套等效项目目录。

官方当前文档直接说明：

- `.continue/checks/` 和 `.agents/checks/` 等价
- `.agents/` 是 emerging cross-tool standard

### 6.1 为什么它重要

如果你的目标是跨工具共享检查体系，这个目录很值得优先考虑。

因为它让 Continue 的 checks 可以跟其他 agent 工具的 `.agents/` 生态接轨。

## 7. `config.yaml`

虽然它主要是用户级文件，但它仍然是理解 Continue 项目配置时不能跳过的核心。

### 7.1 为什么它仍然重要

因为当前 Continue 的主配置能力仍集中在：

- `~/.continue/config.yaml`

项目级目录解决的是：

- 本地 rules
- 本地 MCP
- 本地 checks

而不是把整个 Continue 配置体系都搬进 repo 根。

### 7.2 应该怎么理解它和项目文件的关系

更准确的做法是：

- `config.yaml` 管模型、全局 rules/prompts/docs/MCP
- `.continue/rules/` 管项目本地规则
- `.continue/mcpServers/` 管项目本地 MCP
- `.continue/checks/` / `.agents/checks/` 管项目本地检查

## 8. `config.json` 与 `.continuerc.json`

这两类文件仍然要讲清楚，因为老项目会遇到。

### 8.1 `config.json`

官方当前文档已明确：

- `config.json` deprecated
- 应迁移到 `config.yaml`

### 8.2 `.continuerc.json`

官方当前文档仍明确记载：

- `.continuerc.json` 是历史上的 workspace-level configuration
- 但它属于 deprecated configuration methods

所以结论应该是：

- 要理解它
- 不要再把它当新项目主线

## 9. 最推荐的目录方案

### 9.1 最小必备版

```text
repo/
└─ .continue/
   └─ rules/
      └─ project.md
```

### 9.2 标准实用版

```text
repo/
├─ .continue/
│  ├─ rules/
│  │  ├─ backend.md
│  │  └─ security.md
│  ├─ mcpServers/
│  │  └─ playwright-mcp.yaml
│  └─ checks/
│     └─ migration-safety.md
└─ .agents/
   └─ checks/
      └─ security-review.md
```

### 9.3 历史兼容版

```text
repo/
├─ .continue/
│  ├─ rules/
│  ├─ mcpServers/
│  └─ checks/
├─ .agents/
│  └─ checks/
├─ .continuerc.json
└─ config.json
```

但后两者只应视为迁移对象，不应再作为长期主方案。

## 10. 一句话理解当前状态

- `~/.continue/config.yaml`：当前主配置主线，但主要是用户级
- `.continue/rules/*.md`：当前正式支持的项目本地规则
- `.continue/mcpServers/*.yaml`：当前正式支持的项目本地 MCP
- `.continue/checks/*.md`：当前正式支持的项目本地检查
- `.agents/checks/*.md`：Continue 官方认可的跨工具等效 checks 目录
- `config.json`：旧配置格式，已 deprecated
- `.continuerc.json`：历史上的 workspace 配置，已 deprecated

## 11. 对新项目的建议

如果你现在开始做 Continue 的项目级配置，我建议顺序是：

1. 先在用户目录维护好 `config.yaml`
2. 项目里先补 `.continue/rules/`
3. 再补 `.continue/mcpServers/`
4. 再补 `.continue/checks/`
5. 如果要做跨工具复用，再补 `.agents/checks/`
6. 不要再新建 `.continuerc.json`

## 12. 官方参考

- [How to Configure Continue](https://docs.continue.dev/customize/deep-dives/configuration)
- [config.yaml Reference](https://docs.continue.dev/reference/config)
- [Rules](https://docs.continue.dev/customize/rules)
- [Rules Deep Dive](https://docs.continue.dev/customize/deep-dives/rules)
- [MCP Deep Dive](https://docs.continue.dev/customize/deep-dives/mcp)
- [Continue MCP Reference Example](https://docs.continue.dev/reference/continue-mcp)
- [Check File Reference](https://docs.continue.dev/checks/reference)
- [Run Checks Locally](https://docs.continue.dev/checks/running-locally)
- [config.json Reference (Deprecated)](https://docs.continue.dev/reference/json-reference)
- [Migrating Config to YAML](https://docs.continue.dev/customize/yaml-migration)
