# Drupal AI Classifier

Scripts that keep a Drupal software inventory clean and tag each product with whether it has AI features.

The main programs live in `sandbox/infosec-ai-scanner/src/`:

| Script | What it does |
|---|---|
| `drupal_importer.py` | Reads a software inventory spreadsheet, merges duplicate vendor/product rows, and POSTs one Drupal node per product via JSON:API. |
| `drupal_ai_scanner.py` | Fetches Drupal nodes, asks OpenAI / Azure OpenAI whether each product has AI features, and PATCHes the result back. Incremental: only new or changed nodes are rescanned. Emails a review list for flagged items. |
| `drupal_merge_duplicates.py` | Finds duplicate nodes, merges them into the lowest node ID, deletes the rest. Supports `--dry-run`, `--diff`, `--force-merge`. |
| `normalize_smart_quotes.py` | Replaces Unicode quotes in text fields with ASCII. `--apply` to write. |
| `normalize_title_dashes.py` | Collapses `--` in node titles to `-`. `--apply` to write. |
| `backfill.py` | Recomputes content hashes for already-scanned nodes. |

`sandbox/sbin/` has cron wrappers for running the scanner on a server.

## Setup

```bash
cd sandbox/infosec-ai-scanner
uv sync
cp ../../.env.example .env   # fill in Drupal and OpenAI credentials
```

Set `SITE_COLUMNS` in `.env` to the spreadsheet column headers that mark site usage, comma-separated, in sheet order:

```env
SITE_COLUMNS=SITE_A,SITE_B,SITE_C
```

## Usage

```bash
cd sandbox/infosec-ai-scanner/src
python drupal_importer.py --dry-run --spreadsheet /path/to/inventory.xlsx
python drupal_ai_scanner.py
python drupal_merge_duplicates.py --dry-run
```

Every script that writes to Drupal has a dry-run or preview mode. Use it first.
