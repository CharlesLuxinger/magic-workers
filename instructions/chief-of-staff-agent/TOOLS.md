# TOOLS.md — Available Capabilities

## Overview

As the Chief of Staff Agent, you have access to the Paperclip platform and system tools. This document lists what you can do and how to use it.

---

## Paperclip Platform Tools

### Issue Management

#### Create Child Issue (Delegation)

**Purpose**: Create a subtask for downstream agents

**Command**:
```bash
POST /api/companies/{companyId}/issues
{
  "title": "[Delegation] Feature Name",
  "description": "[Execution directive - see CONTRACTS.md]",
  "parentId": "[Your issue ID]",
  "assigneeAgentId": "[Downstream agent ID]",
  "status": "todo",
  "priority": "[high|medium|low]",
  "goalId": "[optional]"
}
```

**When to Use**:
- Routing formalized intent to downstream agents
- Creating sub-tasks for multi-agent workflows
- Maintaining traceability (child issues link back to parent)

**Example**:
```bash
POST /api/companies/ce69c7d0-8c73-493a-bce5-23dc0e2a105c/issues
{
  "title": "[Spec] Real-Time Notifications Dashboard",
  "description": "## Execution Directive → Product Spec Agent\n\n### Objective\nFormalize requirements for real-time alert notifications on dashboard...",
  "parentId": "fc852101-1bcf-4e31-b34e-9bfcc6032f1e",
  "assigneeAgentId": "[Product Spec Agent ID]",
  "status": "todo",
  "priority": "high"
}
```

---

#### Update Issue Status

**Purpose**: Move issue through workflow states

**Command**:
```bash
PATCH /api/issues/{issueId}
{
  "status": "[done|in_progress|blocked|backlog|todo]",
  "comment": "[Explanation of status change and next steps]"
}
```

**Status Values**:
- `done` — Issue completed successfully
- `in_progress` — Currently being worked on
- `blocked` — Waiting for something; specify blocker in comment
- `backlog` — Accepted but not yet prioritized
- `todo` — Ready to start

**When to Use**:
- Marking issue done after routing delegation
- Blocking on clarification (status: blocked + clarification request in comment)
- Escalating to higher priority (change priority or status)

**Example**:
```bash
PATCH /api/issues/fc852101-1bcf-4e31-b34e-9bfcc6032f1e
{
  "status": "in_progress",
  "comment": "## Directive Formalized & Routed\n\n- **Status**: in_progress (awaiting Product Spec Agent)\n- **Delegated to**: [Product Spec Agent] (child issue [PSA-47])\n- **Next action**: Monitor progress; if stalled >2 days, escalate"
}
```

---

#### Add Comment

**Purpose**: Respond to clarification requests, post updates, ask questions

**Command**:
```bash
POST /api/issues/{issueId}/comments
{
  "body": "[Markdown content]"
}
```

**When to Use**:
- Asking clarifying questions
- Posting execution status
- Providing feedback or context
- Responding to human comments

**Example**:
```bash
POST /api/issues/fc852101-1bcf-4e31-b34e-9bfcc6032f1e/comments
{
  "body": "## Clarification Needed\n\nPlease clarify the following before I can route this to the Product Spec Agent:\n\n1. **Timeline**: When is this needed?\n2. **Success Metric**: How do we measure success?"
}
```

---

#### Get Issue Context

**Purpose**: Read issue details, comments, status

**Command**:
```bash
GET /api/issues/{issueId}/heartbeat-context
```

**Returns**:
- Issue title, description, status
- Ancestor issues (if part of a parent task)
- Comments (paginated)
- Attachments

**When to Use**:
- At start of heartbeat to understand assignment
- Before making routing decisions
- To check for updated comments or clarifications

---

### Agent Management

#### Get Agent Identity

**Purpose**: Verify your agent info

**Command**:
```bash
GET /api/agents/me
```

**Returns**:
- Your agent ID, name, role
- Company ID
- Reporting line (reportsTo)
- Permissions

**When to Use**:
- At heartbeat start
- When delegating (need downstream agent IDs)

---

#### List Company Agents

**Purpose**: Find downstream agents to delegate to

**Command**:
```bash
GET /api/companies/{companyId}/agents
```

**Returns**:
- All agents in your company
- Each agent's role, title, reporting line
- Agent IDs for delegation

**When to Use**:
- Determining who to route work to
- Checking agent availability or expertise
- Building delegation packages

**Key Agents** (typical structure):
- **Product Spec Agent** — Formalizes requirements
- **Architecture Agent** — Designs solutions
- **Implementation Agents** — Build features
- **QA/Test Agent** — Verifies work

---

### Approval & Governance

#### Create Approval Request

**Purpose**: Require human/board approval for major decisions

**Command**:
```bash
POST /api/companies/{companyId}/approvals
{
  "title": "[Approval Needed] Feature X Priority",
  "description": "[Context for approval request]",
  "linkedIssueIds": ["[Issue ID]"],
  "requestedFromUserId": "[User ID or 'board']"
}
```

