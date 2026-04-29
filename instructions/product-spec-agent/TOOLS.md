# TOOLS.md — Available Capabilities

This document lists tools, skills, and APIs available to the CEO agent.

## Paperclip API (Primary)

**Base URL**: `$PAPERCLIP_API_URL`  
**Auth**: `Authorization: Bearer $PAPERCLIP_API_KEY`  
**Header**: All mutating calls require `X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID`

### Identity & Context

```bash
GET /api/agents/me
# Returns: id, role, companyId, budget, chainOfCommand, permissions

GET /api/companies/{companyId}
# Returns: company metadata, agents, projects, status
```

### Issue Management

```bash
# Get issue with ancestors
GET /api/issues/{issueId}

# Get compact heartbeat context
GET /api/issues/{issueId}/heartbeat-context

# Get issue documents (plan, specs, etc)
GET /api/issues/{issueId}/documents
GET /api/issues/{issueId}/documents/{key}

# Create/update issue document
PUT /api/issues/{issueId}/documents/{key}

# Get comments
GET /api/issues/{issueId}/comments
GET /api/issues/{issueId}/comments?after={commentId}&order=asc

# Checkout task
POST /api/issues/{issueId}/checkout

# Update task status + comment
PATCH /api/issues/{issueId}
# { "status": "done|blocked|in_review", "comment": "markdown" }

# Add comment
POST /api/issues/{issueId}/comments

# Create subtask
POST /api/companies/{companyId}/issues
# { "title", "description", "parentId", "goalId", "assigneeAgentId", "priority" }

# Create interaction (for proposals/confirmations)
POST /api/issues/{issueId}/interactions
# { "kind": "request_confirmation|suggest_tasks|ask_user_questions", "title", "body" }
```

### Agent Management

```bash
# List all agents in company
GET /api/companies/{companyId}/agents

# Get agent configurations
GET /api/companies/{companyId}/agent-configurations

# Hire new agent
POST /api/companies/{companyId}/agent-hires
# { "name", "role", "title", "icon", "reportsTo", "capabilities", "adapterType", ... }

# List company skills
GET /api/companies/{companyId}/skills

# Sync agent skills
POST /api/agents/{agentId}/skills/sync
```

### Approvals

```bash
# Get approval details
GET /api/approvals/{approvalId}

# Get linked issues
GET /api/approvals/{approvalId}/issues

# Add approval comment
POST /api/approvals/{approvalId}/comments
```

### Search & Discovery

```bash
# Search issues
GET /api/companies/{companyId}/issues?q=search+term

# List issues with filters
GET /api/companies/{companyId}/issues?assigneeAgentId={id}&status=todo,in_progress,blocked

# Get compact inbox
GET /api/agents/me/inbox-lite
```

---

## Skills (Loaded via `skill` tool)

### Paperclip Skills (Coordination)

| Skill | Purpose | When to Use |
|-------|---------|-----------|
| `paperclip` | Coordinate issues, tasks, approvals | Every heartbeat for API operations |
| `paperclip-create-agent` | Hire new agents | When capacity needed |
| `para-memory-files` | Store/retrieve facts, daily notes | All memory operations |

### Reference Skills (Research)

| Skill | Purpose | When to Use |
|-------|---------|-----------|
| `find-docs` | Authoritative documentation | When researching technologies |
| `context7` / `context7-mcp` | Library/framework documentation | Setup, code gen, API lookups |
| `gh-cli` | GitHub operations | PR reviews, repo management |

### Execution Skills (Delegation)

| Skill | Purpose | When to Use |
|-------|---------|-----------|
| `graphify-windows` | Visualize codebases as knowledge graphs | Understand codebase structure |
| `interface-design` | UI/UX design specialists | Visual system work |
| `review-work` | Post-implementation QA orchestration | After significant work completes |

---

## Direct Tools (Use When Skills Unavailable)

### Bash / PowerShell
```bash
# Execute commands
bash -c "command"

# Use RTK for token efficiency
rtk curl -s "url"
rtk git status
rtk cargo build
```

### File Operations
```bash
read filePath              # Read file contents
write filePath content     # Write file
edit filePath oldStr newStr  # Replace in file
glob pattern               # Find files
```

### LSP (Language Server)
```bash
lsp_diagnostics filePath     # Check type errors
lsp_goto_definition line col  # Jump to definition
lsp_find_references line col  # Find all usages
```

### Git
```bash
git status
git log
git diff
git add .
git commit -m "message"
git push
```

---

## Environment Variables

**Always Available**:
```bash
$PAPERCLIP_API_URL          # API endpoint (http://127.0.0.1:3100)
$PAPERCLIP_API_KEY          # Auth token
$PAPERCLIP_COMPANY_ID       # Company UUID
$PAPERCLIP_AGENT_ID         # CEO agent UUID
$PAPERCLIP_RUN_ID           # Current run UUID (include in headers)
```

**Conditionally Set** (when triggered):
```bash
$PAPERCLIP_TASK_ID          # Issue that triggered this wake
$PAPERCLIP_WAKE_REASON      # Why this run started
$PAPERCLIP_WAKE_COMMENT_ID  # Specific comment that triggered wake
$PAPERCLIP_APPROVAL_ID      # Approval resolution
```

---

## Tool Selection Guide

**Need to...**

| Goal | Tool | Why |
|------|------|-----|
| Get work assignments | Paperclip API | Authoritative source |
| Create subtask | Paperclip API | Tracked for governance |
| Hire agent | `paperclip-create-agent` skill | Guided workflow |
| Store/recall facts | `para-memory-files` skill | Persistent across sessions |
| Look up docs | `find-docs` or `context7` | Current information |
| Understand codebase | `graphify-windows` skill | Visual structure |
| Review code changes | `review-work` skill | QA orchestration |
| Update issue status | Paperclip API | Must use for tracking |
| Check git history | bash git | Fast, local |
| Find type errors | `lsp_diagnostics` | Real-time analysis |

---

## Rate Limiting & Constraints

**API**: No explicit rate limit observed, but use RTK for token efficiency.

**Heartbeat**: Single run per heartbeat. Cannot spawn parallel background agents as CEO (that's for engineers).

**Budget**: Monthly budget tracked. Above 80% → critical work only.

**Memory**: Sessions don't persist context. Always use `para-memory-files` for durable facts.

---

## Quick Recipes

### Hire a New Agent
```bash
1. Use `paperclip-create-agent` skill
2. Collect role, responsibilities, reporting line
3. Submit hire request via Paperclip API
4. Track approval if governance applies
```

### Delegate Work
```bash
1. Create subtask: POST /api/companies/{companyId}/issues
2. Set parentId, goalId, assigneeAgentId
3. Include clear description + acceptance criteria
4. Track in daily notes
5. Check progress in next heartbeat
```

### Store Important Info
```bash
1. Use `para-memory-files` skill
2. Create entity under $AGENT_HOME/life/{category}/{name}/
3. Save durable facts to items.yaml
4. Update daily notes with timeline entry
```

### Unblock a Report
```bash
1. Read blocker comment carefully
2. If CEO-level decision: decide and comment with rationale
3. If board decision needed: escalate via comment with specific ask
4. Follow up in next heartbeat
```

### Close an Issue
```bash
1. Verify work is complete
2. Run lsp_diagnostics to check errors (if code-related)
3. PATCH issue: status=done, comment with summary
4. Include links to any approvals or follow-ups
```
