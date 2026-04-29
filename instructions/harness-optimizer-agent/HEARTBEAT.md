# HEARTBEAT.md — Harness Optimizer Execution Loop (PASSIVE)

## Overview

The Harness Optimizer Agent operates on a PASSIVE heartbeat cycle. You ONLY execute when explicitly triggered by a Paperclip assignment or wake event. You do NOT poll for work.

## Execution Cycle

1. **Fetch latest run data** — Collect traces, token usage, retry counts, failure patterns from Paperclip
2. **Analyze failure root causes** — Convert unstructured failures into deterministic rule categories
3. **Generate optimization report** — Document cost savings, retry reduction, ambiguity elimination recommendations
4. **Update PARA knowledge graph** — Store insights as atomic facts for future analysis
5. **Post findings to Obsidian** — Link reports to historical context for team visibility

## Wake Triggers

- **On-demand wake**: Triggered by Paperclip when new failure traces arrive
- **Cost spike detection**: Alert when token usage or retry count exceeds thresholds
- **Weekly synthesis**: Aggregate weekly patterns into deterministic improvement rules

## Report Generation

Output: `harness_optimization_report.md` (posted to team knowledge base)

**Report structure:**

- Summary metrics (cost delta, retry reduction, ambiguity patterns eliminated)
- Root cause analysis (top 5 failure categories with reproducible examples)
- Recommended rules (specific, actionable changes to reduce future failures)
- Evidence links (traces, harness component references, implementation guidance)

## Obsidian Integration

- Link each optimization report to relevant trace files
- Tag by failure category (e.g., `#ambiguity-handling`, `#cost-optimization`)
- Create decision nodes for team approval workflows

## Exit & Review

1. **PATCH own issue status** to `in_review`.
2. **Leave comment for `@Board`** with optimization summary and report link.
3. **Mention CEO** if strategic reallocation needed.
