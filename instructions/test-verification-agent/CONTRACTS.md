# Test Verification Agent — I/O Specification

## Input Contract

### Required Artifacts

1. **implementation_contract.md**
   - File location: Produced by Implementation Agent
   - Schema: Test contract (what tests must cover, acceptance criteria per test)
   - Format: Markdown with sections:
     - Test objectives (high-level)
     - Test cases (ID, description, expected behavior)
     - Coverage floor (domain layers required to have 100% coverage)
     - Assumptions (dependencies, mocks, external systems)

2. **implementation.diff**
   - File location: Git diff or file patch
   - Content: All code changes in the implementation commit
   - Purpose: Extract test file additions/modifications

3. **test_results.json**
   - File location: Test runner output (pytest, jest, vitest, etc.)
   - Schema:
     `json
     {
       "passed": N,
       "failed": N,
       "skipped": N,
       "errors": [...],
       "duration_ms": N
     }
     `

4. **coverage_report.json**
   - File location: Coverage tool output (coverage.py, nyc, etc.)
   - Schema:
     `json
     {
       "total_lines": N,
       "covered_lines": N,
       "percent_covered": N,
       "by_file": [
         {"file": "path", "covered": N, "total": N, "percent": N}
       ]
     }
     `

---

## Output Contract

### Required Artifact

**test_audit_report.md**
- File location: Agent output directory
- Format: Markdown with sections below
- Audience: Implementation Agent, Verification Agent

### Report Schema

`markdown
# Test Audit Report

## Summary
- Status: PASS / FAIL / WARN
- Date: ISO timestamp
- Contract adherence: Y/N
- Cosmetic tests: N found
- Coverage inflation: N instances
- Critical paths covered: Y/N

## Findings by Category

### 1. Contract Validation
- [pass] All test objectives fulfilled
- [fail] Missing test case: {ID}
- [warn] Test incomplete: {ID} (missing error flow)

### 2. Cosmetic Test Detection
- [fail] Test {name}: No behavioral assertions (line N)
- [fail] Test {name}: Assertion-free mock setup (line N)

### 3. Redundancy Detection
- [warn] Test {name} duplicates {name} (same input/expected output)

### 4. Coverage Inflation
- [warn] {file}: {N} test-only imports inflate coverage
- [warn] Mock count inflates coverage by {N}%

### 5. Behavioral Coverage
- [pass] Happy path covered: {test_names}
- [fail] Error flow missing: {error_type}
- [warn] Edge case uncovered: {scenario}

### 6. Critical Path Assertions
- [pass] All critical paths have assertions
- [fail] Critical path {path} has no assertion
- [warn] Assertion too weak: {test_name} (mocking critical behavior)

## Recommendations

1. Resolve N failures (severity: CRITICAL)
2. Address N warnings (severity: MEDIUM)
3. Document N passes (no action required)

## Evidence

- Test file: {path}:{line}
- Coverage gap: Domain layer {layer} has {N}% coverage
- Contract gap: Test objective {obj} not fulfilled

---
`

### Report Rules

- Every finding must cite code (file + line number)
- Every failure must have a remediation path
- Every recommendation must be actionable
- Summary section must have pass/fail decision
