# Gemini CLI 项目级配置清单

Gemini CLI 是当前少数把“项目 instruction + 项目 settings + 项目 MCP + 项目 sandbox”都公开成正式机制的工具之一。

本文按当前官方文档和公开 schema，重新整理一份更完整的项目级配置清单。

先给结论：如果你准备把 Gemini CLI 当成项目级 vibe coding 工具，推荐先准备下面这套结构。

```text
repo/
├─ GEMINI.md
├─ .geminiignore
└─ .gemini/
   ├─ settings.json
   ├─ .env
   ├─ sandbox.Dockerfile
   ├─ sandbox-macos-custom.sb
   └─ system.md
```

其中最核心的是：

- `GEMINI.md`
- `.gemini/settings.json`
- `.geminiignore`
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

这意味着 `GEMINI.md` 很适合做总纲 + 局部补充的层级式项目记忆。

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

它是真正控制 Gemini CLI 项目行为的入口。

按当前公开 schema，可以确认的顶层配置至少包括：

- `$schema`
- `mcpServers`
- `policyPaths`
- `adminPolicyPaths`
- `general`
- `output`
- `ui`
- `ide`
- `privacy`
- `telemetry`
- `billing`
- `context`
- `tools`
- `mcp`
- `security`
- `model`

这意味着 Gemini CLI 的项目配置并不只是“写个 `GEMINI.md`”，而是真有完整的 `.gemini/settings.json` 配置面。

## 3. `.gemini/settings.json` 当前最值得关注的分类

### 3.1 `general.*`

这组主要是基础运行行为。

当前 schema 中可确认的重要字段包括：

- `general.preferredEditor`
- `general.vimMode`
- `general.defaultApprovalMode`
- `general.devtools`
- `general.enableAutoUpdate`
- `general.enableAutoUpdateNotification`
- `general.enableNotifications`
- `general.checkpointing.enabled`
- `general.plan.enabled`
- `general.plan.directory`
- `general.plan.modelRouting`
- `general.retryFetchErrors`
- `general.maxAttempts`
- `general.sessionRetention.enabled`
- `general.sessionRetention.maxAge`
- `general.sessionRetention.maxCount`
- `general.sessionRetention.minRetention`

### 3.2 `output.*`

当前 schema 能确认：

- `output.format`

官方当前只列出：

- `text`
- `json`

### 3.3 `ui.*`

Gemini CLI 当前 schema 里的 UI 配置比很多人想象得丰富。

能明确确认的字段包括：

- `ui.theme`
- `ui.autoThemeSwitching`
- `ui.terminalBackgroundPollingInterval`
- `ui.customThemes`
- `ui.hideWindowTitle`
- `ui.inlineThinkingMode`
- `ui.showStatusInTitle`
- `ui.dynamicWindowTitle`
- `ui.showHomeDirectoryWarning`
- `ui.showCompatibilityWarnings`
- `ui.hideTips`
- `ui.escapePastedAtSymbols`
- `ui.showShortcutsHint`
- `ui.compactToolOutput`
- `ui.hideBanner`
- `ui.hideContextSummary`
- `ui.footer.items`
- `ui.footer.showLabels`
- `ui.footer.hideCWD`
- `ui.footer.hideSandboxStatus`
- `ui.footer.hideModelInfo`
- `ui.footer.hideContextPercentage`
- `ui.hideFooter`
- `ui.collapseDrawerDuringApproval`
- `ui.showMemoryUsage`
- `ui.showLineNumbers`
- `ui.showCitations`
- `ui.showModelInfoInChat`

### 3.4 `ide.*`

当前 schema 能确认：

- `ide.enabled`
- `ide.hasSeenNudge`

### 3.5 `privacy.*` 和 `telemetry.*`

当前能确认的字段包括：

- `privacy.usageStatisticsEnabled`

以及 `telemetry` 下的：

- `telemetry.enabled`
- `telemetry.target`
- `telemetry.otlpEndpoint`
- `telemetry.otlpProtocol`
- `telemetry.logPrompts`
- `telemetry.outfile`
- `telemetry.useCollector`
- `telemetry.useCliAuth`

### 3.6 `context.*`

这组决定项目记忆和上下文文件行为。

当前公开 schema 中能确认的重要字段包括：

- `context.fileName`
- `context.importFormat`
- `context.discoveryMaxDirs`
- `context.includeDirectories`
- `context.loadMemoryFromIncludeDirectories`
- `context.traversalMaxDirs`
- `context.discoveryExcludeDirs`
- `context.fileFiltering.respectGitIgnore`
- `context.fileFiltering.respectGeminiIgnore`
- `context.fileFiltering.enableRecursiveFileSearch`
- `context.fileFiltering.disableFuzzySearch`
- `context.fileFiltering.additionalIgnoreFiles`

### 3.7 `tools.*`

这组决定 Gemini CLI 在项目里可以怎么用工具。

当前公开 schema 里能确认的重要字段包括：

- `tools.sandbox`
- `tools.sandboxAllowedPaths`
- `tools.sandboxNetworkAccess`
- `tools.shell.enableInteractiveShell`
- `tools.shell.backgroundCompletionBehavior`
- `tools.shell.pager`
- `tools.shell.showColor`
- `tools.shell.inactivityTimeout`
- `tools.shell.enableShellOutputEfficiency`
- `tools.core`
- `tools.allowed`
- `tools.exclude`
- `tools.discoveryCommand`
- `tools.callCommand`
- `tools.useRipgrep`
- `tools.truncateToolOutputThreshold`
- `tools.disableLLMCorrection`
- `tools.useWriteTodos`

