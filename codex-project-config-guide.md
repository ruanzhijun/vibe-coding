# Codex 项目级配置清单

本文整理了一套适合项目级使用的 Codex 配置方案，重点回答 3 个问题：

- `.codex` 目录下应该放哪些文件
- 每个文件分别负责什么
- 各自可以配置哪些关键配置项

结论先说：如果你要把 Codex 当成项目级的 vibe coding 主工具，推荐至少准备以下结构。

```text
repo/
├─ AGENTS.md
├─ .codex/
│  ├─ config.toml
│  ├─ hooks.json
│  ├─ rules/
│  │  └─ default.rules
│  ├─ agents/
│  │  ├─ reviewer.toml
│  │  ├─ explorer.toml
│  │  └─ impl.toml
│  └─ hooks/
│     ├─ pre_tool_use.py
│     └─ post_tool_use.py
└─ .agents/
   └─ skills/
      └─ your-skill/
         └─ SKILL.md
```

其中真正属于 Codex 项目级配置的核心是：

- `.codex/config.toml`
- `.codex/hooks.json`
- `.codex/rules/*.rules`
- `.codex/agents/*.toml`

而这两个虽然不在 `.codex/` 下，但实际非常重要：

- `AGENTS.md`
- `.agents/skills/.../SKILL.md`

## 1. `.codex/config.toml`

这是项目级主配置文件，几乎所有运行时行为都从这里进入。项目级 `.codex/config.toml` 只有在项目被标记为 trusted 时才会加载，而且 Codex 会从仓库根一路向当前目录查找多层 `.codex/config.toml`，越近的优先级越高。

### 1.1 基础模型与推理

可配置项包括：

- `model`
- `model_provider`
- `model_reasoning_effort`
- `model_reasoning_summary`
- `review_model`
- `service_tier`
- `personality`
- `plan_mode_reasoning_effort`
- `model_context_window`
- `model_auto_compact_token_limit`
- `model_catalog_json`
- `model_instructions_file`

这一组主要决定：默认用哪个模型、推理深度有多高、评审时是否切换模型，以及是否加载额外模型说明文件。

### 1.2 权限与审批

可配置项包括：

- `approval_policy`
- `approval_policy.granular.sandbox_approval`
- `approval_policy.granular.rules`
- `approval_policy.granular.mcp_elicitations`
- `approval_policy.granular.request_permissions`
- `approval_policy.granular.skill_approval`

常见值包括：

- `untrusted`
- `on-request`
- `never`
- `granular = { ... }`

这一组决定 Codex 在什么情况下必须停下来问人。

### 1.3 沙箱与工作区权限

可配置项包括：

- `sandbox_mode`
- `sandbox_workspace_write.network_access`
- `sandbox_workspace_write.writable_roots`
- `sandbox_workspace_write.exclude_slash_tmp`
- `sandbox_workspace_write.exclude_tmpdir_env_var`

常见 `sandbox_mode` 包括：

- `read-only`
- `workspace-write`
- `danger-full-access`

这一组是硬权限边界，决定能不能写文件、能不能联网、能改哪些目录。

### 1.4 Shell 与环境变量策略

可配置项包括：

- `allow_login_shell`
- `shell_environment_policy.exclude`
- `shell_environment_policy.include_only`
- `shell_environment_policy.inherit`
- `shell_environment_policy.set`
- `shell_environment_policy.ignore_default_excludes`
- `shell_environment_policy.experimental_use_profile`

适合用于最小化环境暴露，比如只给 Codex 透出必要的 PATH、代理变量和少量令牌。

### 1.5 项目识别与项目级加载

可配置项包括：

- `project_root_markers`
- `projects.<path>.trust_level`
- `project_doc_fallback_filenames`
- `project_doc_max_bytes`

这组配置很关键，直接决定：

- 当前目录是不是一个项目根
- 项目是否被认定为 trusted
- 没有 `AGENTS.md` 时可以退回读哪些项目说明文件

### 1.6 MCP 服务器

可配置项包括：

- `mcp_oauth_callback_port`
- `mcp_oauth_callback_url`
- `mcp_oauth_credentials_store`
- `[mcp_servers.<id>]`
- `mcp_servers.<id>.command`
- `mcp_servers.<id>.args`
- `mcp_servers.<id>.env`
- `mcp_servers.<id>.env_vars`
- `mcp_servers.<id>.url`
- `mcp_servers.<id>.http_headers`
- `mcp_servers.<id>.env_http_headers`
- `mcp_servers.<id>.enabled`
- `mcp_servers.<id>.required`
- `mcp_servers.<id>.enabled_tools`
- `mcp_servers.<id>.disabled_tools`
- `mcp_servers.<id>.startup_timeout_sec`
- `mcp_servers.<id>.startup_timeout_ms`
- `mcp_servers.<id>.tool_timeout_sec`
- `mcp_servers.<id>.oauth_resource`
- `mcp_servers.<id>.scopes`

如果项目要接浏览器调试、设计稿、企业文档、内网服务，这组就是核心入口。

