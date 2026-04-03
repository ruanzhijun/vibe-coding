# Cursor 项目级配置清单

本文按 Cursor 当前官方公开文档，重新整理一份更完整的项目级配置清单，重点回答 3 个问题：

- Cursor 项目里到底应该放哪些文件
- 每个文件分别负责什么
- 哪些是当前主线，哪些已经属于 legacy

先给结论：如果你把 Cursor 当成项目级 vibe coding 工具，当前最应该优先掌握的是这 6 类文件。

```text
repo/
├─ AGENTS.md
├─ .cursorrules                  # 旧单文件规则，兼容但不推荐新建
├─ .cursorignore
├─ .cursorindexingignore
└─ .cursor/
   ├─ rules/
   │  ├─ project.mdc
   │  ├─ frontend.mdc
   │  └─ backend.mdc
   └─ cli.json
```

其中真正属于 Cursor 当前推荐主线的是：

- `AGENTS.md`
- `.cursor/rules/*.mdc`
- `.cursor/cli.json`
- `.cursorignore`
- `.cursorindexingignore`

`.cursorrules` 仍然兼容，但已经是 legacy。

## 1. Cursor 的项目级配置分成三层

当前最好把 Cursor 的项目级配置理解成三层：

1. 项目规则
2. 文件访问与索引边界
3. CLI 项目权限

分别对应：

- `.cursor/rules/*.mdc`、`AGENTS.md`、`.cursorrules`
- `.cursorignore`、`.cursorindexingignore`
- `.cursor/cli.json`

## 2. `.cursor/rules/*.mdc`

这是 Cursor 当前最核心的项目级规则入口。

官方把它定义为 Project Rules，特点是：

- 存在于 `.cursor/rules/`
- 会进入版本控制
- 作用域是当前代码库
- 用来给 Agent 和 Inline Edit 提供持久化规则

### 2.1 它负责什么

最适合放进 `.cursor/rules/*.mdc` 的内容：

- 项目结构说明
- 编码约定
- 测试约定
- 目录级规范
- 特定语言或框架规则
- 代码生成边界
- 提交前检查要求

### 2.2 Rule anatomy

官方当前文档明确说明，每个规则文件都是 `.mdc`，核心元数据围绕这 3 个字段：

- `description`
- `globs`
- `alwaysApply`

它们对应的是：

- `description`：给 Agent Requested 类型的规则做语义说明
- `globs`：路径匹配
- `alwaysApply`：是否始终附加

### 2.3 当前 4 种 rule type

Cursor 当前官方文档明确列出 4 种规则类型：

- `Always`
- `Auto Attached`
- `Agent Requested`
- `Manual`

对应理解如下：

- `Always`：总是进入模型上下文
- `Auto Attached`：命中 `globs` 时自动附加
- `Agent Requested`：AI 自己决定要不要拉进上下文，必须写 `description`
- `Manual`：只有显式 `@ruleName` 才启用

### 2.4 Nested rules

Cursor 当前文档还明确支持嵌套规则目录。

也就是说你不一定只能在项目根放一份：

```text
project/
├─ .cursor/rules/
├─ backend/
│  └─ .cursor/rules/
└─ frontend/
   └─ .cursor/rules/
```

这对 monorepo 或前后端混合仓库很重要。

## 3. `AGENTS.md`

Cursor 官方当前把 `AGENTS.md` 视为 `.cursor/rules` 的简化替代。

它的定位是：

- 一个纯 Markdown 文件
- 放在项目根目录
- 不需要复杂元数据
- 适合项目说明比较简单的仓库

适合写的内容：

- 项目全局规则
- 默认技术栈偏好
- 架构原则
- 构建与测试命令
- 不要碰的目录

### 3.1 `AGENTS.md` 的边界

根据 Cursor 官方当前说明：

- `AGENTS.md` 现在是简单替代方案
- 当前限制是项目根级单文件、全局生效
- 如果你需要更细粒度、可拆分、可扩展的项目规则，还是应该用 `.cursor/rules/*.mdc`

另外，Cursor CLI 文档还明确提到：

- CLI 也会读取项目根的 `AGENTS.md`
- 同时也会读取项目根的 `CLAUDE.md`

这使得 `AGENTS.md` 很适合做跨工具共享的中性项目说明文件。

## 4. `.cursorrules`

`.cursorrules` 是旧单文件规则入口。

当前状态可以概括成一句话：

- 还支持，但已经是 legacy

因此推荐策略是：

- 老项目继续兼容理解
- 新项目不要再以它为主
- 有条件就迁移到 `.cursor/rules/*.mdc`