### 3.8 `mcpServers` 与 `mcp.*`

Gemini CLI 当前有两层和 MCP 相关的配置：

- 顶层 `mcpServers`
- 分类配置 `mcp.*`

顶层 `mcpServers` 是项目级 MCP server 定义本体。

当前 schema 能确认的 server 字段包括：

- `command`
- `args`
- `env`
- `cwd`
- `timeout`
- `trust`
- `description`
- `includeTools`
- `excludeTools`
- `oauth`
- `authProviderType`
- `targetAudience`
- `targetServiceAccount`

而 `mcp.*` 这组还能控制：

- `mcp.serverCommand`
- `mcp.allowed`
- `mcp.excluded`

### 3.9 `security.*`

当前 schema 能确认的重要字段包括：

- `security.toolSandboxing`
- `security.disableYoloMode`
- `security.disableAlwaysAllow`
- `security.enablePermanentToolApproval`
- `security.autoAddToPolicyByDefault`
- `security.blockGitExtensions`
- `security.allowedExtensions`
- `security.folderTrust.enabled`
- `security.environmentVariableRedaction.allowed`
- `security.environmentVariableRedaction.blocked`
- `security.environmentVariableRedaction.enabled`
- `security.auth.selectedType`
- `security.auth.enforcedType`

### 3.10 `model.*`

Gemini CLI 当前公开 schema 中，`model` 这一层已经不只是“填个模型名”。

从 schema 里至少能确认：

- 存在大量内置 model aliases
- 支持 `customAliases`
- 支持 `modelConfig` 继承
- 能控制 `generateContentConfig`
- 能控制 thinking / compression / summarizer 等相关模型路由

## 4. `.geminiignore`

这个文件比当前公开文档页里的独立介绍更分散，但它已经明确出现在官方 schema 的 `respectGeminiIgnore` 与 `additionalIgnoreFiles` 相关配置中。

因此可以把它理解为：

- Gemini CLI 的项目级 companion ignore file

### 4.1 它负责什么

最适合放：

- 不希望被上下文扫描的敏感文件
- 构建产物
- 大型无关目录
- AI 不应读取的模板或密钥文件

### 4.2 和 `.gitignore` 的关系

当前 schema 已明确支持：

- `respectGitIgnore`
- `respectGeminiIgnore`
- `additionalIgnoreFiles`

并且 `additionalIgnoreFiles` 的优先级可以高于 `.geminiignore` 和 `.gitignore`。

## 5. `.gemini/.env`

Gemini CLI 会自动加载 `.env` 文件，而且文档专门区分了普通项目 `.env` 和 `.gemini/.env`。

### 5.1 它适合放什么

最适合放：

- 当前项目专用的 API key
- 模型路由相关变量
- 只对 Gemini CLI 生效的环境变量

### 5.2 一个关键细节

官方当前文档明确说明：

- 某些变量默认会从普通项目 `.env` 里被排除，避免干扰 Gemini CLI
- 但 `.gemini/.env` 中的变量不会被这套排除逻辑过滤

## 6. `.gemini/sandbox.Dockerfile`

如果项目对 sandbox 有定制需求，可以把自定义 Docker 沙箱定义在：

- `.gemini/sandbox.Dockerfile`

## 7. `.gemini/sandbox-macos-*.sb`

Gemini CLI 官方当前文档也支持 macOS 的自定义 Seatbelt profile。

项目里可以放：

- `.gemini/sandbox-macos-custom.sb`

## 8. `.gemini/system.md`

这个文件不是默认自动生效的项目说明文件。

它只有在显式使用 system prompt override 机制时才会参与。

因此要把它理解成：

- 可选的系统提示模板/覆盖文件
- 不是替代 `GEMINI.md` 的主入口

## 9. 最推荐的职责分工

### 9.1 最小必备版

```text
repo/
├─ GEMINI.md
├─ .geminiignore
└─ .gemini/
   └─ settings.json
```

### 9.2 标准实用版

```text
repo/
├─ GEMINI.md
├─ .geminiignore
└─ .gemini/
   ├─ settings.json
   └─ .env
```

### 9.3 进阶版

```text
repo/
├─ GEMINI.md
├─ .geminiignore
└─ .gemini/
   ├─ settings.json
   ├─ .env
   ├─ sandbox.Dockerfile
   ├─ sandbox-macos-custom.sb
   └─ system.md
```

## 10. 一句话理解每类文件

- `GEMINI.md`：项目级 instruction / memory 文件
- `.gemini/settings.json`：项目级主配置
- `.geminiignore`：Gemini CLI 补充忽略文件
- `.gemini/.env`：项目级 Gemini CLI 专用环境变量
- `.gemini/sandbox.Dockerfile`：项目自定义 Docker 沙箱
- `.gemini/sandbox-macos-*.sb`：项目自定义 macOS 沙箱
- `.gemini/system.md`：可选的系统提示覆盖文件

## 11. 推荐的落地顺序

1. 先写 `GEMINI.md`
2. 再写 `.gemini/settings.json`
3. 再补 `.geminiignore`
4. 再补 `.gemini/.env`
5. 如有安全需求再补 sandbox 文件
6. 最后才考虑 `system.md` 这种高级覆盖

## 12. 官方参考

- [Gemini CLI configuration](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/configuration.md)
- [Gemini CLI get-started configuration](https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/configuration.md)
- [Gemini CLI settings schema](https://raw.githubusercontent.com/google-gemini/gemini-cli/main/schemas/settings.schema.json)
- [Gemini CLI repository](https://github.com/google-gemini/gemini-cli)