### 1.7 模型提供方与代理网关

可配置项包括：

- `openai_base_url`
- `[model_providers.<id>]`
- `model_providers.<id>.name`
- `model_providers.<id>.base_url`
- `model_providers.<id>.env_key`
- `model_providers.<id>.env_key_instructions`
- `model_providers.<id>.experimental_bearer_token`
- `model_providers.<id>.http_headers`
- `model_providers.<id>.env_http_headers`
- `model_providers.<id>.query_params`
- `model_providers.<id>.request_max_retries`
- `model_providers.<id>.stream_idle_timeout_ms`
- `model_providers.<id>.stream_max_retries`
- `model_providers.<id>.supports_websockets`
- `model_providers.<id>.wire_api`
- `model_providers.<id>.requires_openai_auth`

适合企业代理、私有网关、区域化出口等场景。

### 1.8 工具、搜索、技能、子代理

可配置项包括：

- `tools.view_image`
- `tools.web_search`
- `[agents]`
- `agents.max_threads`
- `agents.max_depth`
- `agents.job_max_runtime_seconds`
- `[[skills.config]]`
- `skills.config[].path`
- `skills.config[].enabled`

这一组控制：默认是否启用图像查看、web search、多子代理，以及项目级 skills 搜索路径。

### 1.9 功能开关

可配置项包括：

- `[features]`
- `features.codex_hooks`
- `features.multi_agent`
- `features.personality`
- `features.fast_mode`
- `features.unified_exec`
- `features.undo`
- `features.enable_request_compression`
- `features.prevent_idle_sleep`
- `features.apps`
- `features.shell_snapshot`
- `features.web_search`
- `features.web_search_cached`
- `features.web_search_request`

最常用的通常是：

- `features.codex_hooks = true`
- `features.multi_agent = true`
- `features.unified_exec = true`

### 1.10 历史、日志、UI、系统

可配置项包括：

- `history.persistence`
- `history.max_bytes`
- `log_dir`
- `notify`
- `file_opener`
- `hide_agent_reasoning`
- `show_raw_agent_reasoning`
- `tool_output_token_limit`
- `sqlite_home`
- `tui.alternate_screen`
- `tui.animations`
- `tui.notifications`
- `tui.notification_method`
- `tui.show_tooltips`
- `tui.status_line`
- `tui.theme`
- `windows.sandbox`
- `windows.sandbox_private_desktop`

这一组主要解决使用体验和平台适配问题，Windows 本机环境要重点关注。

## 2. `.codex/rules/*.rules`

这是命令级权限策略文件，不是 TOML，而是 `.rules` 文件，语言是 Starlark。主要用于“命令前缀匹配 + 决策”。

核心规则是 `prefix_rule()`，常见字段包括：

- `pattern`
- `decision`
- `justification`
- `match`
- `not_match`

`decision` 目前常用值：

- `allow`
- `prompt`
- `forbidden`

典型用途：

- `git push` 一律 `prompt`
- `rm` 一律 `forbidden`
- `go test` 直接 `allow`
- `curl` 和 `wget` 一律 `prompt`

推荐的最小目录结构：

```text
.codex/rules/
├─ default.rules
├─ destructive.rules
└─ network.rules
```

## 3. `.codex/hooks.json`

这是生命周期钩子配置文件，不是必须，但如果要把 Codex 做成“半自动开发系统”，很有价值。

常见事件包括：

- `SessionStart`
- `UserPromptSubmit`
- `PreToolUse`
- `PostToolUse`
- `Stop`

常见字段包括：

- `matcher`
- `hooks`
- `type = "command"`
- `command`
- `timeout`
- `timeoutSec`
- `statusMessage`

hook 返回里常见的控制字段包括：

- `continue`
- `stopReason`
- `systemMessage`
- `suppressOutput`

注意事项：

- 需要在 `config.toml` 里启用 `features.codex_hooks = true`
- 目前更适合做 guardrail，而不是绝对安全边界
- 常见用途是阻止危险命令、结束时自动跑检查、启动时注入上下文

## 4. `.codex/hooks/`

这个目录本身不是自动加载入口，但非常适合作为 `hooks.json` 调用脚本的存放位置。

推荐放：

```text
.codex/hooks/
├─ session_start.py
├─ pre_tool_use.py
├─ post_tool_use.py
└─ stop.py
```

常见用途：

- 启动时打印项目提示
- 拦截高风险 Bash
- 工具执行后自动记录日志
- 结束时自动执行格式化、lint 或测试

## 5. `.codex/agents/*.toml`

这是项目级自定义子代理配置。

推荐目录：

```text
.codex/agents/
├─ explorer.toml
├─ reviewer.toml
├─ impl.toml
└─ docs.toml
```

每个 agent 文件至少需要：

- `name`
- `description`
- `developer_instructions`

可选：

- `nickname_candidates`

并且还可以额外放很多 `config.toml` 兼容项，例如：

- `model`
- `model_reasoning_effort`
- `sandbox_mode`
- `mcp_servers`
- `skills.config`

