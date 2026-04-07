# Claude 项目级配置清单

本文系统整理一套适合 **Claude Code 作为 vibe coding 工具** 时的项目级配置方案，重点回答 3 个问题：

- 项目里应该放哪些 Claude 相关配置文件
- 每个文件分别负责什么
- 每类文件可以配置哪些关键配置项

先给结论：如果你准备把 Claude Code 当成项目级的主力工具，推荐优先准备下面这套结构。

```text
repo/
├─ CLAUDE.md
├─ CLAUDE.local.md                # 已被官方标记为 deprecated，仅保留兼容理解
├─ .mcp.json
├─ .claude/
│  ├─ settings.json
│  ├─ settings.local.json
│  ├─ agents/
│  │  ├─ reviewer.md
│  │  ├─ explorer.md
│  │  └─ impl.md
│  ├─ skills/
│  │  ├─ test-runner/
│  │  │  └─ SKILL.md
│  │  └─ release-check/
│  │     └─ SKILL.md
│  ├─ commands/                   # 兼容旧写法，建议逐步迁到 skills
│  │  └─ commit.md
│  ├─ output-styles/
│  │  └─ explanatory-plus.md
│  └─ hooks/                      # 只是脚本存放约定，真正配置在 settings.json
│     ├─ pre_tool_use.sh
│     └─ post_tool_use.sh
```

其中真正属于 Claude Code 项目级配置的核心是：

- `CLAUDE.md`
- `.claude/settings.json`
- `.claude/settings.local.json`
- `.mcp.json`
- `.claude/agents/*.md`
- `.claude/skills/<skill>/SKILL.md`
- `.claude/output-styles/*.md`

`.claude/commands/` 仍然可用，但官方已经把“自定义命令”并入 skills 体系，新的项目建议优先使用 `.claude/skills/`。

## 1. Claude Code 的项目级配置体系

Claude Code 的配置是多层级的。项目级最常见的是这 4 类：

1. **Project memory**：`./CLAUDE.md`
   给 Claude 提供项目共享的说明、架构、约束和习惯。
2. **Project settings**：`.claude/settings.json`
   给 Claude 配 JSON 配置，例如权限、hooks、env、模型、插件等。
3. **Local project settings**：`.claude/settings.local.json`
   同样是 JSON 配置，但只对当前机器当前仓库生效，默认不进版本控制。
4. **Project MCP config**：`.mcp.json`
   给当前项目共享 MCP server。

官方的优先级，从高到低是：

1. Managed settings
2. 命令行参数
3. `.claude/settings.local.json`
4. `.claude/settings.json`
5. `~/.claude/settings.json`

同名数组配置在不同 scope 下不是覆盖，而是**合并并去重**。这点很重要，特别是 `permissions.allow`、`sandbox.filesystem.allowWrite` 这类数组配置。

## 2. `CLAUDE.md`

这是 Claude Code 最重要的项目说明文件之一。它本质上不是“权限配置”，而是“项目记忆”和“工作指令”。

适合写入的内容：

- 项目结构
- 正确构建命令
- 正确测试命令
- 代码风格和命名约定
- 哪些目录不要动
- 哪些文件默认不要读
- 关键架构和调用链
- 团队约定和提交规范

Claude Code 会自动加载 `CLAUDE.md`。官方还支持在 `CLAUDE.md` 里使用 `@path` 语法导入其他文件，例如：

```md
See @README for project overview and @docs/deploy.md for deployment steps.
```

这意味着对大型仓库来说，推荐做法不是把所有指令都塞在一个超长 `CLAUDE.md` 里，而是把总纲写在 `CLAUDE.md`，再按模块拆到 `docs/` 里，由 `@路径` 引入。

### 2.1 `CLAUDE.local.md`

这个文件官方仍然识别，但已经明确标记为 **deprecated**。更推荐的替代方式是：

- 在 `CLAUDE.md` 中通过 `@~/.claude/...` 导入个人私有说明
- 或者直接使用 `.claude/settings.local.json`

所以在新项目里：

- 可以理解它
- 不建议把它作为主方案

## 3. `.claude/settings.json`

这是 Claude Code 官方的项目级 JSON 配置入口。它适合放**团队共享、应进入版本控制**的配置。

最重要的点：

- 它是正式官方配置文件
- 它适合放共享权限、共享 hooks、共享 MCP 相关配置、共享插件配置
- 它不适合放个人机器专属偏好和敏感凭据

### 3.1 推荐的职责边界

适合放进 `.claude/settings.json` 的内容：

