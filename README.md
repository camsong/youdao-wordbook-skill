# Youdao Wordbook Skill / 有道单词本 Skill

[![skills.sh](https://skills.sh/b/camsong/youdao-wordbook-skill)](https://skills.sh/camsong/youdao-wordbook-skill/youdao-wordbook)

[中文](#中文) | [English](#english)

## 中文

一个用于导出网易有道单词本的 Agent Skill。它可以将已登录账号中的单词和释义导出为适合备份、分析与复习的 JSONL、CSV 或 Markdown 文件。

该 Skill 仅调用有道单词本的只读列表接口，并使用用户在本地提供的登录 Cookie。它不会添加、删除或修改单词。

### 安装

通过 [skills.sh](https://skills.sh/camsong/youdao-wordbook-skill/youdao-wordbook) 安装指定 Skill（推荐）：

```bash
npx skills add https://github.com/camsong/youdao-wordbook-skill --skill youdao-wordbook
```

也可以使用 GitHub 仓库简写：

```bash
npx skills add camsong/youdao-wordbook-skill
```

安装后，可以直接让 Agent 使用 `youdao-wordbook` Skill，例如：

> 帮我导出有道单词本，并生成一份 Markdown 复习清单。

### 包含内容

- `SKILL.md`：Agent 工作流程与安全规则。
- `scripts/export_youdao_wordbook.py`：仅使用 Python 标准库的导出脚本。
- `references/api.md`：有道单词本只读接口说明。
- `agents/openai.yaml`：Codex UI 元数据。

### 登录认证

导出工具需要已登录有道账号的 Cookie。推荐通过环境变量提供：

```bash
export YOUDAO_COOKIE='DICT_...=...; OUTFOX_SEARCH_USER_ID=...'
```

也可以读取本地 Cookie 文件：

```bash
python scripts/export_youdao_wordbook.py \
  --cookie-file "$HOME/Downloads/youdao-cookies.txt" \
  --format jsonl \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.jsonl"
```

支持以下 Cookie 格式：

- 原始 HTTP `Cookie` 请求头字符串。
- Netscape/curl cookie jar。
- Playwright `storage_state.json`。

不要提交 Cookie 或导出的私人词汇数据。仓库中的 `.gitignore` 已排除常见的 Cookie、环境变量和导出文件名，但提交前仍应检查 `git status`。

### 使用方法

导出 CSV：

```bash
python scripts/export_youdao_wordbook.py \
  --format csv \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.csv"
```

导出保留原始审计数据的 JSONL：

```bash
python scripts/export_youdao_wordbook.py \
  --format jsonl \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.jsonl"
```

导出 Markdown 复习清单：

```bash
python scripts/export_youdao_wordbook.py \
  --format md \
  --output "$HOME/Documents/youdao-wordbook/review.md"
```

### 输出字段

- `word`：单词或词组。
- `translation`：释义。
- `item_id`：有道单词本条目 ID。
- `lan_from` / `lan_to`：源语言和目标语言。
- `added_at` / `added_at_source`：添加时间及时间来源。
- `fetched_at`：数据获取时间。
- `raw`：JSONL 中的原始数据；CSV/Markdown 中对应 `raw_json`。

### 安全说明

- 脚本只执行只读 `GET` 请求。
- Cookie 只在本地提供，不需要写入仓库文件。
- 调用任何可能修改数据的有道接口前，Skill 都要求用户明确确认。
- 如果有道 Web API 发生变化，请先检查 `raw` / `raw_json`，再依赖标准化字段。

## English

An Agent Skill for exporting a logged-in NetEase Youdao wordbook into review-friendly JSONL, CSV, or Markdown files.

It uses Youdao's read-only wordbook list API with a locally supplied authentication cookie. It does not add, delete, or modify words.

### Install

Install the specific Skill through [skills.sh](https://skills.sh/camsong/youdao-wordbook-skill/youdao-wordbook) (recommended):

```bash
npx skills add https://github.com/camsong/youdao-wordbook-skill --skill youdao-wordbook
```

You can also use the GitHub repository shorthand:

```bash
npx skills add camsong/youdao-wordbook-skill
```

After installation, ask your agent to use the `youdao-wordbook` Skill, for example:

> Export my Youdao wordbook and create a Markdown review sheet.

### Authentication

Provide a logged-in Youdao cookie through the `YOUDAO_COOKIE` environment variable or a local cookie file. Raw HTTP Cookie strings, Netscape/curl cookie jars, and Playwright `storage_state.json` files are supported. See the commands in the Chinese section above.

Never commit cookies or exported private vocabulary data. The repository's `.gitignore` excludes common cookie, environment, and export filenames, but always review `git status` before committing.

### Output

The exporter supports JSONL, CSV, and Markdown. Normalized fields include `word`, `translation`, `item_id`, language direction, added time, and fetch time. JSONL preserves the source payload in `raw`; CSV and Markdown use `raw_json`.

### Security

- The script performs read-only `GET` requests.
- Cookies are supplied locally and are never required in repository files.
- The Skill requires explicit confirmation before using any mutating Youdao endpoint.
- If the Youdao Web API changes, inspect `raw` / `raw_json` before relying on normalized fields.

## License / 许可证

MIT
