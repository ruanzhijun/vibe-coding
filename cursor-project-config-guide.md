# Cursor 项目级配置清单

本文整理 Cursor 当前与“项目级配置”最相关的几类文件，重点回答 3 个问题：

- Cursor 项目里应该放哪些配置文件
- 每个文件分别负责什么
- 哪些属于当前主线，哪些已经进入 legacy 轨道

先给结论：如果你把 Cursor 当成项目级 vibe coding 工具，当前最应该优先掌握的是这 4 类文件。

```text
repo/
├─ AGENTS.md
├─ .cursorrules                  # 旧单文件规则，老项目兼容
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

`.cursorrules` 仍然兼容，但已经是 legacy 入口。

## 1. Cursor 的项目级配置分成两套

Cursor 现在要分成两套理解：

1. 编辑器里 Agent / Inline Edit 使用的项目规则体系
2. Cursor CLI 使用的项目级 CLI 配置体系

这两套配置都能放进仓库，但职责不同：

- `.cursor/rules/*.mdc` 和 `AGENTS.md` 解决“AI 在项目里应该怎么做”
- `.cursor/cli.json` 解决“CLI 在这个项目里允许做什么”

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

### 2.2 为什么它比 `.cursorrules` 更好

相较旧的 `.cursorrules`：

- 更适合拆分
- 更适合多人协作
- 可见性更高
- 更容易做目录和主题分层

所以新项目应优先用 `.cursor/rules/*.mdc`，不要再以 `.cursorrules` 为主。

## 3. `AGENTS.md`

Cursor 官方当前把 `AGENTS.md` 视为 `.cursor/rules` 的简化替代。

它的定位是：

- 一个纯 Markdown 文件
- 放在项目根目录
- 不需要复杂元数据
- 适合“项目说明比较简单”的仓库

适合写的内容：

- 项目全局规则
- 默认技术栈偏好
- 架构原则
- 构建与测试命令
- 不要碰的目录

### 3.1 `AGENTS.md` 的边界

根据 Cursor 官方当前说明：

- `AGENTS.md` 现在是“简单替代方案”
- 当前限制是项目根级单文件、全局生效
- 如果你需要更细粒度、可拆分、可扩展的项目规则，还是应该用 `.cursor/rules/*.mdc`

另外，Cursor CLI 文档还明确提到：

- CLI 也会读取项目根的 `AGENTS.md`
- 同时也会读取项目根的 `CLAUDE.md`

这使得 `AGENTS.md` 很适合做跨工具共享的“中性项目说明文件”。

## 4. `.cursorrules`

`.cursorrules` 是旧单文件规则入口。

当前状态可以概括成一句话：

- 还支持，但已经是 legacy

因此推荐策略是：

- 老项目继续兼容理解
- 新项目不要再以它为主
- 有条件就迁移到 `.cursor/rules/*.mdc`

## 5. `.cursor/cli.json`

这是 Cursor CLI 的项目级配置文件。

官方文档明确给出：

- 全局文件是 `~/.cursor/cli-config.json`
- 项目级文件是 `<project>/.cursor/cli.json`

### 5.1 它能配置什么

官方当前说明里最关键的一点是：

- 项目级只能配置 permissions
- 其他 CLI 设置都必须走全局配置

### 5.2 典型字段

项目级最重要的字段包括：

- `version`
- `permissions.allow`
- `permissions.deny`

所以从团队落地角度看，`.cursor/cli.json` 最适合做：

- 允许哪些命令
- 禁止哪些命令
- 当前仓库的最小 CLI 权限边界

## 6. 最推荐的职责分工

### 6.1 简单项目

```text
repo/
├─ AGENTS.md
└─ .cursor/
   └─ cli.json
```

### 6.2 标准项目

```text
repo/
├─ AGENTS.md
└─ .cursor/
   ├─ rules/
   │  ├─ project.mdc
   │  ├─ frontend.mdc
   │  └─ backend.mdc
   └─ cli.json
```

### 6.3 旧项目兼容

```text
repo/
├─ AGENTS.md
├─ .cursorrules
└─ .cursor/
   ├─ rules/
   │  └─ project.mdc
   └─ cli.json
```

## 7. 一句话理解每类文件

- `AGENTS.md`：最简单的项目级 Agent 说明文件
- `.cursor/rules/*.mdc`：当前主线的项目规则体系
- `.cursorrules`：旧单文件规则，兼容但不建议新建
- `.cursor/cli.json`：Cursor CLI 的项目级权限配置

## 8. 推荐的落地顺序

1. 先写 `AGENTS.md`
2. 再拆成 `.cursor/rules/*.mdc`
3. 再补 `.cursor/cli.json`
4. 最后清理旧的 `.cursorrules`

## 9. 官方参考

- [Cursor Rules](https://docs.cursor.com/context/rules)
- [Cursor Context](https://docs.cursor.com/en/context)
- [Cursor CLI Configuration](https://docs.cursor.com/en/cli/reference/configuration)
- [Cursor CLI Using](https://docs.cursor.com/en/cli/using)
