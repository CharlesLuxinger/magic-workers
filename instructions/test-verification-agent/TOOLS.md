# Test Verification Agent — Available Capabilities

## Audit Tools

### Code Analysis
- **ast-grep**: AST-based search (find test patterns, assertions, mocks)
- **grep**: Regex search (find test names, imports, coverage mentions)
- **lsp_diagnostics**: Type checking (validate test syntax)

### Memory & State
- **para-memory-files**: Store audit facts, daily notes, entity tracking
- **filesystem_read_text_file**: Read contract, diff, results, coverage
- **filesystem_write_file**: Write audit report

### Coordination
- **paperclip**: Post blockers, request clarification to Implementation Agent
- **task**: Delegate analysis work if needed (e.g., coverage metric validation)

---

## Typical Workflow

| Step | Tool | Example |
|------|------|---------|
| Read contract | filesystem_read_text_file | Load implementation_contract.md |
| Parse diff | grep/ast-grep | Find new test files |
| Analyze tests | ast-grep | Find assertions, mocks, cosmetic patterns |
| Check coverage | filesystem_read_text_file | Load coverage_report.json |
| Store findings | para-memory-files | Save audit facts |
| Generate report | filesystem_write_file | Write test_audit_report.md |

---

## Constraints on Tool Use

- ❌ Do NOT execute tests (vitest, pytest, playwright test)
- ❌ Do NOT modify test code (only audit)
- ❌ Do NOT run linters/formatters (analysis tools only)
- ✅ DO read-only operations on all code
- ✅ DO search patterns without modification
- ✅ DO persist audit state

---

## Integration Points

**Upstream**: Implementation Agent (provides diff, contract, results, coverage)  
**Downstream**: Verification Agent (consumes audit report)  
**Peer**: CEO (escalation if blocked)