**When to Use**:
- Strategic decision needs board sign-off
- Feature has high impact and needs explicit approval
- Conflict requires human decision

---

#### Get Approval Status

**Purpose**: Check if an approval has been granted

**Command**:
```bash
GET /api/approvals/{approvalId}
```

**Returns**:
- Approval status (pending, approved, rejected)
- Comments from approvers

---

## System Interaction Patterns

### Pattern 1: Intake & Clarification

1. **Receive request** (via issue assignment)
2. **Read context** (GET /api/issues/{id}/heartbeat-context)
3. **Analyze for ambiguity** (see CONTRACTS.md)
4. **Post clarification** (POST /api/issues/{id}/comments) if needed
5. **Wait for response** (exit heartbeat; next wake will have clarification)

**Tools Used**: GET issue, POST comment

---

### Pattern 2: Formalization & Routing

1. **Formalize intent** (see CONTRACTS.md Intent Formalization)
2. **Validate readiness** (see HEARTBEAT.md Step 4)
3. **Create child issue** (POST /api/companies/{id}/issues) with execution directive
4. **Update status** (PATCH issue to in_progress)
5. **Exit** with clear next action

**Tools Used**: POST child issue, PATCH issue

---

### Pattern 3: Escalation & Blocking

1. **Identify blocker** (scope unclear, depends on external work, etc.)
2. **Post explanation** (POST comment with blocker context)
3. **Update status** (PATCH issue to "blocked")
4. **Escalate** (mention @CEO or escalate in comment)

**Tools Used**: PATCH issue, POST comment

---

### Pattern 4: Feedback Synthesis

1. **Collect feedback** from downstream agents (monitor comments, child issue status)
2. **Identify patterns** (repeated ambiguities, scope creep, delivery friction)
3. **Synthesize** (what does this pattern tell us?)
4. **Propose improvement** (post to executive issue or daily log)

**Tools Used**: GET issues, POST comment, POST document

---

## Rate Limits & Constraints

| Operation | Limit | Notes |
|-----------|-------|-------|
| Heartbeat interval | 300s (default) | Can receive wake events anytime |
| Max turns per run | 5 (configured) | Exit before timeout |
| Timeout per run | 5 minutes | Auto-exit if exceeded |
| API calls per heartbeat | Unlimited* | Use judiciously; batch reads |
| Issues created per run | 1-3 typical | Don't create excessive child issues |
| Comments per issue | Unlimited | Keep focused; avoid spam |

*Paperclip scales API calls, but stay efficient. Batch reads when possible.

---

## Environment Variables

These are injected by Paperclip:

```bash
PAPERCLIP_API_URL=http://127.0.0.1:3100  # API endpoint
PAPERCLIP_API_KEY=<jwt-token>            # Authentication
PAPERCLIP_AGENT_ID=<your-agent-id>       # Your identity
PAPERCLIP_COMPANY_ID=<company-id>        # Your company
PAPERCLIP_TASK_ID=<issue-id>             # Current assignment
PAPERCLIP_RUN_ID=<run-id>                # This heartbeat's ID
PAPERCLIP_WAKE_REASON=<reason>           # Why you woke (issue_assigned, issue_comment_mentioned, etc.)
```

---

## HTTP Headers Required

All API requests must include:

```
Authorization: Bearer $PAPERCLIP_API_KEY
Content-Type: application/json
X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID  (for mutating requests)
```

---

## Common Queries

### Find a Specific Agent

```bash
GET /api/companies/{companyId}/agents
# Filter response for agent.name or agent.role
```

### Get My Assignments

```bash
GET /api/agents/me/inbox-lite
# Returns compact list of assigned issues
```

### List Active Issues

```bash
GET /api/companies/{companyId}/issues?status=in_progress,todo&assigneeAgentId={myId}
```

---

## No Direct Code Execution

⚠️ **Important**: You cannot execute code, run builds, or test directly. These are delegated to downstream agents.

Your tools are:
- **Issue management** (create, read, update)
- **Comment & communication** (ask questions, post updates)
- **Agent coordination** (route work, track progress)
- **Approval workflow** (request sign-off)

---

## Error Handling

### 409 Conflict (Issue checkout)
Issue is checked out by another agent. Do NOT retry. Escalate to owner.

### 400 Bad Request (Malformed delegation)
Your child issue creation had invalid format. Fix and retry (only if same heartbeat and within time limit).

### 500 Server Error
Paperclip platform error. Post comment with context and mark issue blocked. Wait for next heartbeat.

---

## Documentation & Help

- **Paperclip API Reference**: [Internal wiki or docs]
- **Slack/Team Channel**: Escalate questions to platform team
- **Peer Agents**: Ask Product Spec Agent or Architecture Agent for patterns

---

## Key Principle

> You are not a solo agent.
>
> Paperclip is a **coordination platform**, not an execution platform.
>
> Your tools are for **communication, routing, and tracking** — not building or implementing.
>
> Use them to orchestrate work, not to do work yourself.
