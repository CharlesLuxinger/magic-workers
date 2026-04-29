# HEARTBEAT.md — Harness Optimizer Execution Loop

## Daily Sync Cycle

Every heartbeat, the Harness Optimizer agent:

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

## Metrics Tracked

- **Token consumption**: Baseline vs. optimized, per-agent and per-run
- **Retry count**: Failed attempts reduced per failure type
- **Ambiguity resolution**: Time to clarification, clarification rate reduction
- **Cost per task**: Average cost trend over time
