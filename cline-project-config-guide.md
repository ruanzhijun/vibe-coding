# Cline 项目级配置清单

Cline 当前最稳的项目级配置主线是：

- `.clinerules/`
- `.clineignore`

此外，Cline 还会识别一些跨工具规则文件：

- `AGENTS.md`
- `.cursorrules`
- `.windsurfrules`

推荐结构如下：

```text
repo/
├─ AGENTS.md
├─ .clineignore
└─ .clinerules/
   ├─ coding.md
   ├─ testing.md
   ├─ architecture.md
   └─ docs.md
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

## 3. `AGENTS.md`

Cline 当前规则文档明确说明：

- Cline 能识别 `AGENTS.md`
- 并把它当作标准的跨工具兼容规则源之一

## 4. `.cursorrules` 与 `.windsurfrules`

Cline 文档也明确列出：

- `.cursorrules`
- `.windsurfrules`

都属于它能自动识别的规则来源。

## 5. 目前不建议误判的点

Cline 官方首页现在也提到了：

- rules
- skills
- workflows
- hooks

但从“当前最稳定、最适合项目入库”的角度看，最应该优先掌握的项目文件仍然是：

- `.clinerules/`
- `.clineignore`

## 6. 最推荐的目录方案

### 6.1 最小必备版

```text
repo/
├─ .clineignore
└─ .clinerules/
   └─ coding.md
```

### 6.2 标准实用版

```text
repo/
├─ AGENTS.md
├─ .clineignore
└─ .clinerules/
   ├─ coding.md
   ├─ testing.md
   ├─ architecture.md
   └─ docs.md
```

### 6.3 兼容迁移版

```text
repo/
├─ AGENTS.md
├─ .cursorrules
├─ .windsurfrules
├─ .clineignore
└─ .clinerules/
   ├─ coding.md
   └─ architecture.md
```

## 7. 一句话理解每类文件

- `.clinerules/`：Cline 当前主线的项目规则目录
- `.clineignore`：Cline 的自动上下文忽略文件
- `AGENTS.md`：跨工具兼容规则源
- `.cursorrules`：Cursor 规则兼容源
- `.windsurfrules`：Windsurf 规则兼容源

## 8. 推荐的落地顺序

1. 先写 `.clineignore`
2. 再写 `.clinerules/coding.md`
3. 再拆出 testing / architecture 等主题文件
4. 最后再加 `AGENTS.md` 做跨工具兼容

## 9. 官方参考

- [Cline Rules](https://docs.cline.bot/features/cline-rules/overview)
- [Conditional Rules](https://docs.cline.bot/features/conditional-rules)
- [.clineignore](https://docs.cline.bot/prompting/prompt-engineering-guide/clineignore-file-guide)
