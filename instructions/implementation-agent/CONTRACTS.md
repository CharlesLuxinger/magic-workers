# CONTRACTS.md - I/O Specification

## Input Contract

An implementation contract is the primary input to Implementation Agent 2. It defines:

### Structure

```yaml
title: "Implementation Contract: {Feature Name}"
objective: |
  What should be implemented. One-sentence description.
scope:
  - "Requirement 1: ..."
  - "Requirement 2: ..."
  - "Requirement 3: ..."
constraints:
  - "Must not: ..."
  - "Must respect: ..."
  - "Must use: ..."
test_strategy:
  - "Unit tests for function X"
  - "Integration tests for flow Y"
  - "Edge case coverage for Z"
coverage_target: "XX%" (default: 80%)
acceptance_criteria:
  - "All tests pass"
  - "Coverage >= target"
  - "No architecture violations"
  - "Code review feedback addressed"
architectural_boundaries:
  - "Do not modify X module"
  - "Respect interface Y"
  - "Use pattern Z for consistency"
existing_patterns:
  - "Reference file A for style"
  - "Reference file B for error handling"
  - "Reference file C for testing approach"
```

### Validation Rules

**Valid contract must have:**
- ✅ title (present)
- ✅ objective (single sentence, clear)
- ✅ scope (non-empty list, specific)
- ✅ constraints (architectural boundaries defined)
- ✅ test_strategy (explicit test cases)
- ✅ acceptance_criteria (measurable and verifiable)
- ✅ coverage_target (number >= 0)

**Invalid contract:**
- ❌ Vague objective ("Improve the system")
- ❌ Open-ended scope ("Add cool features")
- ❌ No test strategy
- ❌ Conflicting constraints
- ❌ Unmeasurable acceptance criteria

**If contract is invalid:**
1. Do NOT proceed with implementation
2. Escalate to Architecture & Test Design Guard Agent
3. Request contract revision
4. Mark issue as `blocked` with escalation reason

---

## Output Deliverable

Implementation Agent 2 produces an **implementation patch** that satisfies the contract.

### Structure

```
implementation-patch-{timestamp}.md
├── summary
│   ├── contract_id: (reference)
│   ├── status: (in_progress | complete | blocked)
│   ├── phase: (red | green | refactor | verify)
│   └── next_action: (what comes next)
├── changes
│   ├── files_added: [list]
│   ├── files_modified: [list]
│   ├── files_deleted: [list]
│   └── diff: (git diff or semantic diff)
├── tests
│   ├── test_count: (number)
│   ├── pass_rate: (X/Y)
│   ├── coverage: (XX%)
│   └── test_log: (output)
├── verification
│   ├── acceptance_criteria: [checks]
│   ├── architectural_violations: [if any]
│   ├── code_review_feedback: [if any]
│   └── sign_off: (ready for next phase)
└── blockers
    ├── current_blocker: (if any)
    ├── root_cause: (why blocked)
    └── unblock_owner: (who can unblock)
```

### Validation Rules

**Patch is valid when:**
- ✅ All tests pass
- ✅ Coverage >= contract target
- ✅ No `as any`, `@ts-ignore`, or type escapes
- ✅ No empty catch blocks
- ✅ No commented-out code
- ✅ Diff is focused (no scope creep)
- ✅ Architectural boundaries respected
- ✅ Code follows existing patterns

**Patch is invalid when:**
- ❌ Tests fail
- ❌ Coverage < target
- ❌ Contains type suppressions
- ❌ Contains unhandled errors
- ❌ Diff includes unrelated changes
- ❌ Architecture violated

**If patch is invalid:**
1. Identify root causes
2. Fix each issue (respect red-green-refactor)
3. Re-run tests
4. Re-validate
5. Repeat until valid

---

## Handoff to Test Verification Agent

When patch is complete and valid:

1. Create issue with type: `test-verification`
2. Assign to: Test Verification Agent
3. Include: implementation patch, test results, coverage report
4. Set status: `in_review`
5. Add comment: "Ready for semantic verification and acceptance testing"

Test Verification Agent will:
- Run full semantic checks
- Verify acceptance criteria
- Sign off or request changes
- Mark done when approved

---

## Escalation Protocol

When to escalate to Architecture & Test Design Guard Agent:

- ❌ Invalid contract received (vague, conflicting, unmeasurable)
- ❌ Architectural violation detected during implementation
- ❌ Constraint contradiction discovered
- ❌ Scope ambiguity requires design decision
- ❌ Existing code patterns conflict with contract
- ❌ Test strategy impossible (not testable)
- ❌ Coverage target unachievable

Escalation format:
```markdown
## Escalation

**Issue:** [description]
**Root Cause:** [why this is escalated]
**Requested Action:** [what Architecture needs to decide]
**Contract Reference:** [link]
**Current Status:** Blocked until resolved
```

---

## Examples

### Valid Contract

```yaml
title: "Add JWT Token Refresh"
objective: "Implement JWT refresh token rotation with 7-day expiration."
scope:
  - "Add POST /auth/refresh endpoint"
  - "Validate refresh token signature"
  - "Rotate token on each refresh"
  - "Respond with new access + refresh token"
constraints:
  - "Respect existing auth module structure"
  - "Use bcryptjs for hashing (already in deps)"
  - "Do not modify login endpoint"
  - "Do not add new database tables"
test_strategy:
  - "Unit: valid refresh token generates new tokens"
  - "Unit: invalid token rejected"
  - "Unit: old token no longer works"
  - "Integration: /auth/refresh flow"
coverage_target: "85%"
acceptance_criteria:
  - "All tests pass"
  - "Coverage >= 85%"
  - "No type errors"
  - "Existing auth tests still pass"
architectural_boundaries:
  - "Stay within src/auth/ module"
  - "Use existing middleware patterns"
  - "Respect token schema in database"
```

### Invalid Contract

```yaml
title: "Improve auth"  # ❌ Vague
objective: "Make auth better"  # ❌ Not measurable
scope: []  # ❌ Empty
constraints: []  # ❌ No boundaries
test_strategy: []  # ❌ No tests defined
coverage_target: null  # ❌ Missing
```

### Valid Patch Output

```
implementation-patch-2026-04-29-001.md

## Summary
- Contract ID: MAG-12
- Status: complete
- Phase: verify
- Next Action: Submit to Test Verification Agent

## Changes
- Files Modified: src/auth/refresh.ts
- Tests Added: src/auth/refresh.test.ts

## Tests
- Test Count: 12
- Pass Rate: 12/12 ✅
- Coverage: 89%

## Verification
✅ All acceptance criteria met
✅ No architectural violations
✅ Code follows existing patterns
✅ No type suppressions
✅ Ready for review
```
