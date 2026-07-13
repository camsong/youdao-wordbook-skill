# Youdao Wordbook Skill

[![skills.sh](https://skills.sh/b/camsong/youdao-wordbook-skill)](https://skills.sh/camsong/youdao-wordbook-skill)

An Agent Skill for exporting a logged-in NetEase Youdao wordbook into review-friendly JSONL, CSV, or Markdown.

The skill uses Youdao's read-only web wordbook list API and a local authenticated cookie supplied by the user. It does not add, delete, or modify words.

## Install

```bash
npx skills add camsong/youdao-wordbook-skill
```

After installation, ask your agent to use the `youdao-wordbook` skill to export your Youdao wordbook.

## What It Includes

- `SKILL.md` with the workflow and safety rules for agents.
- `scripts/export_youdao_wordbook.py`, a Python standard-library exporter.
- `references/api.md` with notes about the read-only Youdao wordbook API.
- `agents/openai.yaml` with Codex UI metadata.

## Authentication

The exporter needs a logged-in Youdao cookie. Provide it in one of these ways:

```bash
export YOUDAO_COOKIE='DICT_...=...; OUTFOX_SEARCH_USER_ID=...'
```

or pass a local cookie file:

```bash
python scripts/export_youdao_wordbook.py \
  --cookie-file "$HOME/Downloads/youdao-cookies.txt" \
  --format jsonl \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.jsonl"
```

Supported cookie inputs:

- Raw `Cookie` header string.
- Netscape/curl cookie jar.
- Playwright `storage_state.json`.

Do not commit cookies or exported private vocabulary data. This repository's `.gitignore` excludes common cookie, environment, and export filenames, but you should still review `git status` before committing.

## Usage

Export CSV:

```bash
python scripts/export_youdao_wordbook.py \
  --format csv \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.csv"
```

Export JSONL with raw audit data preserved:

```bash
python scripts/export_youdao_wordbook.py \
  --format jsonl \
  --output "$HOME/Documents/youdao-wordbook/youdao-wordbook.jsonl"
```

Export a Markdown review sheet:

```bash
python scripts/export_youdao_wordbook.py \
  --format md \
  --output "$HOME/Documents/youdao-wordbook/review.md"
```

## Output Fields

- `word`
- `translation`
- `item_id`
- `lan_from`
- `lan_to`
- `added_at`
- `added_at_source`
- `fetched_at`
- `raw` for JSONL, or `raw_json` for CSV/Markdown

## Security

- The script performs read-only `GET` requests.
- Cookies are supplied locally and are never required in repository files.
- The skill explicitly requires confirmation before using any mutating Youdao endpoint.
- If the Youdao web API changes, inspect `raw` / `raw_json` before relying on normalized fields.

## License

MIT
