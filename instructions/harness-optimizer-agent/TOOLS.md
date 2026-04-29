# TOOLS.md — Available Capabilities

## Paperclip API

Access via `skill: paperclip` or direct HTTP.

### Endpoints Used

- **GET /api/runs** — Fetch run history with filters (agent, status, date range, cost, retry count)
- **GET /api/runs/{runId}** — Full trace for single run (logs, tool calls, decisions, timings)
- **GET /api/runs/{runId}/analysis** — Pre-computed failure classification and metrics
- **GET /api/issues** — Fetch task history (assignments, blockers, time-to-completion)
- **PATCH /api/issues/{issueId}** — Post optimization report comments and recommendations
- **GET /api/traces** — Query full execution traces for deterministic rule extraction

### Rate Limits
- 60 requests/minute
- Cache results aggressively (24h TTL for stable data)

## obsidian-cli

Access via `skill: obsidian-cli` for knowledge artifact management.

### Commands Used

- **Create notes**: `obsidian create --vault [path] --title [name] --content [md]`
- **Link traces**: Embed references to Paperclip run IDs and trace snippets
- **Tag automation**: Automatically tag reports by failure category
- **Search**: Query historical reports by keyword or tag

### Integration Pattern
1. Generate markdown report locally
2. Post to Obsidian vault via CLI
3. Link back to Paperclip traces
4. Tag by failure category for discoverability

## para-memory-files

Access via `skill: para-memory-files` for PARA-based knowledge storage.

### Structure

```
/memory/
  /Projects/          # Active optimization initiatives
    /cost-reduction/     # Ongoing cost optimization effort
    /retry-patterns/     # Recurring failure analysis
  /Areas/             # Ongoing responsibilities
    /metrics/           # Cost, retry, ambiguity tracking
    /root-causes/       # Categorized failure repository
  /Resources/         # Reference material
    /templates/         # Report templates, rule schemas
    /traces/            # Historical trace analysis
  /Archive/           # Completed analyses
```

### Atomic Fact Schema

Each finding stored as:
```yaml
kind: failure_pattern
category: [ambiguity|cost|tool_misuse|context_missing|timeout]
occurrences: N
first_seen: [date]
last_seen: [date]
root_cause: [specific instruction gap]
recommended_rule: [deterministic fix]
traces: [list of Paperclip run IDs]
status: [proposed|approved|implemented|verified]
```

## Additional Capabilities

### LSP Integration
- Query agent instruction quality (type checking, completeness)
- Validate proposed rule syntax before implementation

### Git Integration
- Track changes to agent instructions over time
- Link optimization reports to rule implementation commits

### Slack/Team Notifications
- Post summaries of high-impact optimizations
- Alert on cost spikes or retry rate increases
