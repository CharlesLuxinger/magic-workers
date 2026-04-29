# Contracts

## Inputs

### 1. Feature Specification (`feature_spec.md`)
- **Source**: Issue document on `{issueId}#document-feature_spec`
- **Format**: Markdown with explicit scope boundaries
- **Content**: Requirements, acceptance criteria, constraints, out-of-scope list
- **Required**: YES — without spec, verification is impossible

### 2. Implementation Diff/Changeset
- **Source**: Git commit range or file diff from preceding agent
- **Format**: Unified diff or list of changed files with content
- **Content**: All code changes to review
- **Required**: YES — the object of verification

### 3. Test Audit Report (`test_audit_report.md`)
- **Source**: Issue document from Test Verification Agent
- **Format**: Markdown structured report
- **Content**: Test pass/fail status, coverage metrics, deterministic validator outputs
- **Required**: YES — scope verification happens only after tests pass

### 4. Deterministic Sensor Outputs
- **Source**: Test Verification Agent outputs (e.g., linter, type checker results)
- **Format**: JSON or structured log
- **Content**: Compilation errors, type safety, lint violations
- **Required**: Conditional — used to validate that implementation is deterministically sound

## Outputs

### Review Report (`semantic_review_report.md`)
- **Destination**: Issue document on `{issueId}#document-semantic_review_report`
- **Format**: Markdown structured report
- **Content**:
  - **Verdict**: APPROVED | BLOCKED
  - **Scope Analysis**: For each code change, in-scope? out-of-scope? why?
  - **Violations** (if blocked): Exact spec reference + code location + required action
  - **Coherence Findings**: Naming, architecture, edge cases
  - **Regression Check**: Any regressions detected?
  - **Next Step**: What happens next (domain analytics, spec clarification, etc.)
- **Required**: YES

### Issue Comment
- **Destination**: Issue comment thread
- **Format**: Markdown summary of findings
- **Content**: Verdict, blockers (if any), next action
- **Audience**: Implementation team, Product Spec Agent, downstream agents
- **Required**: YES

### Issue Status Update
- **Transition**: Based on verdict
  - ✅ Approved → status = `in_review` (or forward to next agent)
  - ❌ Blocked → status = `blocked`, comment with blocker explanation
- **Required**: YES

## Validation Rules

### Scope Enforcement (Hard Stops)

1. **Undocumented Features**: Any code path NOT in spec → BLOCKED
2. **Nice-to-Have Scope Creep**: Features marked "would be nice" but not required → BLOCKED
3. **New API Endpoints**: Endpoints not in spec → BLOCKED
4. **New Data Fields**: Database/API fields not in spec → BLOCKED
5. **Optional Behavior**: Code that has behavior beyond spec → BLOCKED

### Coherence Checks (Warnings → Blocks if egregious)

1. **Naming Consistency**: Function/variable names follow codebase patterns?
2. **Architecture Coherence**: Placement, structure align with existing modules?
3. **Edge-Case Completeness**: Spec edge cases all handled?

### Pre-Requisites (Hard Fails)

- Test Verification report must exist
- Deterministic validators must pass
- Spec document must exist and be clear
- If ANY prerequisite missing → BLOCKED with clear explanation

## Communication Protocol

### When Approving
```
## ✅ Semantic Verification: APPROVED

- Scope: All changes within `feature_spec.md` boundaries
- Coherence: Naming and architecture consistent
- Edge cases: Complete per spec
- Next: [Forward to Domain Analytics / Awaiting next phase]
```

### When Blocking (Scope Violation)
```
## ❌ Semantic Verification: BLOCKED

**Scope Violation**: Implementation includes out-of-spec capability

- **Violation**: [Feature name] (code: [file:line])
  - **Spec says**: [Quote from spec]
  - **Code does**: [Quote from implementation]
  - **Required action**: Spec must expand OR code must remove

- **Blocker owner**: Product Spec Agent
- **Next**: Await spec clarification before re-review
```

### When Blocking (Other Reason)
```
## ❌ Semantic Verification: BLOCKED

**Reason**: [Edge cases incomplete / Architecture misalignment / etc.]

- [Specific finding with code reference]
- **Required action**: [Clear action for Implementation Agent or Product Spec]
- **Blocker owner**: [Team]
- **Next**: [Resolution path]
```

## Escalation Path

- **Scope ambiguity**: → Product Spec Agent (for spec clarification)
- **Architecture question**: → Architecture & Test Design Guard Agent (for review)
- **Implementation blocker**: → Implementation Agent (for fix)
- **Unresolvable conflict**: → Chief of Staff Agent (for decision)
