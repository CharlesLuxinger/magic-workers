# HEARTBEAT.md - Execution Loop (PASSIVE)

## Heartbeat Cycle

The Implementation Agent operates on a PASSIVE heartbeat cycle. You ONLY execute when explicitly triggered by a Paperclip assignment or wake event. You do NOT poll for work.

Each heartbeat is a discrete execution window. The Implementation Agent wakes to:

1. Check assignment queue
2. Process assigned work (one issue at a time)
3. Execute per contract
4. Leave durable progress
5. Close heartbeat

## Execution Entry Point

**Wake Trigger:**

- Paperclip wake event (issue assigned, comment, time-based)
- Check `latest comment` in wake payload
- Verify issue status and assignee

**Checkout Protocol:**

- Harness provides checkout lock automatically
- Do NOT call `/api/issues/{id}/checkout` again
- Work on the issue within this run window

## Work Loop

```text
WHILE issue is actionable:
  1. Read latest comment + wake payload
  2. If board comment supersedes prior work: triage anew
  3. Execute contract or escalate
  4. Leave comment with progress
  5. If work continues next heartbeat: update issue status
  6. If work complete: mark "done" or "blocked"
```

## Progress Markers

**In Progress:**

- Issue status: `in_progress`
- Comment: Current action + next step
- Blocking state: None

**Blocked:**

- Issue status: `blocked`
- Comment: Root cause + unblock owner
- Wait for external action (board, upstream agent, etc.)

**Done:**

- Issue status: `done`
- Comment: Completion summary
- Leave no dangling work

## Comment Protocol

Every heartbeat must leave a comment explaining:

- What was done
- What changed
- What happens next
- Any blockers or decisions

Use markdown for clarity. Be concise.

## Exit Conditions

**Stop work when:**

- Issue marked `in_review` (after delegation)
- Issue marked `blocked` with clear unblock owner
- Board comment changes direction (treat as new input)
- Work completed to spec

**Delegation**:

- Once `VERIFY` phase is successful, create child issue for Test Verification Agent:
  - `POST /api/companies/{companyId}/issues`
  - Include `parentId`, `goalId`, `assigneeAgentId: "test-verification-agent"`
  - **REQUIRED**: Set `projectId: {same-as-parent}`.

**Exit**:

- PATCH own issue status to `in_review`
- Leave comment for `@Board` with implementation summary.
