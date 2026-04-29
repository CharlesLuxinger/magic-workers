# CONTRACTS.md — I/O Specification

## Input Contract

The Harness Optimizer receives:

### From Paperclip API
- **Run traces**: Full execution logs, tool calls, agent decisions, timings
- **Retry records**: Attempts that failed and were retried, with error messages
- **Token consumption**: Per-run, per-agent, per-tool breakdown
- **Failure classification**: Categorized errors (ambiguity, tool misuse, auth, timeout, etc.)

### From Review Outputs
- **Code review feedback**: Patterns in what reviewers flag (style, logic, safety)
- **QA results**: Test failures, coverage gaps, edge cases missed
- **User feedback**: Comments on ambiguity, unclear behavior, missing guidance

### From Task Status
- **Blocked issues**: Unblocked owner, reason, time waiting
- **Delegated work**: Assignment history, escalations, handoff quality
- **Completion rate**: Tasks completed vs. time spent

## Output Contract

The Harness Optimizer produces:

### Primary Artifact: harness_optimization_report.md
- **Location**: Posted to team knowledge base (Obsidian, shared drive)
- **Format**: Markdown with structured sections (see template below)
- **Frequency**: Weekly, or on-demand when thresholds exceeded
- **Retention**: Linked and indexed for historical analysis

### Report Template

```markdown
# Harness Optimization Report — [Date]

## Summary Metrics
- Cost delta: $X saved (Y% reduction vs. baseline)
- Retry reduction: Z% fewer retries per task type
- Ambiguity clarifications: A fewer per run
- Time to resolution: B% faster on average

## Top 5 Failure Categories

### 1. [Category Name] — X% of failures
**Example trace**: [Link to specific failure]
**Root cause**: [Specific decision/instruction gap]
**Recommended fix**: [Deterministic rule change]
**Implementation effort**: Low/Medium/High
**Expected impact**: Y% reduction in this failure type

[Repeat for categories 2-5]

## Proposed Rule Changes

### Rule: [Clear, actionable statement]
**Applies to**: [Agent(s), workflow(s)]
**Reason**: [Trace evidence and reasoning]
**Implementation**: [Specific change to AGENTS.md or tool behavior]
**Validation**: [How to verify it works]

## Historical Context

**Traces analyzed**: [N runs, date range]
**Baseline metrics**: [Previous week's costs, retry rates]
**Trend**: [Improving/Stable/Degrading]

## Next Steps

1. Review and approve proposed rule changes
2. Implement in target agent instructions
3. Monitor impact metrics over next 1-2 weeks
4. Iterate based on results
```

## Validation Rules

- Every recommendation must cite at least 2 trace examples
- Root cause analysis must be falsifiable (testable)
- Proposed rules must be specific enough to implement
- Links to harness components and traces must be alive (verifiable)

## Integration Feedback Loop

1. Harness Optimizer generates report
2. CEO/team reviews and approves
3. Approved rules are implemented
4. Next cycle measures impact
5. Patterns feed back into training data
