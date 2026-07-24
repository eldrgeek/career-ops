# Mode: signal-scan — Layoff Signal Scanner

Scan public sources for companies experiencing layoffs, restructuring, or workforce reductions that make them potential buyers of INTOO outplacement services.

## Prerequisites

Before scanning, read:
1. `trigger-sources.yml` — scan configuration and source list
2. `data/signal-history.tsv` — previously seen signals (for dedup)
3. `data/prospect-pipeline.md` — existing prospects (for dedup)
4. `intoo-profile.yml` — ICP filter criteria

## Scan Procedure

### Step 1 — Execute Searches

For each **enabled** source in `trigger-sources.yml`:
1. Run WebSearch with the configured `query`
2. Extract from each result:
   - **Company name** — the company doing the layoff
   - **Approximate employee count** — total company size if available
   - **Layoff scale** — number of employees affected (or percentage)
   - **Announcement date** — when the news broke
   - **News URL** — source link
   - **Brief description** — 1-line summary of what happened

Process sources in priority order: `high` first, then `medium`, then `low`.

### Step 2 — Deduplicate

For each extracted signal, check:
1. Is the URL already in `data/signal-history.tsv`? → Skip (exact duplicate)
2. Is the company already in `data/prospect-pipeline.md`? → Skip unless new/larger layoff event
3. Is the company+date combination already seen? → Skip

### Step 3 — ICP Filter

Apply `icp_filter` from `trigger-sources.yml`:
- **min_employees:** Skip companies below this threshold (if employee count is known)
- **exclude_patterns:** Skip if company description matches any pattern (e.g., "seed stage", "non-profit")
- **geography_primary:** Prioritize but don't hard-filter by geography
- **priority_verticals:** Tag the vertical for scoring; non-priority verticals still pass

If employee count is unknown, DO NOT skip — mark as "unverified" and let qualify mode resolve it.

### Step 4 — Record Results

For each **new** signal that passes filters:

1. **Add to prospect-pipeline.md:**
   ```
   | {next_num} | {signal_date} | {company} | {scale} | {vertical} | —/5 | Signal | — | — | {brief_note} |
   ```

2. **Append to signal-history.tsv:**
   ```
   {url}\t{signal_date}\t{source_name}\t{company}\t{scale}\t added
   ```

For each **skipped** signal (duplicate or below ICP):
- Append to signal-history.tsv with status `skipped` and reason

### Step 5 — Summary Report

Print a summary table:

```
Signal Scan Complete — {date}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Sources scanned:  {N}
Signals found:    {N total}
  New:            {N added to pipeline}
  Duplicate:      {N already seen}
  Below ICP:      {N filtered out}

New Signals:
| Company | Scale | Date | Source | Vertical |
|---------|-------|------|--------|----------|
| ...     | ...   | ...  | ...    | ...      |

Next step: Run `/intoo-leadgen qualify {company}` for top prospects.
```

## Rules

- NEVER fabricate signals. Every signal must come from a WebSearch result with a real URL.
- NEVER add a company to the pipeline without a source URL.
- If WebSearch returns no results for a source, log it and move on — don't retry with modified queries.
- If a company appears in multiple sources, use the earliest date and note "multiple sources" in notes.
- Rate limit: pause briefly between WebSearch calls to avoid throttling.