- 权限规则
- 默认 permission mode
- 共享 hooks
- 共享环境变量
- 团队统一模型偏好
- 项目级 output style 指定
- 共享 MCP 开启策略
- 插件启用状态和 marketplace 配置
- 文件建议脚本、状态栏脚本、git attribution

不建议放进 `.claude/settings.json` 的内容：

- 本机路径差异很大的设置
- 带秘密值的 env
- 你个人临时实验中的宽松权限
- 只适用于你机器的 sandbox/network 例外

## 4. `.claude/settings.json` 可配置项总览

下面按功能分类列出项目级最值得关注的配置项。这里重点列**项目级最常用和最相关**的项，同时标注少量“虽然存在但不适合普通项目仓库”的项。

### 4.1 权限配置 `permissions`

核心字段：

- `permissions.allow`
- `permissions.ask`
- `permissions.deny`
- `permissions.additionalDirectories`
- `permissions.defaultMode`
- `permissions.disableBypassPermissionsMode`
- `permissions.skipDangerousModePermissionPrompt`

作用：

- `allow`：默认放行的工具规则
- `ask`：命中时弹确认
- `deny`：明确拒绝
- `additionalDirectories`：给 Claude 增加额外工作目录访问
- `defaultMode`：设置默认权限模式
- `disableBypassPermissionsMode`：禁止 bypass 权限模式
- `skipDangerousModePermissionPrompt`：跳过进入危险模式的确认提示

`defaultMode` 常见值：

- `default`
- `acceptEdits`
- `plan`
- `auto`
- `dontAsk`
- `bypassPermissions`

注意：