## 5. `.cursorignore`

这是 Cursor 当前非常重要，但很多文档会漏掉的项目级文件。

官方当前文档明确说明：

- 把 `.cursorignore` 放在项目根
- 它控制 Cursor 能访问哪些文件

### 5.1 它会阻止什么

被 `.cursorignore` 命中的内容，会被阻止用于：

- codebase indexing
- Tab / Agent / Inline Edit 可访问代码
- `@` 文件引用

### 5.2 它不会阻止什么

官方文档也明确提醒：

- Agent 触发的 terminal 和 MCP tool calls，不受 `.cursorignore` 完全约束

因此不能把 `.cursorignore` 误认为绝对安全边界。

### 5.3 它适合放什么

最适合放：

- `.env`
- 密钥文件
- 凭据
- 大型无关目录
- 不希望被 AI 读取的敏感模板

### 5.4 语法和层级

官方明确说明它使用 `.gitignore` 语法，并且还支持：

- 注释
- `!` 反向匹配
- 可选启用 hierarchical ignore，从父目录继续找 `.cursorignore`

## 6. `.cursorindexingignore`

这是另一个容易漏掉的项目级文件。

官方当前文档明确说明：

- `.cursorindexingignore` 只影响 indexing
- 被它排除的文件仍然可以被其他 AI 功能访问

所以它和 `.cursorignore` 的边界非常不同。

### 6.1 它适合什么场景

最适合：

- 大体积日志
- 大量生成文件
- 不需要参与 codebase search 的资产
- 想减少 embeddings / indexing 体积，但不想完全屏蔽 AI 读取的内容

### 6.2 一个常见误区

如果你的目标是：

- 不让 Agent 读到敏感文件

那应该优先用：

- `.cursorignore`

而不是 `.cursorindexingignore`。

## 7. `.cursor/cli.json`

这是 Cursor CLI 的项目级配置文件。

官方文档明确给出：

- 全局文件是 `~/.cursor/cli-config.json`
- 项目级文件是 `<project>/.cursor/cli.json`

### 7.1 它能配置什么

官方当前说明里最关键的一点是：

- 项目级只能配置 `permissions`
- 其他 CLI 设置都必须走全局配置

### 7.2 典型字段

项目级最重要的字段包括：

- `version`
- `permissions.allow`
- `permissions.deny`

因此 `.cursor/cli.json` 最适合做：

- 允许哪些命令
- 禁止哪些命令
- 当前仓库的最小 CLI 权限边界

## 8. 最推荐的职责分工

### 8.1 最小必备版

```text
repo/
├─ AGENTS.md
├─ .cursorignore
└─ .cursor/
   ├─ rules/
   │  └─ project.mdc
   └─ cli.json
```

### 8.2 标准实用版

```text
repo/
├─ AGENTS.md
├─ .cursorignore
├─ .cursorindexingignore
└─ .cursor/
   ├─ rules/
   │  ├─ project.mdc
   │  ├─ frontend.mdc
   │  └─ backend.mdc
   └─ cli.json
```

### 8.3 旧项目兼容版

```text
repo/
├─ AGENTS.md
├─ .cursorrules
├─ .cursorignore
└─ .cursor/
   ├─ rules/
   │  └─ project.mdc
   └─ cli.json
```

## 9. 一句话理解每类文件

- `AGENTS.md`：最简单的项目级 Agent 说明文件
- `.cursor/rules/*.mdc`：当前主线的项目规则体系
- `.cursorrules`：旧单文件规则，兼容但不建议新建
- `.cursorignore`：阻止 AI 访问与索引的忽略文件
- `.cursorindexingignore`：只影响 indexing 的忽略文件
- `.cursor/cli.json`：Cursor CLI 的项目级权限配置

## 10. 推荐的落地顺序

1. 先写 `AGENTS.md`
2. 再拆成 `.cursor/rules/*.mdc`
3. 再补 `.cursorignore`
4. 再按需要加 `.cursorindexingignore`
5. 最后补 `.cursor/cli.json`

## 11. 官方参考

- [Cursor Rules](https://docs.cursor.com/en/context/rules)
- [Cursor Context / AGENTS.md](https://docs.cursor.com/en/context)
- [Cursor Ignore Files](https://docs.cursor.com/en/context/ignore-files)
- [Cursor Codebase Indexing](https://docs.cursor.com/en/context/codebase-indexing)
- [Cursor CLI Configuration](https://docs.cursor.com/en/cli/reference/configuration)
- [Cursor CLI Using](https://docs.cursor.com/en/cli/using)
