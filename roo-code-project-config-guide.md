# Roo Code 项目级配置清单

Roo Code 的项目级配置入口是存在的，但它不像 Cursor、Claude、Codex 那样被一份“总配置文档”清晰集中展示。

截至目前，基于 Roo Code 官方文档站、官方文档仓库、官方仓库 issue 与官方 security advisory，项目级最值得关注的是这几类文件：

- `AGENTS.md`
- `.roomodes`
- `.roorules`
- `.roo/mcp.json`

推荐结构如下：

```text
repo/
├─ AGENTS.md
├─ .roomodes
├─ .roorules
└─ .roo/
   └─ mcp.json
```

## 1. `AGENTS.md`

Roo Code 官方文档仓库本身就在仓库根使用 `AGENTS.md`。

它的定位很直接：

- 给 agent 的项目说明
- 适合放项目约束、命令、规范、架构信息

## 2. `.roomodes`

`.roomodes` 是 Roo Code 的项目级自定义模式文件。

这点可以从 Roo Code 官方文档仓库根目录和官方 issue 中稳定确认。

### 2.1 它负责什么

适合定义：

- 自定义 mode
- 每个 mode 的角色定义
- 何时使用这个 mode
- 该 mode 允许的工具组
- 某些 mode 能编辑哪些类型的文件

### 2.2 从官方仓库能看到的字段

Roo Code 官方文档仓库里的 `.roomodes` 能确认到的核心结构包括：

- `customModes`
- `slug`
- `name`
- `roleDefinition`
- `whenToUse`
- `groups`
- `source`

## 3. `.roorules`

`.roorules` 是 Roo Code 的项目规则文件。

官方文档仓库根目录直接使用了它，内容是项目规则的 Markdown 文本。

### 3.1 它负责什么

适合写：

- 文档规则
- 链接规范
- 输出格式约束
- 发布说明禁忌
- 特定框架的写法要求

## 4. `.roo/mcp.json`

这是 Roo Code 的项目级 MCP 配置文件。

Roo Code 官方 security advisory 明确确认：

- 项目级 MCP 配置就存放在 `.roo/mcp.json`

### 4.1 它负责什么

适合放：

- 项目专用 MCP servers
- Context7
- GitHub
- 浏览器
- 数据库
- 内部工具服务

### 4.2 为什么要单独强调

Roo Code 官方 security advisory 直接指出：

- `.roo/mcp.json` 允许定义可执行命令
- 这会带来真实的安全边界问题

## 5. 可能还会出现的 supporting files

从 Roo Code 官方文档仓库实际内容看，你还可能在项目中看到：

- `.roo/*.xml`
- `.roo/rules-*/*`

这些更像 supporting files，而不是所有项目都必须有的主配置入口。

## 6. 最推荐的目录方案

### 6.1 最小必备版

```text
repo/
├─ AGENTS.md
├─ .roomodes
└─ .roo/
   └─ mcp.json
```

### 6.2 标准实用版

```text
repo/
├─ AGENTS.md
├─ .roomodes
├─ .roorules
└─ .roo/
   └─ mcp.json
```

### 6.3 进阶版

```text
repo/
├─ AGENTS.md
├─ .roomodes
├─ .roorules
└─ .roo/
   ├─ mcp.json
   ├─ architect.xml
   └─ docs.xml
```

## 7. 一句话理解每类文件

- `AGENTS.md`：项目共享说明和工作约束
- `.roomodes`：项目自定义模式定义
- `.roorules`：项目共享规则
- `.roo/mcp.json`：项目级 MCP 配置

## 8. 推荐的落地顺序

1. 先写 `AGENTS.md`
2. 再写 `.roorules`
3. 需要分角色时再写 `.roomodes`
4. 最后再补 `.roo/mcp.json`

## 9. 说明边界

Roo Code 目前的公开资料确实比其他工具分散一些，所以这里的结论优先基于：

- Roo Code 官方文档站
- RooCodeInc 官方文档仓库
- RooCodeInc 官方仓库 security advisory
- RooCodeInc 官方 issue 中对项目配置文件的实际讨论

## 10. 官方参考

- [Roo Code Docs](https://docs.roocode.com/)
- [RooCodeInc/Roo-Code-Docs](https://github.com/RooCodeInc/Roo-Code-Docs)
- [Roo Code MCP security advisory](https://github.com/RooCodeInc/Roo-Code/security/advisories/GHSA-5x8h-m52g-5v54)
