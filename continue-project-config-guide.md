# Continue 项目级配置清单

Continue 要单独说明，因为它当前的官方配置主线已经明显转向：

- 用户级 `config.yaml`
- Hub / config / rules / prompts 的中心化管理

这意味着，如果你问“Continue 现在有哪些项目级配置文件”，正确答案不是“很多”，而是：

- 历史上有
- 现在主线并不强推
- 真正稳定的主配置入口主要是用户级，而不是仓库级

## 1. 当前结论先说

如果你的目标是像 Codex、Claude、Cursor 那样，把大部分 AI 行为直接跟着仓库一起提交，Continue 当前不是这条路线里最强的一类工具。

Continue 当前官方主线是：

- `~/.continue/config.yaml`

而不是项目根里的一堆配置文件。

## 2. 当前主线：`config.yaml`

Continue 当前官方文档明确说明：

- 本地主配置是 `config.yaml`
- 位置在用户目录的 `.continue/`
- 不是项目根

### 2.1 它负责什么

官方 YAML reference 当前能确认的能力包括：

- `models`
- `context`
- `rules`
- `prompts`
- `docs`
- `mcpServers`
- `data`

也就是说 Continue 的能力本身并不弱，只是当前主要通过用户级 YAML 来承载。

## 3. `config.json`

这是 Continue 的旧配置格式。

官方当前文档已经明确标记：

- `config.json` 是 deprecated
- 应迁移到 `config.yaml`

## 4. `.continuerc.json`

这才是 Continue 历史上的 workspace-level 配置文件。

官方当前文档仍然明确写着：

- `.continuerc.json` 是 workspace-level configuration
- 但它属于 legacy / deprecated 配置方法

### 4.1 它负责什么

历史上，它的作用是：

- 在仓库级覆盖 `config.json`
- 通过 `mergeBehavior` 控制 merge 或 overwrite

### 4.2 为什么不能把它当成当前主线

因为 Continue 官方当前文档已经非常明确：

- 它属于 deprecated configuration methods

## 5. `config.ts`

官方当前文档也还提到：

- `config.ts`

但它同样是用户目录下的高级扩展配置，不是标准项目级配置文件。

## 6. 如果你坚持要给 Continue 做项目级配置，当前该怎么理解

更准确的说法是：

### 6.1 当前官方支持最强的是用户级 YAML

```text
~/.continue/
└─ config.yaml
```

### 6.2 仓库级历史入口是 `.continuerc.json`

```text
repo/
└─ .continuerc.json
```

但它已经是 deprecated。

## 7. 那 Continue 现在项目里到底该不该放东西

如果你们团队主要用 Continue，但又希望把“项目约束”放进仓库，当前更实用的方式通常不是继续堆 Continue 专有 JSON，而是：

- 把通用规则写在 `AGENTS.md`
- 把项目文档、架构约定、测试约定写在仓库文档里
- 把 Continue 的模型、rules、prompts、MCP 主配置留在用户级 `config.yaml`

## 8. 一句话理解当前状态

- `config.yaml`：当前主线，但主要是用户级
- `config.json`：旧配置格式，已 deprecated
- `.continuerc.json`：历史上的 workspace 配置，已 deprecated
- `config.ts`：高级扩展配置，用户级

## 9. 对新项目的建议

如果你的项目要同时兼容多个 vibe coding 工具，而 Continue 只是其中之一，我建议：

1. 项目里先写 `AGENTS.md`
2. 需要 Copilot / Cursor / Claude / Codex 的专属配置时，再加各自文件
3. Continue 自身的主要模型与 rules / prompts / MCP 仍由用户级 `config.yaml` 管理
4. 不要再新建 `.continuerc.json` 作为长期主方案

## 10. 官方参考

- [How to Configure Continue](https://docs.continue.dev/customize/deep-dives/configuration)
- [config.yaml Reference](https://docs.continue.dev/yaml-reference)
- [config.json Reference (Deprecated)](https://docs.continue.dev/reference/json-reference)
- [Migrating Config to YAML](https://docs.continue.dev/customize/yaml-migration)
