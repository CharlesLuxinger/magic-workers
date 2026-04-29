# TOOLS — Domain Analytics Agent Capabilities

## Available Analysis Tools

### Diff Parser (`diff_parser`)
**Purpose:** Extract analytical changes from implementation diffs  
**Input:** Unified diff with line numbers  
**Output:** Structured change objects (module, function, old_code, new_code)  
**Example:**
```python
parsed = diff_parser.parse("--- a/src/scoring/weight_calculator.py\n+++ b/src/scoring/weight_calculator.py\n...")
# Returns: [{"module": "src/scoring/weight_calculator.py", "function": "calculate_weight", "old_formula": "...", "new_formula": "..."}]
```

### Weight Calculator (`weight_calculator`)
**Purpose:** Validate scoring weight formulas for mathematical consistency  
**Input:** Old formula, new formula, historical weights, regime definitions  
**Output:** Consistency score (0-100), violations list  
**Limitations:**
- Cannot validate symbolic formulas; requires explicit arithmetic
- Assumes regime definitions are correct (does not validate those)
- Limited to polynomial and rational functions

### Formula Evaluator (`formula_evaluator`)
**Purpose:** Compare old and new formulas on historical data  
**Input:** Formula strings, historical dataset (min 100 samples)  
**Output:** Delta distribution (mean change, std, outliers)  
**Limitations:**
- Requires historical data; cannot work on net-new features
- Does not handle time-series drift (use semantic_comparator for that)

### Semantic Comparator (`semantic_comparator`)
**Purpose:** Detect behavioral model assumption violations  
**Input:** Old model assumptions (YAML), new model assumptions (YAML), behavioral data  
**Output:** Assumption violation count, drift score (0-100)  
**Limitations:**
- Cannot infer hidden assumptions; requires explicit list
- Statistical power improves with >1000 samples

### Regime Validator (`regime_validator`)
**Purpose:** Verify regime definitions remain coherent  
**Input:** Regime definitions (YAML), historical data tagged with regimes  
**Output:** Regime coherence score (0-100), overlap detection  
**Limitations:**
- Cannot detect new regimes; only validates existing ones
- Requires >10 observations per regime minimum

### Projection Stabilizer (`projection_stabilizer`)
**Purpose:** Test whether projections remain stable under change  
**Input:** Projection model, change description, historical baseline  
**Output:** Stability score (0-100), out-of-bounds forecast % (should be <5%)  
**Limitations:**
- Limited to linear and exponential models
- Cannot handle regime transitions within projection window

---

## External Systems

### Harness Optimizer Agent
**Access:** On-demand consultation  
**When to use:** When semantic drift detected and needs resolution strategy  
**API:**
```
POST /harness-optimizer/consult
{
  "issue": "semantic_drift_detected",
  "context": {...},
  "drift_score": 75,
  "request": "recommend mitigations"
}
```

### Scoring Service
**Access:** Read-only queries  
**When to use:** Fetch current scoring rules, weight definitions  
**API:**
```
GET /scoring-service/rules/{regime}/{scoring_model}
GET /scoring-service/weights/historical?from_date=2026-01-01
```

### Historical Data Store
**Access:** Read-only queries  
**When to use:** Retrieve historical behavior data for validation  
**API:**
```
GET /data-store/historical/{metric}?regime=standard&date_range=2026-01-01:2026-04-29
GET /data-store/regimes/{regime_name}?count=true
```

---

## What I Cannot Do

❌ **Cannot modify code** — I only validate  
❌ **Cannot bypass validation rules** — constraints are hard  
❌ **Cannot run without upstream trigger** — requires Semantic Verification Agent to invoke  
❌ **Cannot make implementation decisions** — only Harness Optimizer decides mitigations  
❌ **Cannot work with incomplete data** — all required fields must be present  
❌ **Cannot predict future drift** — only detect current/historical drift  

---

## Tool Timeout & Fallback

| Tool | Timeout | Fallback |
|------|---------|----------|
| diff_parser | 10s | FAIL (cannot proceed without parsed diff) |
| weight_calculator | 30s | INCONCLUSIVE (cannot assess scoring consistency) |
| formula_evaluator | 60s | INCONCLUSIVE (cannot assess historical impact) |
| semantic_comparator | 120s | INCONCLUSIVE (cannot detect drift) |
| regime_validator | 30s | WARNING (assume regime stability) |
| projection_stabilizer | 60s | WARNING (assume stability) |

---

## Usage Workflow

```
1. Intake: Receive diff + changes + historical rules
2. Parse: diff_parser → get structured changes
3. Validate Scoring: weight_calculator + formula_evaluator → scoring score
4. Validate Regime: regime_validator → continuity score
5. Detect Drift: semantic_comparator → drift score
6. Test Projections: projection_stabilizer → stability score
7. Generate Report: Combine all scores + recommendations
8. Escalate if needed: Call Harness Optimizer on-demand
```

---

## Performance Characteristics

- **Typical runtime:** 10-15 minutes per validation cycle
- **Tool utilization:** ~70% of time in formula_evaluator and semantic_comparator
- **Memory peak:** ~500 MB when loading full historical dataset
- **API calls:** ~15-20 calls to external systems per cycle
