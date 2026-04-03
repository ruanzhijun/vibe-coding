# Cline 项目级配置清单

Cline 当前的项目级配置，已经不只是 `.clinerules/` 和 `.clineignore` 两件事。

按当前官方文档，真正应该纳入项目级配置清单的是：

- `.clinerules/`
- `.clineignore`
- `.cline/skills/`
- `.clinerules/workflows/`
- `.clinerules/hooks/`
- `AGENTS.md`
- `.cursorrules`
- `.windsurfrules`

推荐结构如下：

```text
repo/
├─ AGENTS.md
├─ .clineignore
├─ .cline/
│  └─ skills/
│     └─ deploy/
│        └─ SKILL.md
└─ .clinerules/
   ├─ coding.md
   ├─ testing.md
   ├─ architecture.md
   ├─ workflows/
   │  └─ release-prep.md
   └─ hooks/
      └─ PreToolUse.js
```

## 1. `.clinerules/`

这是 Cline 当前主线的 workspace 规则目录。

官方当前文档明确说明：

- workspace rules 放在 `.clinerules/`
- Cline 会处理其中的 `.md` 和 `.txt` 文件
- workspace rules 会进入版本控制

### 1.1 它负责什么

适合写：

- 编码规范
- 测试规范
- 架构约束
- 文档规范
- 不允许修改的模块
- 团队约定

### 1.2 它可以拆分

官方当前说明中，`.clinerules/` 支持多文件组合。

### 1.3 支持条件规则

Cline 当前文档明确支持 Conditional Rules。

规则文件顶部可以用 YAML frontmatter 指定：

- `paths`

这意味着 `.clinerules/` 已经不是纯静态说明目录，而是按上下文自动附加的规则系统。

## 2. `.clineignore`

这是 Cline 的项目忽略文件。

官方当前文档明确说明：

- 文件名就是 `.clineignore`
- 作用类似 `.gitignore`
- 它控制 Cline 自动扫描与上下文加载时要跳过哪些文件

### 2.1 它负责什么

适合忽略：

- `node_modules/`
- `dist/`
- `build/`
- `.next/`
- `coverage/`
- 大型数据文件
- `.env*`
- 二进制资源

### 2.2 一个关键点

官方当前文档说明得很清楚：

- `.clineignore` 控制的是自动加载
- 被忽略文件仍可被显式 `@` 引用读取

所以它更像“默认上下文边界”，而不是绝对访问隔离。

## 3. `.cline/skills/*/SKILL.md`

这是 Cline 当前项目级能力里最容易被漏掉的一项。

官方当前文档明确说明：

- skills 是模块化 instruction sets
- project skills 推荐放在 `.cline/skills/`

此外官方也明确给出兼容位置：

- `.clinerules/skills/`
- `.claude/skills/`

但当前推荐位置是：

- `.cline/skills/`

### 3.1 它负责什么

适合沉淀：

- 部署流程
- 发版流程
- 安全审查流程
- 数据迁移流程
- 项目 onboarding 流程

### 3.2 `SKILL.md` 的关键字段

官方当前文档能确认的元数据包括：

- `name`
- `description`

并明确要求：

- `name` 必须与目录名一致

### 3.3 当前状态

官方明确说明：

- Skills 目前仍是 experimental feature

但它已经是正式文档里的项目级目录，不应再漏掉。

## 4. `.clinerules/workflows/*.md`

这是 Cline 的项目级 workflows 目录。

官方当前文档明确说明：

- workspace workflows 放在 `.clinerules/workflows/`
- 文件是 Markdown
- 通过 `/filename.md` 形式调用

### 4.1 它负责什么

适合放：

- 发布前检查
- release prep
- 部署流程
- 项目初始化流程
- PR review checklist

### 4.2 它和 rules 的区别

官方当前文档的核心定位是：

- Rules：总是生效
- Workflows：按需调用

所以 workflows 更像：

- slash-command 风格的项目级工作流脚本

