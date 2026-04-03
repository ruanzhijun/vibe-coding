# GitHub Copilot 项目级配置清单

GitHub Copilot 的项目级配置不像 Codex、Claude Code 那样集中在一个专属目录里，而是分散在 `.github/` 和少量通用 agent 文件中。

本文重点整理：

- Copilot 项目里应该放哪些文件
- 每类文件分别负责什么
- 哪些是当前主线，哪些处于预览或附属地位

先给结论：当前最值得关注的是这几类文件。

```text
repo/
├─ AGENTS.md
├─ CLAUDE.md                     # 可选，供某些 agent 兼容
├─ GEMINI.md                     # 可选，供某些 agent 兼容
└─ .github/
   ├─ copilot-instructions.md
   ├─ instructions/
   │  ├─ frontend.instructions.md
   │  └─ backend.instructions.md
   └─ prompts/
      ├─ review.prompt.md
      └─ readme.prompt.md
```

其中主线是：

- `.github/copilot-instructions.md`
- `.github/instructions/*.instructions.md`
- `.github/prompts/*.prompt.md`
- `AGENTS.md`

## 1. `.github/copilot-instructions.md`

这是 Copilot 当前最标准的仓库级自定义说明文件。

### 1.1 它负责什么

适合写：

- 项目总规则
- 构建与测试命令
- 代码风格
- 团队约定
- 常见限制
- 评审偏好

### 1.2 适用范围

GitHub 官方当前文档把它定义为：

- repository-wide custom instructions
- 放在 `.github/copilot-instructions.md`
- 对仓库上下文内的 Copilot 请求生效

如果项目只准备一个 Copilot 文件，优先就是它。

## 2. `.github/instructions/*.instructions.md`

这是 Copilot 的路径级自定义说明文件体系。

### 2.1 它负责什么

适合做：

- 前后端分离规则
- 测试目录专属规则
- 文档目录专属规则
- monorepo 中 package 级说明

### 2.2 文件格式

官方要求：

- 文件放在 `.github/instructions/` 下
- 文件名必须以 `.instructions.md` 结尾
- 顶部 frontmatter 里写 `applyTo`

### 2.3 它和仓库级说明的关系

如果路径级说明命中，同时仓库级 `.github/copilot-instructions.md` 也存在，则两者都会生效。

## 3. `.github/prompts/*.prompt.md`

这是 Copilot 的 prompt files 机制。

### 3.1 它负责什么

这类文件不是“总是生效的规则”，而是：

- 可复用 prompt 模板
- 用于特定任务
- 更接近项目共享提示词资产

### 3.2 当前状态

官方当前说明明确提到：

- prompt files 仍处于 public preview

所以它很有价值，但不能把它当作最稳定的唯一配置入口。

## 4. `AGENTS.md`

GitHub Copilot 当前也支持通用 agent instructions。

官方当前文档明确说明：

- 你可以在仓库里放一个或多个 `AGENTS.md`
- Copilot 工作时会优先使用目录树里最近的 `AGENTS.md`

这使得 `AGENTS.md` 在 Copilot 里有两个明显优势：

- 可以按目录做就近覆盖
- 可以和其他 agent 工具共享

## 5. `CLAUDE.md` 和 `GEMINI.md`

GitHub 官方当前文档也明确提到：

- 如果不使用多层 `AGENTS.md`
- 也可以在仓库根放一个 `CLAUDE.md` 或 `GEMINI.md`

但从“跨工具可迁移”和“Copilot 原生主线”角度看，优先级依然建议是：

1. `AGENTS.md`
2. `.github/copilot-instructions.md`
3. `.github/instructions/*.instructions.md`
4. `.github/prompts/*.prompt.md`

## 6. 最推荐的职责分工

### 6.1 最小必备版

```text
repo/
└─ .github/
   └─ copilot-instructions.md
```

### 6.2 标准实用版

```text
repo/
├─ AGENTS.md
└─ .github/
   ├─ copilot-instructions.md
   └─ instructions/
      ├─ frontend.instructions.md
      └─ backend.instructions.md
```

### 6.3 进阶版

```text
repo/
├─ AGENTS.md
└─ .github/
   ├─ copilot-instructions.md
   ├─ instructions/
   │  ├─ frontend.instructions.md
   │  ├─ backend.instructions.md
   │  └─ docs.instructions.md
   └─ prompts/
      ├─ review.prompt.md
      ├─ tests.prompt.md
      └─ readme.prompt.md
```

## 7. 一句话理解每类文件

- `.github/copilot-instructions.md`：整个仓库的 Copilot 默认说明
- `.github/instructions/*.instructions.md`：按路径生效的说明文件
- `.github/prompts/*.prompt.md`：复用型 prompt 文件，偏任务模板
- `AGENTS.md`：通用 agent 指令文件，支持目录就近覆盖
- `CLAUDE.md` / `GEMINI.md`：单文件兼容替代方案

## 8. 推荐的落地顺序

1. 先写 `.github/copilot-instructions.md`
2. 再按目录拆 `.github/instructions/*.instructions.md`
3. 再补 `AGENTS.md`
4. 最后再把高频任务沉淀成 `.github/prompts/*.prompt.md`

## 9. 官方参考

- [Adding repository custom instructions for GitHub Copilot](https://docs.github.com/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot)
- [About customizing GitHub Copilot responses](https://docs.github.com/en/copilot/concepts/prompting/response-customization)
- [Adding instructions files to your repository](https://docs.github.com/copilot/customizing-copilot/adding-instructions-files-to-your-repository)
- [Prompt files](https://docs.github.com/en/copilot/tutorials/customization-library/prompt-files)
