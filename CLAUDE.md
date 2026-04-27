# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

```bash
pip install -r requirements.txt
streamlit run app.py
```

Credentials can be provided via either:
- `.streamlit/secrets.toml` (preferred for local dev): `META_ACCESS_TOKEN = "..."`
- `.env` file: `META_ACCESS_TOKEN=...`

The app falls back gracefully when `secrets.toml` is absent and reads from env vars instead.

## Architecture

This is a single-file Streamlit dashboard (`app.py`) for tracking Facebook/Meta ad campaign performance and Google Sheets form submissions for a beauty industry pre-course survey.

**Hard-coded identifiers** (in `app.py`):
- `CAMPAIGN_ID` — the Meta ad campaign to track
- `AD_ACCOUNT_ID` — Meta ads account
- Google Sheets CSV export URL — source for lead/form submission data

**Data flow:**
1. `fetch_meta_insights()` — calls Meta Graph API v19.0, fetches spend/clicks/impressions/CPC with pagination; cached 300s
2. `fetch_sheet_data()` — pulls a Google Sheets CSV export for form submission timestamps; cached 300s
3. Datasets are merged by date; derived metrics (CPL, cumulative spend/leads) are computed
4. UI renders KPI cards, a dual-axis Plotly trend chart (daily spend bars + cumulative cost-per-lead line), and a daily breakdown table with a totals row

**Sidebar controls:** date range picker and a Meta token management section (exchange short-term → 60-day long-term token via `exchange_long_term_token()`; inspect expiry via `inspect_token()`).

**Chinese date parsing:** `parse_tw_datetime()` handles Traditional Chinese AM/PM ("上午"/"下午") from the Google Sheet timestamps.

## Secrets Required

| Variable | Purpose |
|---|---|
| `META_ACCESS_TOKEN` | Required — Facebook Graph API token |
| `META_APP_ID` | Optional — only needed for token exchange |
| `META_APP_SECRET` | Optional — only needed for token exchange |
