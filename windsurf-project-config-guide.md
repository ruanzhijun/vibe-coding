# Windsurf 项目级配置清单

Windsurf 当前的项目级配置主线很清晰，核心就是两套：

- `.windsurf/rules/*.md`
- `AGENTS.md`

此外，如果你希望连“索引时忽略哪些文件”也项目化，还应补上：

- `.codeiumignore`

先给推荐结构：

```text
repo/
├─ AGENTS.md
├─ .codeiumignore
└─ .windsurf/
   └─ rules/
      ├─ project.md
      ├─ frontend.md
      └─ backend.md
```

## 1. `.windsurf/rules/*.md`

这是 Windsurf 当前最核心的 workspace 级规则目录。

官方当前文档明确说明：

- workspace 规则放在 `.windsurf/rules/*.md`
- 一个文件就是一条 rule
- 每条 rule 都有自己的 activation mode

### 1.1 它负责什么

适合放：

- 项目全局约束
- 技术栈要求
- 目录级规则
- 测试规范
- 文档规范
- 安全限制

### 1.2 激活模式

官方文档当前给出的触发模式包括：

- `always_on`
- `model_decision`
- `glob`
- `manual`

### 1.3 规则发现范围

Windsurf 官方当前说明里还有一个非常关键的点：

- 它不只看当前根目录
- 会扫描当前 workspace 的子目录
- 对 git 仓库，还会向上搜索到 git root

## 2. `AGENTS.md`

Windsurf 对 `AGENTS.md` 的支持非常明确，而且比很多工具更强的一点是：

- 它支持目录级自动作用域

### 2.1 它怎么工作

官方当前文档明确说明：

- 根目录的 `AGENTS.md` 会被当作 always-on 规则
- 子目录的 `AGENTS.md` 会自动变成该目录的 glob 规则

### 2.2 它和 `.windsurf/rules` 的区别

可以简单理解成：

- `AGENTS.md`：更轻量，按位置自动推导作用域
- `.windsurf/rules/*.md`：更强，靠 frontmatter 明确控制触发方式

## 3. `.codeiumignore`

这个文件不属于“规则提示词”，但它是 Windsurf 当前非常实用的项目级配置文件。

官方文档明确说明：

- 如果要进一步控制索引范围，可以在仓库根放 `.codeiumignore`
- 被忽略的文件不会被索引
- 也不能被 Cascade 编辑

## 4. 不要混淆的配置

Windsurf 还有一些很重要的控制项，例如：

- 终端 allow list
- 终端 deny list

但这些目前走的是用户/IDE settings，不是仓库内项目文件。

## 5. 最推荐的目录方案

### 5.1 最小必备版

```text
repo/
├─ AGENTS.md
└─ .windsurf/
   └─ rules/
      └─ project.md
```

### 5.2 标准实用版

```text
repo/
├─ AGENTS.md
├─ .codeiumignore
└─ .windsurf/
   └─ rules/
      ├─ project.md
      ├─ frontend.md
      ├─ backend.md
      └─ testing.md
```

### 5.3 目录就近增强版

```text
repo/
├─ AGENTS.md
├─ .codeiumignore
├─ .windsurf/
│  └─ rules/
│     ├─ cross-cutting.md
│     └─ release.md
├─ frontend/
│  └─ AGENTS.md
└─ backend/
   └─ AGENTS.md
```

## 6. 一句话理解每类文件

- `.windsurf/rules/*.md`：Windsurf 当前主线的项目规则文件
- `AGENTS.md`：按目录自动作用域的轻量规则文件
- `.codeiumignore`：控制索引与可编辑范围的忽略文件

## 7. 推荐的落地顺序

1. 先写根目录 `AGENTS.md`
2. 再补 `.windsurf/rules/project.md`
3. 需要分目录时，再在子目录放 `AGENTS.md`
4. 最后补 `.codeiumignore`

## 8. 官方参考

- [Windsurf Memories & Rules](https://docs.windsurf.com/windsurf/cascade/memories)
- [Windsurf AGENTS.md](https://docs.windsurf.com/windsurf/cascade/agents-md)
- [Windsurf Ignore](https://docs.windsurf.com/context-awareness/windsurf-ignore)
