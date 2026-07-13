---
name: youdao-wordbook
description: 获取网易有道单词本词句列表并导出复习数据；当用户要同步、备份、分析、复习有道单词本，或需要记录单词、释义、语言方向、添加时间、itemId 等字段时使用。
---

# Youdao Wordbook

## Overview

Use this skill to export the user's Youdao wordbook from the logged-in web API and turn it into review-friendly JSONL, CSV, or Markdown.

The web page is `https://www.youdao.com/webwordbook/wordlist`; the current list API is documented in `references/api.md`.

## Workflow

1. Confirm where the user wants the exported vocabulary saved. If unspecified, write outside any git repo, for example `~/Documents/youdao-wordbook/`.
2. Obtain a logged-in Youdao cookie by one of these methods:
   - User provides a Cookie header string.
   - User sets `YOUDAO_COOKIE`.
   - User exports browser cookies to a Netscape/curl cookie jar or Playwright `storage_state.json`.
3. Run `scripts/export_youdao_wordbook.py` with the cookie source, desired format, and output path.
4. Check the summary count and inspect the first few rows. If `added_at` is empty, inspect each row's `raw` / `raw_json` because Youdao may rename or omit the add-time field.

## Quick Commands

Cookie string:

```bash
YOUDAO_COOKIE='DICT_...=...; OUTFOX_SEARCH_USER_ID=...' \
python scripts/export_youdao_wordbook.py \
  --format csv \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.csv"
```

Cookie file:

```bash
python scripts/export_youdao_wordbook.py \
  --cookie-file "$HOME/Downloads/youdao-cookies.txt" \
  --format jsonl \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.jsonl"
```

Markdown review sheet:

```bash
python scripts/export_youdao_wordbook.py \
  --cookie-file "$HOME/Downloads/youdao-storage-state.json" \
  --format md \
  --output "$HOME/Documents/youdao-wordbook/review.md"
```

## Output Fields

The script emits normalized fields:

- `word`
- `translation`
- `item_id`
- `lan_from`
- `lan_to`
- `added_at`
- `added_at_source`
- `fetched_at`
- `raw` for JSONL, or `raw_json` for CSV/Markdown

Keep `raw` when the user cares about auditability or future migrations.

## Safety Notes

- Do not commit exported wordbook data or cookies.
- Do not print full cookies in the final answer.
- The export script only performs read-only GET requests.
- Add/delete endpoints exist, but they mutate the user's Youdao account. Only call them after explicit confirmation.
