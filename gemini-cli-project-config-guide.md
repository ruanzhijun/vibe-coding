# Gemini CLI 项目级配置清单

Gemini CLI 是当前少数把“项目 instruction + 项目 settings + 项目 MCP + 项目 sandbox”都公开成正式机制的工具之一。

本文重点整理：

- 项目里应该放哪些 Gemini CLI 配置文件
- 每个文件分别负责什么
- 当前主线配置项应该怎么看

先给结论：如果你准备把 Gemini CLI 当成项目级 vibe coding 工具，推荐先准备下面这套结构。

```text
repo/
├─ GEMINI.md
└─ .gemini/
   ├─ settings.json
   ├─ .env
   ├─ sandbox.Dockerfile
   ├─ sandbox-macos-custom.sb
   └─ system.md                  # 只有在显式启用 system prompt override 时才会用到
```

其中最核心的是：

- `GEMINI.md`
- `.gemini/settings.json`
- `.gemini/.env`

## 1. `GEMINI.md`

这是 Gemini CLI 的项目级上下文文件，也就是项目记忆 / instruction 文件。

### 1.1 它负责什么

适合写进 `GEMINI.md` 的内容：

- 项目结构
- 构建命令
- 测试命令
- 代码风格
- 目录禁区
- 架构原则
- 特定模块约定

### 1.2 它不是一次性单点加载

Gemini CLI 官方当前文档明确说明，context files 具有层级扫描能力：

- 会从全局目录加载
- 会从当前目录和父目录向上找
- 还会在当前目录下继续找更具体的子目录上下文文件

这意味着 `GEMINI.md` 很适合做“总纲 + 局部补充”的层级式项目记忆。

### 1.3 可以改成别的文件名

官方文档里能确认：

- 默认文件名是 `GEMINI.md`
- 可以通过 settings 调整为 `AGENTS.md`
- 也可以接受文件名数组

这使 Gemini CLI 可以和其他工具共享一套项目说明文件。

## 2. `.gemini/settings.json`

这是 Gemini CLI 的项目级主配置文件。

官方明确说明：

- 用户级配置在 `~/.gemini/settings.json`
- 项目级配置在 `<project>/.gemini/settings.json`
- 项目级会覆盖用户级

### 2.1 它负责什么

它是真正控制 Gemini CLI 项目行为的入口，主要覆盖：

- approval mode
- model
- UI
- tools
- MCP
- context
- privacy
- security
- telemetry
- policy files

### 2.2 当前文档里要注意的一个点

Gemini CLI 官方公开文档目前存在两套表述：

1. 一套旧/兼容写法会提到类似 `contextFileName`
2. 当前 get-started 文档主线已经采用分类后的嵌套写法，例如：
   - `general.*`
   - `tools.*`
   - `context.*`
   - `privacy.*`
   - `security.*`

如果你是新项目，建议优先按当前分组式写法理解，并在落地前以官方 schema 为准。

## 3. `.gemini/settings.json` 当前最值得关注的分类

### 3.1 `general.*`

这组主要是基础运行行为。

常见项目级关注点包括：

- `general.preferredEditor`
- `general.vimMode`
- `general.defaultApprovalMode`
- `general.checkpointing.enabled`
- `general.sessionRetention.*`

### 3.2 `tools.*`

这组决定 Gemini CLI 在项目里可以怎么用工具。

常见关注点包括：

- `tools.sandbox`
- `tools.discoveryCommand`
- `tools.callCommand`
- `tools.core`
- `tools.exclude`

### 3.3 `mcpServers`

这是项目级 MCP 入口。

官方当前文档能确认的 server 字段包括：

- `command`
- `args`
- `env`
- `cwd`
- `timeout`
- `trust`
- `includeTools`
- `excludeTools`

### 3.4 `context.*`

这组决定项目记忆和上下文文件行为。

当前值得重点关注的包括：

- `context.fileName`
- `context.includeDirectories`
- `context.loadMemoryFromIncludeDirectories`
- `context.discoveryMaxDirs`
- `context.fileFiltering.respectGitIgnore`
- `context.fileFiltering.respectGeminiIgnore`

### 3.5 `privacy.*`

项目里最常见的是：

- `privacy.usageStatisticsEnabled`

### 3.6 `security.*`

当前文档里能确认的重要点包括：

- `security.allowedEnvironmentVariables`
- `security.blockedEnvironmentVariables`

### 3.7 `policyPaths`

如果你们团队还要叠加外部 policy 文件或目录，入口就在这里。

## 4. `.gemini/.env`

Gemini CLI 会自动加载 `.env` 文件，而且文档还专门区分了普通项目 `.env` 和 `.gemini/.env`。

### 4.1 它适合放什么

最适合放：

- 当前项目专用的 API key
- 模型路由相关变量
- 只对 Gemini CLI 生效的环境变量

### 4.2 一个关键细节

官方当前文档明确说明：

- 某些变量默认会从“普通项目 `.env`”里被排除，避免干扰 Gemini CLI
- 但 `.gemini/.env` 中的变量不会被这套排除逻辑过滤

## 5. `.gemini/sandbox.Dockerfile`

如果项目对 sandbox 有定制需求，可以把自定义 Docker 沙箱定义在：

- `.gemini/sandbox.Dockerfile`

## 6. `.gemini/sandbox-macos-*.sb`

Gemini CLI 官方当前文档也支持 macOS 的自定义 Seatbelt profile。

项目里可以放：

- `.gemini/sandbox-macos-custom.sb`

## 7. `.gemini/system.md`

这个文件不是默认自动生效的项目说明文件。

它只有在你显式使用 system prompt override 机制时才会参与。

因此要把它理解成：

- 可选的系统提示模板/覆盖文件
- 不是替代 `GEMINI.md` 的主入口

## 8. 最推荐的职责分工

### 8.1 最小必备版

```text
repo/
├─ GEMINI.md
└─ .gemini/
   └─ settings.json
```

### 8.2 标准实用版

```text
repo/
├─ GEMINI.md
└─ .gemini/
   ├─ settings.json
   └─ .env
```

### 8.3 进阶版

```text
repo/
├─ GEMINI.md
└─ .gemini/
   ├─ settings.json
   ├─ .env
   ├─ sandbox.Dockerfile
   ├─ sandbox-macos-custom.sb
   └─ system.md
```

## 9. 一句话理解每类文件

- `GEMINI.md`：项目级 instruction / memory 文件
- `.gemini/settings.json`：项目级主配置
- `.gemini/.env`：项目级 Gemini CLI 专用环境变量
- `.gemini/sandbox.Dockerfile`：项目自定义 Docker 沙箱
- `.gemini/sandbox-macos-*.sb`：项目自定义 macOS 沙箱
- `.gemini/system.md`：可选的系统提示覆盖文件

## 10. 推荐的落地顺序

1. 先写 `GEMINI.md`
2. 再写 `.gemini/settings.json`
3. 再补 `.gemini/.env`
4. 如有安全需求再补 sandbox 文件
5. 最后才考虑 `system.md` 这种高级覆盖

## 11. 官方参考

- [Gemini CLI configuration](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/configuration.md)
- [Gemini CLI get-started configuration](https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/configuration.md)
- [Gemini CLI settings schema](https://raw.githubusercontent.com/google-gemini/gemini-cli/main/schemas/settings.schema.json)
