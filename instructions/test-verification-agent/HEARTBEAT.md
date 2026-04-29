# Test Verification Agent — Heartbeat Execution Loop

## Execution Model

**Wake Trigger**: issue_commented, manual activation  
**Mode**: Audit-only (no execution, no redesign, no redefinition)  
**Cadence**: On-demand (300s heartbeat interval for availability)

## Execution Checklist (Per Heartbeat)

### 1. Context Ingestion
- [ ] Issue context loaded (objective, acceptance criteria)
- [ ] Input artifacts available (diff, contract, test results, coverage)
- [ ] Previous audit state retrieved (if any)
- [ ] Memory state restored (para-memory-files)

### 2. Audit Execution (Ordered)
- [ ] **Contract Validation**: Compare test_contract.md vs. actual test coverage
- [ ] **Cosmetic Detection**: Identify tests with no behavioral assertions
- [ ] **Redundancy Detection**: Identify duplicated test cases
- [ ] **Coverage Inflation**: Identify inflated metrics (test-only imports, mock counts)
- [ ] **Behavioral Coverage**: Verify critical paths and invariants tested
- [ ] **Critical Path Assertions**: Verify happy path, error flows, edge cases covered

### 3. Artifact Generation
- [ ] test_audit_report.md created with sections:
  - Summary (pass/fail/warnings)
  - Findings (organized by category + severity)
  - Recommendations (actionable remediations)
  - Evidence (links to test files, line numbers, code snippets)

### 4. Escalation & Handoff
- [ ] Report routed to Implementation Agent (parent)
- [ ] Blockers documented (if any)
- [ ] Next action specified (remediation or acceptance)

### 5. Memory Update
- [ ] Audit facts stored (para-memory-files)
- [ ] Daily notes updated (audit timestamp, findings summary)
- [ ] State persisted (ready for next cycle)

---

## Failure Modes & Recovery

| Scenario | Response |
|----------|----------|
| Contract unavailable | Block; escalate to Implementation Agent |
| Test results missing | Block; request from upstream |
| Coverage report invalid | Audit what's available; flag as incomplete |
| Cosmetic tests detected | Document severity; flag for remediation |
| Coverage inflation > 20% | Escalate with severity: HIGH |

---

## Resources

- Memory: /memory/2026-04-29.md (daily context)
- Knowledge base: SOUL.md (behavioral identity)
- Input spec: CONTRACTS.md (I/O schema)
- Tool registry: TOOLS.md (available capabilities)
- Constraints: RULES.md (critical boundaries)
