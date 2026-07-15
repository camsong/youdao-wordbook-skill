# Youdao Wordbook Skill / 有道单词本 Skill

[![skills.sh](https://skills.sh/b/camsong/youdao-wordbook-skill)](https://skills.sh/camsong/youdao-wordbook-skill/youdao-wordbook)

[中文](#中文) | [English](#english)

## 中文

一个用于导出网易有道单词本的 Agent Skill。它可以将已登录账号中的单词和释义导出为适合备份、分析与复习的 JSONL、CSV 或 Markdown 文件。

该 Skill 仅调用有道单词本的只读列表接口，并使用用户在本地提供的登录 Cookie。它不会添加、删除或修改单词。

### 安装

通过 [skills.sh](https://skills.sh/camsong/youdao-wordbook-skill/youdao-wordbook) 安装：

```bash
npx skills add camsong/youdao-wordbook-skill
```

安装后，可以直接让 Agent 使用 `youdao-wordbook` Skill，例如：

> 帮我导出有道单词本，并生成一份 Markdown 复习清单。

### 包含内容

- `skills/youdao-wordbook/SKILL.md`：Agent 工作流程与安全规则。
- `skills/youdao-wordbook/scripts/export_youdao_wordbook.py`：仅使用 Python 标准库的导出脚本。
- `skills/youdao-wordbook/references/api.md`：有道单词本只读接口说明。
- `skills/youdao-wordbook/agents/openai.yaml`：Codex UI 元数据。

### 登录认证

导出工具需要已登录有道账号的 Cookie。**首次使用**时，如果没有可用 Cookie，脚本会以退出码 `2` 打印从浏览器获取 Cookie 的步骤；提供一次有效 Cookie 后，它会被校验并缓存到 skill 目录下的 `.youdao_cookie.json`（权限 `0600`，已被 `.gitignore` 排除），之后运行**无需再输入 Cookie**。当缓存的 Cookie 过期时，脚本会以退出码 `3` 提示，并自动清除失效缓存，此时重新提供一次新的 Cookie 即可。

退出码约定：

| 退出码 | 含义 |
|--------|------|
| `0` | 成功；新提供的 Cookie 已校验并缓存 |
| `2` | 无可用 Cookie（首次使用）——需要从浏览器获取 |
| `3` | Cookie 无效或过期——需要提供新的 Cookie |
| `1` | 其他错误 |

从浏览器获取 Cookie：打开 `https://www.youdao.com/webwordbook/wordlist`（确保已登录）→ DevTools（F12）→ Network → 刷新 → 点击任意 `dict.youdao.com` 请求 → 复制 Request Headers 中完整的 Cookie 值（须包含 `DICT_SESS` 与 `DICT_LOGIN`）。

首次提供 Cookie（只校验并缓存，不导出）：

```bash
python skills/youdao-wordbook/scripts/export_youdao_wordbook.py \
  --check --cookie 'DICT_SESS=...; DICT_LOGIN=...'
```

Cookie 也可以通过环境变量提供：

```bash
export YOUDAO_COOKIE='DICT_...=...; OUTFOX_SEARCH_USER_ID=...'
```

也可以读取本地 Cookie 文件：

```bash
python skills/youdao-wordbook/scripts/export_youdao_wordbook.py \
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
python skills/youdao-wordbook/scripts/export_youdao_wordbook.py \
  --format csv \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.csv"
```

导出保留原始审计数据的 JSONL：

```bash
python skills/youdao-wordbook/scripts/export_youdao_wordbook.py \
  --format jsonl \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.jsonl"
```

导出 Markdown 复习清单：

```bash
python skills/youdao-wordbook/scripts/export_youdao_wordbook.py \
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

Install through [skills.sh](https://skills.sh/camsong/youdao-wordbook-skill/youdao-wordbook):

```bash
npx skills add camsong/youdao-wordbook-skill
```

After installation, ask your agent to use the `youdao-wordbook` Skill, for example:

> Export my Youdao wordbook and create a Markdown review sheet.

### Authentication

On **first use**, if no cookie is available the script exits with code `2` and prints browser steps for fetching one. After you supply a valid cookie once, it is validated and cached under the skill at `.youdao_cookie.json` (`0600`, git-ignored), so later runs need **no cookie input**. When the cached cookie expires, the script exits with code `3`, clears the stale cache, and you supply a fresh cookie once more.

Exit codes: `0` success (fresh cookie validated + cached), `2` no cookie (fetch from browser), `3` cookie invalid/expired (supply a fresh one), `1` other error.

Fetch a cookie from a logged-in browser: open `https://www.youdao.com/webwordbook/wordlist`, DevTools → Network → refresh → click a `dict.youdao.com` request → copy the full Request Headers → Cookie value (must contain `DICT_SESS` and `DICT_LOGIN`). Provide it once via `--cookie`, the `YOUDAO_COOKIE` environment variable, or a local cookie file (raw HTTP Cookie strings, Netscape/curl cookie jars, and Playwright `storage_state.json` files are supported).

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