典型分工：

- `explorer`：只读查代码
- `reviewer`：做代码评审
- `impl`：实现功能
- `docs`：只看官方文档或 MCP 文档源

## 6. `AGENTS.md`

虽然它不在 `.codex/` 下，但它是项目级使用 Codex 时最重要的仓库说明文件之一。

适合写入：

- 项目结构
- 正确构建命令
- 正确测试命令
- 哪些目录不要动
- 哪些配置文件默认不要读
- 代码风格
- 提交要求

对多数项目来说，`AGENTS.md` 是“项目行为规范”，而不是“硬权限边界”。

## 7. `.agents/skills/.../SKILL.md`

项目级 skills 不建议放在 `.codex/skills`，而是放在：

- `$CWD/.agents/skills`
- `$REPO_ROOT/.agents/skills`

每个 skill 目录至少有一个：

- `SKILL.md`

推荐结构：

```text
.agents/skills/
├─ repo-onboarding/
│  └─ SKILL.md
├─ test-runner/
│  └─ SKILL.md
└─ release-check/
   └─ SKILL.md
```

适合沉淀稳定工作流，例如：

- 新仓库快速上手
- 测试执行规范
- 发版前检查
- 前端截图还原
- 数据库迁移助手

## 8. `requirements.toml`

这个文件通常不是普通项目必备项，而是偏企业或受管环境的强约束配置。它不一定放在项目里，也不属于常规项目级自定义配置的第一优先级。

它主要适合限制：

- `allowed_approval_policies`
- `allowed_sandbox_modes`
- `allowed_web_search_modes`
- `[features]`
- `mcp_servers`
- `rules.prefix_rules[]`

如果是个人或普通团队刚开始做 vibe coding，一开始通常不需要引入它。

## 9. 推荐的项目级配置方案

### 9.1 最少必备版

如果想尽快开始，至少准备：

```text
repo/
├─ AGENTS.md
└─ .codex/
   ├─ config.toml
   └─ rules/
      └─ default.rules
```

### 9.2 标准实用版

如果想把项目真正跑顺，推荐：

```text
repo/
├─ AGENTS.md
├─ .codex/
│  ├─ config.toml
│  ├─ hooks.json
│  ├─ rules/
│  │  ├─ default.rules
│  │  ├─ destructive.rules
│  │  └─ network.rules
│  ├─ agents/
│  │  ├─ explorer.toml
│  │  ├─ reviewer.toml
│  │  └─ impl.toml
│  └─ hooks/
│     ├─ pre_tool_use.py
│     └─ stop.py
└─ .agents/
   └─ skills/
      ├─ repo-onboarding/
      │  └─ SKILL.md
      └─ test-runner/
         └─ SKILL.md
```

### 9.3 团队增强版

多人协作时再补：

- 更多 `.codex/agents/*.toml`
- 更细的 `.codex/rules/*.rules`
- 更完整的 hooks
- 企业侧 `requirements.toml`
- MCP 服务配置

## 10. 一句话理解每类文件

- `.codex/config.toml`：主配置，控制模型、审批、沙箱、MCP、功能开关
- `.codex/rules/*.rules`：命令级权限规则
- `.codex/hooks.json`：生命周期钩子编排
- `.codex/hooks/*`：hook 实际执行脚本
- `.codex/agents/*.toml`：项目级子代理定义
- `AGENTS.md`：仓库行为规范和项目说明
- `.agents/skills/.../SKILL.md`：可复用工作流技能
- `requirements.toml`：偏企业治理的强约束配置

## 11. 推荐的落地顺序

如果你现在开始转 vibe coding，建议按这个顺序落地：

1. 先写 `AGENTS.md`
2. 再写 `.codex/config.toml`
3. 再写 `.codex/rules/default.rules`
4. 然后补 `.codex/agents/*.toml`
5. 稳定后再加 `hooks.json`
6. 最后把稳定流程沉淀进 `.agents/skills/`

## 12. 官方参考

官方文档可对照：

- [Config basics](https://developers.openai.com/codex/config-basic)
- [Advanced config](https://developers.openai.com/codex/config-advanced)
- [Config reference](https://developers.openai.com/codex/config-reference)
- [Config schema](https://developers.openai.com/codex/config-schema.json)
- [Rules](https://developers.openai.com/codex/rules)
- [Hooks](https://developers.openai.com/codex/hooks)
- [Subagents](https://developers.openai.com/codex/subagents)
- [MCP](https://developers.openai.com/codex/mcp)
- [Skills](https://developers.openai.com/codex/skills)
- [Introducing Codex](https://openai.com/index/introducing-codex/)

## 13. 最后的建议

对项目来说，最关键的不是把 `.codex` 配到最复杂，而是先把下面三样配稳：

- `AGENTS.md`
- `.codex/config.toml`
- `.codex/rules/default.rules`

这三样稳定后，再逐步补 hooks、skills、subagents，Codex 才会真正从“能用”进入“高效可控”。