## 5. `.clinerules/hooks/`

这是 Cline 的项目级 hooks 目录。

官方当前文档明确说明：

- project hooks 存在 `.clinerules/hooks/`
- 可以进入版本控制

### 5.1 它负责什么

适合做：

- 阻止危险操作
- 在文件写入前跑检查
- 注入上下文
- 记录操作日志
- 调用外部校验脚本

### 5.2 hook type

官方当前文档明确说明：

- Cline 支持 8 种 hook type

并且：

- 同一个 hooks 目录里，每种 hook type 只能有一个

因此 `.clinerules/hooks/` 不是随便放脚本的目录，而是明确有生命周期约束的项目级入口。

## 6. `AGENTS.md`

Cline 当前规则文档明确说明：

- Cline 能识别 `AGENTS.md`
- 并把它当作标准的跨工具兼容规则源之一

因此如果你希望和其他工具共享项目说明，根目录放一个 `AGENTS.md` 很有价值。

## 7. `.cursorrules` 与 `.windsurfrules`

Cline 文档也明确列出：

- `.cursorrules`
- `.windsurfrules`

都属于它能自动识别的规则来源。

这意味着：

- 如果项目原来主要给 Cursor 或 Windsurf 写规则
- Cline 也能一定程度上复用这些文件

但如果项目是以 Cline 为主，主线仍然建议放在：

- `.clinerules/`
- `.clineignore`
- `.cline/skills/`
- `.clinerules/workflows/`
- `.clinerules/hooks/`

## 8. 最推荐的目录方案

### 8.1 最小必备版

```text
repo/
├─ .clineignore
└─ .clinerules/
   └─ coding.md
```

### 8.2 标准实用版

```text
repo/
├─ AGENTS.md
├─ .clineignore
├─ .cline/
│  └─ skills/
│     └─ deploy/
│        └─ SKILL.md
└─ .clinerules/
   ├─ coding.md
   ├─ testing.md
   ├─ architecture.md
   ├─ workflows/
   │  └─ release-prep.md
   └─ hooks/
      └─ PreToolUse.js
```

### 8.3 兼容迁移版

```text
repo/
├─ AGENTS.md
├─ .cursorrules
├─ .windsurfrules
├─ .clineignore
├─ .cline/
│  └─ skills/
│     └─ deploy/
│        └─ SKILL.md
└─ .clinerules/
   ├─ coding.md
   ├─ workflows/
   │  └─ release-prep.md
   └─ hooks/
      └─ PreToolUse.js
```

## 9. 一句话理解每类文件

- `.clinerules/`：Cline 当前主线的项目规则目录
- `.clineignore`：Cline 的自动上下文忽略文件
- `.cline/skills/*/SKILL.md`：项目级按需加载技能
- `.clinerules/workflows/*.md`：项目级 slash-command 工作流
- `.clinerules/hooks/`：项目级生命周期 hooks
- `AGENTS.md`：跨工具兼容规则源
- `.cursorrules`：Cursor 规则兼容源
- `.windsurfrules`：Windsurf 规则兼容源

## 10. 推荐的落地顺序

1. 先写 `.clineignore`
2. 再写 `.clinerules/coding.md`
3. 再拆出 testing / architecture 等主题规则
4. 再补 `.clinerules/workflows/`
5. 再补 `.clinerules/hooks/`
6. 最后加 `.cline/skills/` 和 `AGENTS.md`

## 11. 官方参考

- [Cline Rules](https://docs.cline.bot/features/cline-rules/overview)
- [Conditional Rules](https://docs.cline.bot/features/conditional-rules)
- [.clineignore](https://docs.cline.bot/prompting/prompt-engineering-guide/clineignore-file-guide)
- [Cline Skills](https://docs.cline.bot/customization/skills)
- [Cline Workflows](https://docs.cline.bot/features/slash-commands/workflows/quickstart)
- [Cline Hooks](https://docs.cline.bot/customization/hooks)
