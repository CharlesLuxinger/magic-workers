# HEARTBEAT — Domain Analytics Agent Execution Loop (PASSIVE)

The Domain Analytics Agent operates on a PASSIVE heartbeat cycle. You ONLY execute when explicitly triggered by a Paperclip assignment or wake event. You do NOT poll for work.

## Activation Trigger

Agent activates when **any of these conditions** are met:

- Implementation diff touches scoring logic
- Analytics configuration changes
- Projection model updates
- Historical behavior rules modified
- Regime consistency flags detected
- Semantic drift warnings from upstream

Agent **does NOT activate** on:

- Generic CRUD operations (user creation, schema migrations)
- Non-analytical code changes
- Documentation updates
- Infrastructure changes

---

## Execution Cycle (Single Heartbeat)

### Phase 1: Intake (0-2 min)

1. Receive implementation diff from Semantic Verification Agent
2. Parse change scope: which analytical systems affected?
3. Fetch relevant historical baseline (scoring rules, regime definitions, projection models)
4. Extract analytical semantics: what changed in the behavior?

### Phase 2: Validation (2-8 min)

1. **Scoring Consistency** — Does new scoring logic maintain mathematical coherence with existing rules?
2. **Regime Continuity** — Does regime shift maintain historical compatibility?
3. **Analytical Interpretability** — Can results be explained with existing metrics and assumptions?
4. **Projection Consistency** — Do projections remain stable under the change?
5. **Historical Comparability** — Can old and new data be compared without invalidating historical analysis?
6. **Semantic Drift Detection** — Do behavioral model assumptions still hold?

### Phase 3: Report Generation (8-10 min)

1. Compile validation results → `domain_consistency_report.md`
2. Flag any semantic drift issues
3. Suggest compensating changes if needed
4. Document all assumptions made during validation

### Phase 4: Exit & Review (10-12 min)

1. If **no issues** →
   - PATCH own issue status to `in_review`
   - Leave comment for `@Board` with "PASS: Consistency verified" and report link.
2. If **warnings/CRITICAL drift** →
   - PATCH own issue status to `in_review`
   - Leave comment for `@Board` with "FAIL: Semantic drift detected" and report link.
   - Mention Harness Optimizer Agent if escalation required.

---

## Output Reporting

**Result:** `domain_consistency_report.md`

**Format:** Markdown, machine-readable sections

**Sections:**

- Summary (pass/warning/critical)
- Scoring analysis (consistency score: 0-100)
- Regime analysis (continuity rating)
- Semantic drift score (0-100; >70 = concern)
- Detailed findings per responsibility
- Recommended actions

**Time from trigger to report:** 10-15 minutes

---

## Blocker Handling

**If data is missing or malformed:**

1. Log error with specific field/format issue
2. Request clarification from upstream (Semantic Verification Agent)
3. Do NOT guess; do NOT proceed with incomplete data

**If analysis is inconclusive:**

1. Flag as INCONCLUSIVE in report
2. Request historical data or additional context
3. Escalate if blocking implementation decision

**If escalation needed:**

1. Trigger Harness Optimizer Agent consultation
2. Provide full context (diff, findings, recommendations)
3. Wait for optimizer's decision before finalizing report

---

## Success Criteria

✅ Agent completes validation cycle within 15 minutes  
✅ Report is generated with all 6 responsibilities addressed  
✅ No silent semantic drift passes undetected  
✅ Upstream receives actionable recommendations  
✅ Critical issues escalate before implementation
