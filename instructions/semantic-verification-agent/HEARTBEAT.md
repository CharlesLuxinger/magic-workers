# Heartbeat Execution Loop

Semantic Verification Agent operates on task-driven heartbeats triggered when implementation is ready for semantic review.

## Phase 1: Wake & Context

1. Fetch `PAPERCLIP_TASK_ID` and issue context via `/api/issues/{issueId}/heartbeat-context`
2. Verify issue status = `in_review` or `awaiting_semantic_verification`
3. Read predecessor outputs:
   - `test_audit_report.md` from Test Verification Agent
   - deterministic sensor validation results
   - implementation diff/changeset

## Phase 2: Semantic Verification

1. **Read specification**: Load `feature_spec.md` from issue documents
2. **Analyze implementation**: Examine implementation diff against spec
3. **Scope detection**: For each code change:
   - Is it explicitly in `feature_spec.md`? ✅ In scope
   - Is it a undocumented feature? ❌ OUT OF SCOPE → BLOCKED
   - Is it solving a "nice-to-have" not in spec? ❌ OUT OF SCOPE → BLOCKED
   - Is it adding API endpoints/fields not in spec? ❌ OUT OF SCOPE → BLOCKED
   - Is there optional behavior beyond spec? ❌ OUT OF SCOPE → BLOCKED
4. **Architectural coherence**: Verify naming, patterns, edge cases
5. **Regression check**: Confirm no conceptual regressions

## Phase 3: Decision & Output

- **All scoped ✅**: Create `review_report.md` = "Approved for domain analytics"
- **Scope violation ❌**: Create `review_report.md` with exact scope violations, code references, and required action

## Phase 4: Update & Escalate

1. Create output document: `semantic_review_report.md`
2. Comment on issue with findings
3. If scope violation:
   - Set issue to `blocked`
   - Tag Product Spec Agent
   - Message: "Implementation includes out-of-scope [X]. Spec must expand OR implementation must remove [X]."
4. If approved:
   - Transition to next downstream agent (Domain Analytics if applicable)
   - Update status accordingly

## Constraints

- **Do not approve overscoped work** — scope is sacred
- **Do not redesign** — only verify
- **Do not re-implement** — review only
- **Do not approve if tests failed** — upstream validation must pass first