- `skipDangerousModePermissionPrompt` 在共享项目设置中会被忽略，防止仓库默认替你跳过危险确认
- 权限规则格式支持 `Tool` 或 `Tool(specifier)`，例如：

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run lint)",
      "Bash(npm run test *)"
    ],
    "ask": [
      "Bash(git push *)"
    ],
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./secrets/**)",
      "Bash(curl *)",
      "WebFetch"
    ],
    "defaultMode": "acceptEdits"
  }
}
```

### 4.2 沙箱配置 `sandbox`

核心字段：

- `sandbox.enabled`
- `sandbox.failIfUnavailable`
- `sandbox.autoAllowBashIfSandboxed`
- `sandbox.excludedCommands`
- `sandbox.allowUnsandboxedCommands`
- `sandbox.filesystem.allowWrite`
- `sandbox.filesystem.denyWrite`
- `sandbox.filesystem.denyRead`
- `sandbox.filesystem.allowRead`
- `sandbox.network.allowUnixSockets`
- `sandbox.network.allowAllUnixSockets`
- `sandbox.network.allowLocalBinding`
- `sandbox.network.allowedDomains`
- `sandbox.network.httpProxyPort`
- `sandbox.network.socksProxyPort`
- `sandbox.enableWeakerNestedSandbox`
- `sandbox.enableWeakerNetworkIsolation`

这组配置决定：

- Bash 是否真正跑在沙箱里
- 哪些命令可以绕过沙箱
- 哪些目录可以读写
- 哪些域名能出网
- 本地端口和 Unix socket 能不能访问

典型项目级配置示例：

```json
{
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": true,
    "excludedCommands": ["docker *"],
    "filesystem": {
      "allowWrite": ["./tmp", "./dist"],
      "denyRead": ["~/.aws/credentials"]
    },
    "network": {
      "allowedDomains": ["github.com", "*.npmjs.org"]
    }
  }
}
```

### 4.3 环境变量 `env`

核心字段：

- `env`

作用：

- 为每个 Claude 会话注入环境变量
- 可用于项目级统一设置少量共享变量

示例：

```json
{
  "env": {
    "APP_ENV": "development",
    "CI": "0"
  }
}
```

注意：

- 不建议把真实秘钥直接提交到项目 `settings.json`
- 敏感值更适合放到 `.claude/settings.local.json` 或系统环境变量中

### 4.4 Hooks `hooks`

核心字段：

- `hooks`
- `allowedHttpHookUrls`
- `httpHookAllowedEnvVars`
- `disableAllHooks`

Claude Code 的 hooks 是通过 `settings.json` 配置的，**不是单独一个 hooks 配置文件**。`.claude/hooks/` 通常只是脚本存放目录的约定。

Claude 支持的常见 hook 事件包括：

- `PreToolUse`
- `PostToolUse`
- `UserPromptSubmit`
- `SessionStart`
- `Stop`
- `SubagentStop`
- `Notification`
- `PreCompact`
- `SessionEnd`

典型结构：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/check-style.sh",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

你可以在项目里用 `.claude/hooks/` 存这些脚本，例如：

```text
.claude/hooks/
├─ check-style.sh
├─ forbid-prod-edit.sh
└─ status-line.sh
```

### 4.5 模型与输出风格

核心字段：

- `model`
- `modelOverrides`
- `alwaysThinkingEnabled`
- `effortLevel`
- `showThinkingSummaries`
- `language`
- `outputStyle`
- `includeGitInstructions`

作用：

- 指定默认模型
- 为不同 provider 覆盖模型 ID
- 控制是否默认启用 extended thinking
- 控制 effort level
- 控制思考摘要显示
- 设置默认语言
- 绑定 output style
- 是否保留内建 git 工作流指令

说明：

- `outputStyle` 只是在 settings 里指定“当前使用哪种输出风格”
- 真正的自定义 output style 文件放在 `.claude/output-styles/`

### 4.6 文件建议与工作目录补充

核心字段：

- `fileSuggestion`
- `respectGitignore`
- `permissions.additionalDirectories`

作用：

- 自定义 `@` 文件自动补全逻辑
- 控制文件建议是否遵守 `.gitignore`
- 允许 Claude 访问额外目录

这在大仓库或 monorepo 中非常有用。

### 4.7 MCP 相关项目设置

核心字段：

- `enableAllProjectMcpServers`
- `enabledMcpjsonServers`
- `disabledMcpjsonServers`

作用：

- 是否自动批准项目 `.mcp.json` 中全部 server
- 白名单批准某些 `.mcp.json` server
- 黑名单拒绝某些 `.mcp.json` server

### 4.8 插件相关项目设置

核心字段：

- `enabledPlugins`
- `extraKnownMarketplaces`

作用：

- 控制哪些插件在这个仓库启用
- 给团队预注册 marketplace 来源

示例：

```json
{
  "enabledPlugins": {
    "formatter@team-tools": true,
    "analyzer@security-plugins": false
  },
  "extraKnownMarketplaces": {
    "team-tools": {
      "source": {
        "source": "github",
        "repo": "acme-corp/claude-plugins"
      }
    }
  }
}
```

### 4.9 其他常见项目级字段

常见可用字段还包括：

- `attribution`
- `companyAnnouncements`
- `defaultShell`
- `plansDirectory`
- `prefersReducedMotion`
- `spinnerTipsEnabled`
- `spinnerTipsOverride`
- `spinnerVerbs`
- `statusLine`
- `feedbackSurveyRate`
- `showClearContextOnPlanAccept`
- `fastModePerSessionOptIn`
- `voiceEnabled`

这些里最常见的项目级用途：

- `attribution`：控制 Claude 生成的 commit/PR attribution
- `companyAnnouncements`：团队启动公告
- `defaultShell`：尤其 Windows 项目可能要切 `powershell`
- `statusLine`：定制状态栏脚本
- `plansDirectory`：统一计划文件落点

## 5. `.claude/settings.local.json`

这个文件和 `.claude/settings.json` 使用**同一套 JSON schema**，但作用域是：

- 只对你自己生效
- 只在当前仓库生效
- 默认不提交到 git

典型用途：

- 放你本机专属的宽松权限规则
- 放你自己的 output style 偏好
- 放本机路径相关 env
- 放临时实验中的 hook
- 放个人试验性 MCP 启用策略

最推荐的分工方式：

- 团队规则放 `.claude/settings.json`
- 个人覆盖放 `.claude/settings.local.json`

## 6. `.mcp.json`

这是 Claude Code 的**项目级 MCP 共享配置文件**。

如果你希望团队成员在这个项目里共享同一批 MCP 工具，例如：

- GitHub
- Notion
- Stripe
- 内部 API
- Playwright
- 数据库工具

就应该把它们定义在 `.mcp.json`。

标准结构：

```json
{
  "mcpServers": {
    "shared-server": {
      "command": "/path/to/server",
      "args": [],
      "env": {}
    }
  }
}
```

常见 server 定义字段包括：

- `type`
- `command`
- `args`
- `env`
- `url`
- `headers`
- `oauth`

Claude 官方支持在 `.mcp.json` 中做环境变量展开，常见格式：

- `${VAR}`
- `${VAR:-default}`

例如：

```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}"
      }
    }
  }
}
```

注意：

- 项目级 `.mcp.json` 会在首次使用时要求批准
- Claude 提供 `claude mcp reset-project-choices` 用于重置审批结果
- Windows 原生环境下，某些 `npx` 型 stdio server 需要 `cmd /c` 包一层

## 7. `.claude/agents/*.md`

这是 Claude Code 的**项目级自定义 subagent** 目录。

推荐结构：

```text
.claude/agents/
├─ reviewer.md
├─ explorer.md
├─ impl.md
└─ browser-tester.md
```

每个 subagent 都是一个 Markdown 文件，顶部带 YAML frontmatter，正文是它的 system prompt。

基础示例：

```md
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Glob, Grep
model: sonnet
---

You are a code reviewer. Focus on quality, security, and maintainability.
```

### 7.1 subagent frontmatter 可配置项

官方支持的主要字段包括：

- `name`
- `description`
- `tools`
- `disallowedTools`
- `model`
- `permissionMode`
- `maxTurns`
- `skills`
- `mcpServers`
- `hooks`
- `memory`
- `background`
- `effort`
- `isolation`
- `color`
- `initialPrompt`

这些字段分别解决：

- 什么时候应该调用这个 agent
- 它能用哪些工具
- 它使用哪个模型
- 它是否在 worktree 中隔离执行
- 它是否加载特定 skills
- 它是否带项目内专属 MCP
- 它有没有持久记忆

这已经远不只是“一个 prompt 文件”，而是一整套 task agent 配置。

## 8. `.claude/skills/<skill>/SKILL.md`

这是现在官方推荐的**项目级复用工作流**配置方式。

推荐结构：

```text
.claude/skills/
├─ test-runner/
│  ├─ SKILL.md
│  ├─ examples.md
│  └─ scripts/
│     └─ validate.sh
└─ release-check/
   ├─ SKILL.md
   └─ checklist.md
```

skills 的作用类似“可被 Claude 自动加载或被你手动调用的 prompt 工作流”。

### 8.1 为什么推荐 skills

官方现在已经明确：

- 自定义命令已经并入 skills 体系
- `.claude/commands/*.md` 仍兼容
- 但新的项目建议优先使用 `.claude/skills/.../SKILL.md`

### 8.2 `SKILL.md` frontmatter 可配置项

主要字段包括：

- `name`
- `description`
- `argument-hint`
- `disable-model-invocation`
- `user-invocable`
- `allowed-tools`
- `model`
- `effort`
- `context`
- `agent`
- `hooks`
- `paths`
- `shell`

这些字段让 skill 可以控制：

- 你能不能主动 `/skill-name` 调用
- Claude 能不能自动触发它
- 触发时能用哪些工具
- 在哪些路径下才自动触发
- 是内联运行，还是 fork 到 subagent 中运行
- 要不要绑定某个 agent 或模型

### 8.3 `SKILL.md` 支持的动态变量

官方支持的字符串替换包括：

- `$ARGUMENTS`
- `$ARGUMENTS[N]`
- `$N`
- `${CLAUDE_SESSION_ID}`
- `${CLAUDE_SKILL_DIR}`

所以 skill 很适合做：

- `/fix-issue 123`
- `/deploy staging`
- `/review-pr 456 high`

### 8.4 supporting files

skill 目录不止可以放 `SKILL.md`，还可以放：

- `reference.md`
- `examples.md`
- `template.md`
- `scripts/*`

官方建议把 `SKILL.md` 控制在 500 行以内，详细说明拆到 supporting files 中，再在 `SKILL.md` 里显式引用。

## 9. `.claude/commands/*.md`

这个目录仍然有效，但更适合作为**兼容历史项目**的理解方式。

它的能力和 skills 已经高度重合，官方明确指出：

- `.claude/commands/deploy.md`
- `.claude/skills/deploy/SKILL.md`

现在都可以形成 `/deploy`。

因此推荐策略是：

- 老项目先不急着删
- 新项目优先直接用 skills

如果项目里仍保留 commands，它的文件也支持 frontmatter，例如：

- `allowed-tools`
- `argument-hint`
- `description`
- `model`

并支持：

- `!` 前缀执行 bash 预处理
- `@文件路径` 注入文件内容
- `$ARGUMENTS`、`$1`、`$2` 等参数替换

## 10. `.claude/output-styles/*.md`

Claude Code 支持项目级自定义 output styles。

推荐结构：

```text
.claude/output-styles/
└─ explanatory-plus.md
```

output style 用来修改 Claude 的主系统提示风格，不是技能，不是命令，不是 agent。

适合做的事情：

- 统一解释风格
- 统一输出格式
- 统一教学风格
- 把 Claude 调整成“更偏教学”“更偏审阅”“更偏规范输出”的助手

### 10.1 output style frontmatter 字段

主要字段包括：

- `name`
- `description`
- `keep-coding-instructions`

示例：

```md
---
name: My Custom Style
description: Explain implementation choices in a more educational way
keep-coding-instructions: true
---

# Custom Style Instructions

Always explain why a change is needed before presenting the final patch.
```

注意：

- output style 文件放在 `.claude/output-styles/`
- 真正“当前 session 用哪个 style”，是在 `settings.json` 里的 `outputStyle` 字段决定
- 官方说明里，`/config` 选择 output style 后，通常保存到 `.claude/settings.local.json`

## 11. `.claude/hooks/`

这个目录不是 Claude Code 自动发现的配置文件目录，但非常适合作为项目 hook 脚本的存放位置。

推荐放：

```text
.claude/hooks/
├─ pre_tool_use.sh
├─ post_tool_use.sh
├─ forbid-secrets.sh
└─ status-line.sh
```

然后在 `.claude/settings.json` 里通过 `hooks` 引用这些脚本。

## 12. 普通项目最推荐的目录方案

### 12.1 最少必备版

如果你要快速把 Claude Code 用起来，至少准备：

```text
repo/
├─ CLAUDE.md
└─ .claude/
   └─ settings.json
```

### 12.2 标准实用版

如果你要让团队真正协作顺畅，推荐：

```text
repo/
├─ CLAUDE.md
├─ .mcp.json
└─ .claude/
   ├─ settings.json
   ├─ agents/
   │  ├─ reviewer.md
   │  └─ impl.md
   ├─ skills/
   │  ├─ test-runner/
   │  │  └─ SKILL.md
   │  └─ release-check/
   │     └─ SKILL.md
   ├─ output-styles/
   │  └─ explanatory-plus.md
   └─ hooks/
      └─ check-style.sh
```

### 12.3 高配增强版

如果你已经进入稳定的 vibe coding 流程，再增加：

- `.claude/settings.local.json` 使用说明
- 更细的 skills 目录
- 更强的 hooks 守护
- 项目级 subagents
- 项目级 MCP server
- output style 定制
- 插件 marketplace 配置

## 13. 一句话理解每类文件

- `CLAUDE.md`：项目共享记忆和工作说明
- `CLAUDE.local.md`：旧的本地项目记忆文件，已不推荐作为主方案
- `.claude/settings.json`：项目共享 JSON 配置主入口
- `.claude/settings.local.json`：个人本地项目覆盖配置
- `.mcp.json`：项目共享 MCP server 配置
- `.claude/agents/*.md`：项目级 subagent 定义
- `.claude/skills/<skill>/SKILL.md`：项目级可复用技能和工作流
- `.claude/commands/*.md`：旧版自定义命令，仍兼容但建议迁移到 skills
- `.claude/output-styles/*.md`：项目级输出风格
- `.claude/hooks/*`：hook 脚本存放目录约定

## 14. 最后的建议

如果你现在要把 Claude Code 真正用于 vibe coding，我建议按下面顺序落地：

1. 先写 `CLAUDE.md`
2. 再写 `.claude/settings.json`
3. 把敏感和个人化内容放 `.claude/settings.local.json`
4. 再补 `.mcp.json`
5. 然后沉淀 `.claude/skills/`
6. 再建立 `.claude/agents/`
7. 最后才是 output styles 和更复杂的 hooks

对大多数团队来说，真正最关键的是这 4 个：

- `CLAUDE.md`
- `.claude/settings.json`
- `.claude/settings.local.json`
- `.mcp.json`

skills 和 agents 会让你更强，hooks 和 output styles 会让你更精细，但前面这 4 个才是项目级落地 Claude Code 的基础。

## 15. 官方参考

- [Claude Code settings](https://code.claude.com/docs/en/settings)
- [Manage Claude's memory](https://code.claude.com/docs/en/memory)
- [Create custom subagents](https://code.claude.com/docs/en/sub-agents)
- [Extend Claude with skills](https://code.claude.com/docs/en/skills)
- [Slash commands](https://code.claude.com/docs/en/slash-commands)
- [Connect Claude Code to tools via MCP](https://code.claude.com/docs/en/mcp)
- [Hooks reference](https://code.claude.com/docs/en/hooks)
- [Output styles](https://code.claude.com/docs/en/output-styles)
