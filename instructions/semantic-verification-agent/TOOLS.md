# Tools

## Paperclip Integration

### Issue & Comment APIs
- `GET /api/issues/{issueId}` — Fetch issue details, status, ancestors
- `GET /api/issues/{issueId}/documents/{key}` — Fetch issue documents (spec, reports)
- `PUT /api/issues/{issueId}/documents/{key}` — Create/update issue documents
- `GET /api/issues/{issueId}/comments` — Read comment thread
- `POST /api/issues/{issueId}/comments` — Post findings/verdict
- `PATCH /api/issues/{issueId}` — Update issue status, add blocking info

### Agent Context
- `PAPERCLIP_TASK_ID` — Current issue/task ID
- `PAPERCLIP_AGENT_ID` — Your agent identity
- `PAPERCLIP_COMPANY_ID` — Company context
- `PAPERCLIP_API_URL` — API endpoint
- `PAPERCLIP_API_KEY` — Authentication

## Code Analysis Tools

### Local Tools
- **Git**: `git show`, `git diff`, `git log` — inspect changesets
- **Grep/Ripgrep**: Search code for patterns, undocumented features
- **File reading**: Direct file access to review implementation

### LSP / Language Servers (if available)
- Type checking, symbol references
- Architecture consistency queries

## Decision Checklist

Before approving:
- [ ] Spec document exists and is clear
- [ ] Test report exists and shows passing
- [ ] Deterministic validators passed (types, lints, etc.)
- [ ] Implementation diff reviewed against spec line-by-line
- [ ] No undocumented features detected
- [ ] No scope creep detected
- [ ] Naming consistent with codebase
- [ ] Architecture coherent with system design
- [ ] Edge cases complete per spec

Before blocking:
- [ ] Exact scope violation identified
- [ ] Code reference provided (file:line)
- [ ] Spec reference quoted
- [ ] Clear action for resolution
- [ ] Blocker owner identified

## Output Locations

- **Review Report**: `{issueId}#document-semantic_review_report`
- **Issue Comment**: Posted to issue thread
- **Status Update**: Issue status field

## Error Handling

| Error | Recovery |
|-------|----------|
| Spec document missing | Block with clear message → Product Spec Agent |
| Test report missing | Block → Test Verification Agent |
| Ambiguous spec | Flag specific ambiguity → Product Spec Agent |
| Conflicting requirements | Flag + escalate → Chief of Staff |
| Implementation unclear | Request clarification comment → Implementation Agent |
