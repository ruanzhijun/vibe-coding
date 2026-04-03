
### Codex

- [Codex 项目级配置清单](./codex-project-config-guide.md)
  说明 `AGENTS.md`、`.codex/config.toml`、`.codex/rules/*.rules`、`.codex/hooks.json`、`.codex/agents/*.toml`、`.agents/skills/**/SKILL.md` 等项目级入口的职责与边界。

### Claude Code

- [Claude 项目级配置清单](./claude-project-config-guide.md)
  说明 `CLAUDE.md`、`.claude/settings.json`、`.claude/settings.local.json`、`.mcp.json`、`.claude/agents/*.md`、`.claude/skills/**/SKILL.md` 等项目级入口的职责与当前推荐关系。

### Cursor

- [Cursor 项目级配置清单](./cursor-project-config-guide.md)
  说明 `AGENTS.md`、`.cursor/rules/*.mdc`、`.cursorignore`、`.cursorindexingignore`、`.cursor/cli.json` 和 `.cursorrules` 各自的职责、当前推荐关系，以及 Cursor CLI 项目级权限配置的边界。

### Gemini CLI

- [Gemini CLI 项目级配置清单](./gemini-cli-project-config-guide.md)
  说明 `GEMINI.md`、`.gemini/settings.json`、`.geminiignore`、`.gemini/.env`、sandbox 文件和 `system.md` 等项目文件分别负责什么，并补充新版 schema 下的重要配置分类。

### GitHub Copilot

- [GitHub Copilot 项目级配置清单](./github-copilot-project-config-guide.md)
  说明 `.github/copilot-instructions.md`、`.github/instructions/*.instructions.md`、`.github/prompts/*.prompt.md`、`AGENTS.md` 等仓库内配置入口的职责与边界。

### Windsurf

- [Windsurf 项目级配置清单](./windsurf-project-config-guide.md)
  说明 `.windsurf/rules/*.md`、`AGENTS.md`、`.codeiumignore` 三类文件如何分工，以及哪些配置仍属于用户级 settings。

### Kiro

- [Kiro 项目级配置清单](./kiro-project-config-guide.md)
  说明 `.kiro/steering/*.md`、`AGENTS.md`、`.kiro/settings/mcp.json`、`.kiro/skills/*/SKILL.md`、`.kiro/prompts/*.md` 等项目文件的职责与优先级。

### Aider

- [Aider 项目级配置清单](./aider-project-config-guide.md)
  说明 `.aider.conf.yml`、`.aiderignore`、`.aider.model.settings.yml`、`.aider.model.metadata.json` 和 `.env` 在项目中的作用。

### Cline

- [Cline 项目级配置清单](./cline-project-config-guide.md)
  说明 `.clinerules/`、`.clineignore`、`.cline/skills/`、`.clinerules/workflows/`、`.clinerules/hooks/`、`AGENTS.md` 以及对 `.cursorrules`、`.windsurfrules` 的兼容关系。

### Roo Code

- [Roo Code 项目级配置清单](./roo-code-project-config-guide.md)
  说明 `AGENTS.md`、`.roomodes`、`.roorules`、`.roo/mcp.json` 等 Roo Code 项目文件的职责与安全边界。

### Continue

- [Continue 项目级配置清单](./continue-project-config-guide.md)
  说明 Continue 当前的项目本地 `rules / mcpServers / checks` 目录，以及为什么 `config.yaml` 仍是主配置、`.continuerc.json` 与 `config.json` 又为什么属于 legacy / deprecated。
