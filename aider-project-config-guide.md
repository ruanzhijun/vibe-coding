# Aider 项目级配置清单

Aider 的项目级配置风格和 Codex / Claude / Cursor 不一样。

它更像一个“命令行工具配置体系”，主入口是 YAML，而不是项目内 rules / agents / hooks 多目录体系。

先给结论：Aider 当前最值得关注的项目级文件是这几类。

```text
repo/
├─ .aider.conf.yml
├─ .aiderignore
├─ .aider.model.settings.yml
├─ .aider.model.metadata.json
└─ .env
```

## 1. `.aider.conf.yml`

这是 Aider 最核心的项目级主配置文件。

官方当前文档明确说明：

- Aider 会在 home 目录找
- 会在 git repo root 找
- 也会在当前目录找
- 后加载的优先级更高

所以把 `.aider.conf.yml` 放到仓库根，是项目级配置的主线方式。

### 1.1 它负责什么

官方当前文档里，这个文件几乎覆盖了 Aider 的绝大多数选项。

项目级最常见的包括：

- `model`
- API key / base URL 相关设置
- `git`
- `gitignore`
- `add-gitignore-files`
- `aiderignore`
- `subtree-only`
- `auto-commits`
- `dirty-commits`
- attribution 相关设置
- 输出样式
- 历史文件

## 2. `.aiderignore`

这是 Aider 的项目忽略文件。

官方文档虽然没有单独拉出完整页面，但 FAQ 和 options 文档都明确提到：

- 默认忽略文件名是 `.aiderignore`
- 语法遵循 `.gitignore`
- 可以用 `--aiderignore` 指定别的文件

### 2.1 它负责什么

适合放：

- monorepo 中当前不相关的包
- 大型构建产物
- 生成代码
- 数据文件
- vendor / third-party 目录

## 3. `.aider.model.settings.yml`

这是 Aider 的高级模型设置文件。

官方当前文档明确说明：

- 你可以通过它覆盖或补充任意模型的 settings
- 文件可以放在 home、git repo root 或当前目录
- 后加载的优先

### 3.1 它负责什么

适合处理：

- 某个模型的 `edit_format`
- `weak_model_name`
- `use_repo_map`
- `examples_as_sys_msg`
- `extra_params`
- `editor_model_name`
- `accepts_settings`

## 4. `.aider.model.metadata.json`

这是模型元数据覆盖文件。

官方当前文档明确说明：

- 它适用于 aider 不认识或元数据不完整的模型

### 4.1 它负责什么

适合写：

- `max_tokens`
- `max_input_tokens`
- `max_output_tokens`
- `input_cost_per_token`
- `output_cost_per_token`
- `litellm_provider`
- `mode`

## 5. `.env`

Aider 也支持通过 `.env` 文件管理环境变量。

项目里常见用途：

- OpenAI / Anthropic / OpenRouter / Azure 等 API keys
- Aider 相关环境变量
- 需要随项目环境切换的 provider 参数

## 6. 最推荐的职责分工

### 6.1 最小必备版

```text
repo/
├─ .aider.conf.yml
└─ .aiderignore
```

### 6.2 标准实用版

```text
repo/
├─ .aider.conf.yml
├─ .aiderignore
└─ .env
```

### 6.3 进阶版

```text
repo/
├─ .aider.conf.yml
├─ .aiderignore
├─ .aider.model.settings.yml
├─ .aider.model.metadata.json
└─ .env
```

## 7. 一句话理解每类文件

- `.aider.conf.yml`：Aider 项目级主配置
- `.aiderignore`：Aider 项目忽略规则
- `.aider.model.settings.yml`：高级模型行为覆盖
- `.aider.model.metadata.json`：模型元数据覆盖
- `.env`：项目环境变量

## 8. 推荐的落地顺序

1. 先写 `.aider.conf.yml`
2. 再补 `.aiderignore`
3. 需要项目环境变量时再补 `.env`
4. 最后再引入 model settings 和 metadata

## 9. 官方参考

- [YAML config file](https://aider.chat/docs/config/aider_conf.html)
- [Options reference](https://aider.chat/docs/config/options.html)
- [Config with .env](https://aider.chat/docs/config/dotenv.html)
- [Advanced model settings](https://aider.chat/docs/config/adv-model-settings.html)
- [Aider FAQ](https://aider.chat/docs/faq.html)
