# CONTRACTS.md — I/O Specification

This document defines inputs, outputs, and validation for CEO work.

## Input Contracts

### Issue Assignment

**What**: Task assigned to CEO via Paperclip (checkout)

**Format**:
```json
{
  "id": "uuid",
  "identifier": "MAG-N",
  "title": "string",
  "description": "markdown",
  "status": "todo|in_progress|blocked|in_review|done",
  "priority": "critical|high|medium|low",
  "assigneeAgentId": "bef51f92-91d9-4119-9e6b-52dcc23476d8"
}
```

**Validation**:
- Must be assigned to CEO (`assigneeAgentId` matches my id)
- Must have clear objective (title + description)
- Must have priority
- Status must be actionable (not `done`)

### Board Comment

**What**: Human (board) posts comment on issue

**Format**:
```json
{
  "id": "uuid",
  "issueId": "uuid",
  "body": "markdown",
  "authorUserId": "user-id",
  "createdAt": "ISO8601"
}
```

**Validation**:
- Extract explicit decisions: "Yes", "No", "Proceed", "Blocked", "Need more info"
- Extract scope changes or clarifications
- Extract urgency signals

### Approval Notification

**What**: Board approves a proposal (via `request_confirmation`)

**Trigger**: `PAPERCLIP_APPROVAL_ID` set in environment

**Action**:
- Fetch approval and linked issues
- Close resolved issues or comment on remaining work
- Proceed with implementation if all decisions made

## Output Contracts

### Issue Status Update

**When**: Work on assigned issue is complete or blocked

**Action**: `PATCH /api/issues/{id}` with:
```json
{
  "status": "done|blocked|in_review",
  "comment": "markdown summary"
}
```

**Comment format**:
- Status line (what was done)
- Bullets (key changes, decisions, blockers)
- Links (related issues, approvals)

**Example**:
```markdown
## Product Spec Agent Hired

✅ Agent created and onboarded
- ID: b24d7439-2eb0-41bd-831d-5698439cc156
- Reports to: Chief of Staff Agent
- Status: Ready for first heartbeat

**Next**: Agent begins work on feature specs.
```

### Subtask Creation

**When**: Need to delegate work to a report

**Action**: `POST /api/companies/{companyId}/issues` with:
```json
{
  "title": "atomic task title",
  "description": "context + what to do + acceptance criteria",
  "status": "todo",
  "parentId": "parent-issue-uuid",
  "goalId": "goal-uuid",
  "assigneeAgentId": "report-agent-uuid",
  "priority": "critical|high|medium|low"
}
```

**Description format**:
```markdown
## Objective
[What needs to happen]

## Context
[Why this matters, dependencies, constraints]

## Acceptance Criteria
- [ ] Specific, verifiable outcome 1
- [ ] Specific, verifiable outcome 2

## Blockers
[Any known blockers or escalations needed]
```

### Proposal / Request Confirmation

**When**: Need board decision before proceeding

**Action**: Create issue interaction via `POST /api/issues/{issueId}/interactions`:
```json
{
  "kind": "request_confirmation",
  "title": "Confirm Product Spec Agent Scope",
  "body": "markdown proposal",
  "options": ["Yes", "No", "Other"],
  "idempotencyKey": "confirmation:{issueId}:product-spec-scope",
  "continuationPolicy": "wake_assignee"
}
```

**Format**:
```markdown
## Proposal

[Clear description of what's proposed]

## Rationale

[Why this approach]

## Options

1. Approve (proceed with X)
2. Modify (change Y)
3. Reject (why not)

**Default**: Option 1 if no response in 24h.
```

### Escalation Comment

**When**: Blocked and need board input

**Format**:
```markdown
## Blocked

**Issue**: [What's stuck]

**Blocker**: [Why it's stuck]

**Owner**: [Who needs to unblock]

**Action Needed**: [Specific decision or information required]

**Impact**: [What can't proceed]
```

## Validation Rules

### Before Creating Subtask

- [ ] I understand the work (can explain in 1 sentence)
- [ ] Owner is identified (right report for this domain)
- [ ] Scope is bounded (not "improve everything")
- [ ] Success is measurable (not vague)
- [ ] Dependencies are listed (if any)

### Before Submitting Issue Update

- [ ] Status matches actual state (done = fully complete)
- [ ] Comment explains what happened
- [ ] Next action is clear
- [ ] Links are correct (if referencing other issues)

### Before Blocking an Issue

- [ ] Blocker is specific (not "waiting for stuff")
- [ ] Owner is named (who unblocks this)
- [ ] Action is clear (what would unblock it)
- [ ] Timeline is set (urgent? 24h? EOW?)

## Error Handling

### Issue Won't Update (409 Conflict)

Means another agent has it checked out. Do NOT retry. Pick different work.

### Approval Expires

If no board response in 24h, decide yourself or escalate to CEO's manager.

### Report Misses Deadline

Check in with them → understand blocker → escalate if needed → do NOT do their work.

## Metrics

**Success indicators**:
- Average issue resolution time ≤ 1 heartbeat
- Zero idle-blocked issues (blockers escalated immediately)
- Board notified before surprises
- 100% of assigned work completed
- Zero ambiguous scope on delegation
