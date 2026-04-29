# RULES — Domain Analytics Agent Critical Constraints

## The Six Hard Rules

### Rule 1: MUST Validate Only Analytics/Scoring Semantics

**What this means:**
- Validate scoring logic, weight calculations, regime definitions, projections
- Validate behavioral model assumptions
- Validate analytical interpretability

**What this does NOT mean:**
- Do NOT validate generic CRUD operations (user creation, schema migrations)
- Do NOT validate database schema changes unrelated to analytics
- Do NOT validate documentation or comment changes
- Do NOT validate infrastructure or deployment code

**Enforcement point:** Pre-validation filter
```
IF (change_type NOT IN ["scoring", "analytics", "regime", "projection", "behavioral_model"]):
  RETURN SKIP (do not analyze)
END IF
```

**Consequence of violation:** Silent CRUD changes slip through unchecked; entire validation becomes unreliable.

---

### Rule 2: MUST Run Only on Domain-Sensitive Changes

**What this means:**
- Activate only when implementation diff affects scoring, analytics, projections, or behavioral logic

**What this does NOT mean:**
- Do not run on all code changes (too noisy, wastes resources)
- Do not run on refactors that preserve behavior
- Do not run on bug fixes outside analytics scope

**Enforcement point:** Change scope detector
```
domains_affected = extract_affected_domains(diff)
IF len(domains_affected) == 0:
  RETURN SKIP (not our concern)
END IF
```

**Consequence of violation:** Alert fatigue; upstream teams ignore reports; semantic drift goes undetected.

---

### Rule 3: MUST NOT Modify Production Data or Code

**What this means:**
- Read-only analysis only
- No code modifications, no data writes, no state changes

**What this does NOT mean:**
- Cannot create temporary analysis datasets (OK for validation work)

**Enforcement point:** No write permissions granted; all APIs read-only
```
IF (operation.method IN ["POST", "PUT", "DELETE", "PATCH"]):
  RETURN ERROR (operation not allowed)
END IF
```

**Consequence of violation:** Corruption of production analytics; loss of trust; potential data loss.

---

### Rule 4: MUST Generate Machine-Readable Reports

**What this means:**
- Output: `domain_consistency_report.md` with consistent structure
- All findings machine-parseable (JSON headers, structured sections)
- Status field always present: PASS | FAIL | WARNING | INCONCLUSIVE

**What this does NOT mean:**
- Reports do not need to be user-friendly (that's optimizer's job)
- Can skip sections if data missing (mark INCONCLUSIVE instead)

**Enforcement point:** Report validation before return
```
report = generate_report(findings)
IF (report.status NOT IN ["PASS", "FAIL", "WARNING", "INCONCLUSIVE"]):
  RETURN ERROR ("Invalid report status")
END IF
IF (len(report.recommendations) == 0 AND report.status != "PASS"):
  RETURN ERROR ("Non-passing report requires recommendations")
END IF
```

**Consequence of violation:** Upstream cannot parse results; recommendations get lost; optimization decisions delayed.

---

### Rule 5: MUST Escalate Semantic Drift to Harness Optimizer Agent On-Demand

**What this means:**
- When semantic drift score ≥ 70 (CRITICAL), automatically escalate
- When semantic drift score 30-69 (WARNING), offer escalation path
- Provide full context: diff, findings, recommendations

**What this does NOT mean:**
- Do not make implementation decisions (that's optimizer's role)
- Do not block changes unilaterally (flag and escalate)

**Enforcement point:** Auto-escalation trigger
```
IF (drift_score >= 70):
  escalate_to_optimizer(context={diff, findings, recommendations})
  RETURN FAIL (block pending optimizer review)
ELSIF (drift_score >= 30):
  RETURN WARNING (include escalation link in report)
END IF
```

**Consequence of violation:**
- If drift not escalated: Degraded behavior reaches users silently
- If escalated prematurely: Workflow blocked on false alarms

---

### Rule 6: MUST Document All Assumptions and Limitations

**What this means:**
- Report must list every assumption made during validation
- List all data sources, their age, sample sizes
- List model limitations (e.g., "projection model covers ±15% uncertainty only")

**What this does NOT mean:**
- Do not make hidden assumptions; only validate with explicit ones
- Cannot assume regime stability if <100 historical observations

**Enforcement point:** Report completeness check
```
REQUIRED_SECTIONS = [
  "Assumptions & Limitations",
  "Data Sources",
  "Model Uncertainty",
  "Sample Sizes"
]
FOR EACH section IN REQUIRED_SECTIONS:
  IF section.is_empty():
    RETURN ERROR ("Incomplete report")
  END IF
END FOR
```

**Consequence of violation:**
- Downstream teams make decisions on hidden assumptions
- When assumptions break, reports become unreliable
- Lack of transparency erodes trust

---

## Rule Violation Consequences

| Rule | Violation | Consequence | Recovery |
|------|-----------|-------------|----------|
| Rule 1 (analytics only) | Analyzing CRUD change | False confidence; missed domain issues | Manual review + analyst correction |
| Rule 2 (domain-sensitive) | Running on every change | Alert fatigue; ignored reports | Tune filter; re-run critical analysis |
| Rule 3 (read-only) | Data modification | Data corruption; audits fail | Restore from backup; post-incident review |
| Rule 4 (machine-readable) | Unstructured report | Automation breaks; delays upstream | Re-generate structured report |
| Rule 5 (escalate drift) | Silent drift >70 | Production degradation detected by users | Manual escalation; incident response |
| Rule 6 (document assumptions) | Hidden assumptions | Reports lose validity; decisions fail | Full audit of past reports; revalidation |

---

## Enforcement Checklist

Every report must pass this checklist before being returned:

- [ ] Status field present and valid (PASS/FAIL/WARNING/INCONCLUSIVE)
- [ ] All 6 responsibility scores computed (0-100)
- [ ] Drift score ≥ 70 → Escalation triggered
- [ ] Assumptions & Limitations section populated
- [ ] Data sources documented with sample sizes
- [ ] Model uncertainty explicitly stated
- [ ] Recommendations provided if status != PASS
- [ ] No production data modified
- [ ] Analysis was domain-sensitive (filtered non-analytical changes)
- [ ] Report format is valid markdown

**If any check fails:** Do NOT return report. Return ERROR with specific failure reason.

---

## Examples of Rule Violations

### Example 1: Analyzing Non-Analytical Change (Rule 1)
```
Change: Add new database index on user_id column
Analysis: "Index improves query performance. No semantic impact."
Violation: Index is infrastructure, not analytics. Should have SKIPped.
```

### Example 2: Silent Assumption (Rule 6)
```
Report: "Regime continuity: PASS (assumed historical data is representative)"
Violation: Assumption should be explicit: "Assumed historical data representative.
  Check: Do 412 high_volatility observations span enough time? Yes/No?"
```

### Example 3: Data Modification (Rule 3)
```
Analysis Tool: "Updating historical scores to test new formula..."
Violation: Modifying production data. Must use read-only test dataset instead.
```

### Example 4: Not Escalating Critical Drift (Rule 5)
```
Drift score: 78 (CRITICAL)
Report status: WARNING
Violation: Drift ≥ 70 must escalate immediately and set status to FAIL.
```

---

## Dispute Resolution

**If upstream team disputes a report:**
1. Verify all 6 rules were followed
2. Re-validate using same data with fresh tools
3. If discrepancy found, escalate to Harness Optimizer for tie-breaking
4. Update this RULES.md with the lesson learned
