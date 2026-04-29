# CONTRACTS — Domain Analytics Agent I/O Specification

## Input Format

### Implementation Diff

**Required:** Yes  
**Format:** Unified diff with line numbers and context

```
--- a/src/scoring/weight_calculator.py
+++ b/src/scoring/weight_calculator.py
@@ -42,5 +42,7 @@
   def calculate_weight(regime, factors):
-    return sum(f * WEIGHT_MAP[r] for f in factors)
+    base_weight = sum(f * WEIGHT_MAP[r] for f in factors)
+    adjustment = regime.variance_factor * 0.15
+    return base_weight * adjustment
```

### Scoring/Analytics Changes

**Required:** Yes  
**Format:** JSON with change description

```json
{
  "module": "src/scoring/weight_calculator.py",
  "change_type": "formula_modification",
  "what_changed": "Added variance_factor adjustment to weight calculation",
  "affected_systems": ["scoring", "projections"],
  "historical_impact": "affects scores calculated after 2026-01-01"
}
```

### Projections

**Required:** Yes (if change affects projections)  
**Format:** JSON array with baseline and new projection models

```json
{
  "projection_model": "linear_extrapolation_v2",
  "baseline_assumptions": {
    "stability_assumption": "factors maintain mean ± 2σ",
    "regime_assumption": "regime remains in current state"
  },
  "affected_metrics": ["user_score", "cohort_average"]
}
```

### Historical Behavior Rules

**Required:** Yes  
**Format:** YAML with regime definitions and historical rules

```yaml
regimes:
  high_volatility:
    definition: "std_dev > 0.5"
    stability_rule: "weight_change <= 5%"
    historical_count: 412
  standard:
    definition: "0.1 < std_dev <= 0.5"
    stability_rule: "weight_change <= 2%"
    historical_count: 8394
```

---

## Output Format

### domain_consistency_report.md

**Machine-readable markdown report** with sections:

```markdown
# Domain Consistency Report
Generated: 2026-04-29T06:30:00Z
Status: FAIL / PASS / WARNING / INCONCLUSIVE

## Executive Summary
[1-2 sentence result]

## Scoring Analysis
- Consistency Score: 0-100
- Status: PASS / FAIL
- Finding: [specific issue or confirmation]

## Regime Analysis
- Continuity Rating: 0-100
- Status: PASS / FAIL
- Finding: [historical compatibility result]

## Analytical Interpretability
- Interpretability Score: 0-100
- Status: PASS / FAIL / INCONCLUSIVE
- Finding: [can old metrics still be explained?]

## Projection Consistency
- Stability Score: 0-100
- Status: PASS / FAIL / WARNING
- Finding: [projection stability under change]

## Historical Comparability
- Comparability Score: 0-100
- Status: PASS / FAIL
- Finding: [can old and new data be compared?]

## Semantic Drift Detection
- Drift Score: 0-100 (higher = more drift)
- Status: PASS / FAIL / WARNING
- Finding: [assumption violations detected]

## Recommendations
1. [Action if FAIL]
2. [Mitigations if WARNING]
3. [Proceed if PASS]

## Assumptions & Limitations
- Assumed regime definition stable through 2026-03-31
- Historical data: 412 high_volatility, 8394 standard regime observations
- Projection model: linear_extrapolation_v2 (±15% uncertainty)
```

---

## Validation Rules

### PASS Criteria (all must be true)
- Scoring consistency score ≥ 90
- Regime continuity rating ≥ 85
- Analytical interpretability ≥ 80
- Projection stability ≥ 85
- Historical comparability ≥ 80
- Semantic drift score < 30

### WARNING Criteria (at least one true)
- Any score between 70-89
- Semantic drift score 30-69
- Inconclusive findings on one responsibility
- Change affects <100 historical observations

### FAIL Criteria (at least one true)
- Any score < 70
- Semantic drift score ≥ 70
- Regime discontinuity detected
- Historical data invalidated

---

## Data Quality Requirements

| Field | Required | Validation |
|-------|----------|-----------|
| implementation_diff | YES | Must parse without errors |
| change_type | YES | Must be known type (formula_modification, regime_shift, etc.) |
| affected_systems | YES | At least one system listed |
| historical_behavior_rules | YES | All regimes must have counts > 10 |
| baseline_assumptions | YES | Must be explicit (no "probably"s) |
| projection_model | CONDITIONAL | Required if change affects projections |

### Error Handling

**Malformed Input:**
```
Status: ERROR
Field: "historical_behavior_rules"
Issue: "regime 'ultra_high_volatility' has count=3, minimum=10"
Action: Request updated rules or exclude regime from analysis
```

**Missing Data:**
```
Status: INCONCLUSIVE
Field: "projections"
Issue: "Not provided; cannot assess projection consistency"
Action: Request projection model or accept inconclusive finding
```

**Ambiguous Change:**
```
Status: INCONCLUSIVE
Finding: "Change description unclear; multiple interpretations possible"
Action: Request clarification on intended behavior
```

---

## Decision Criteria

| Scenario | Report Status | Escalate? | Recommend Block? |
|----------|---------------|-----------|-----------------|
| All green (PASS) | PASS | No | No |
| One yellow (WARNING) | WARNING | No | No (proceed with caution) |
| Multiple yellow | WARNING | Possibly | Case-by-case |
| One red (FAIL) | FAIL | YES | YES |
| INCONCLUSIVE | INCONCLUSIVE | YES | Maybe (need info) |
| Semantic drift >70 | FAIL | YES | YES |
