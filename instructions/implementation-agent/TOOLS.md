# TOOLS.md - Available Capabilities

## Overview

Implementation Agent 2 has access to a comprehensive toolkit for implementing features under contract.

## Tool Categories

### Code Editing & Analysis

| Tool | Purpose | Usage |
|------|---------|-------|
| `lsp_diagnostics` | Type check and error detection | Before and after code changes |
| `lsp_goto_definition` | Jump to symbol definition | Understanding existing code |
| `lsp_find_references` | Find all usages of a symbol | Impact analysis before changes |
| `lsp_symbols` | Get symbols from file/workspace | Finding functions, classes, exports |
| `lsp_prepare_rename` | Validate rename operation | Safe refactoring |
| `lsp_rename` | Rename symbol across workspace | Refactoring after tests pass |
| `ast_grep_search` | AST-aware pattern search | Finding code patterns |
| `ast_grep_replace` | AST-aware pattern replacement | Bulk refactoring |

### File Operations

| Tool | Purpose | Usage |
|------|---------|-------|
| `read` | Read file contents | Understanding existing code |
| `write` | Create or overwrite file | Writing tests and implementation |
| `edit` | Line-based edits to file | Surgical code changes |
| `glob` | Find files by pattern | Locating related files |
| `grep` | Search file contents | Finding specific patterns |
| `filesystem_list_directory` | List files in directory | Navigation |
| `filesystem_search_files` | Recursive file search | Finding related code |

### Execution & Testing

| Tool | Purpose | Usage |
|------|---------|-------|
| `bash` | Execute shell commands | Run tests, build, linting |
| (use `rtk` prefix) | Token optimization | Reduce output verbosity |

### Git & Version Control

| Tool | Purpose | Usage |
|------|---------|-------|
| `bash` with `git` | Commit, branch, diff | Version control operations |

### Verification & Quality

| Tool | Purpose | Usage |
|------|---------|-------|
| `lsp_diagnostics` | Type safety check | End of implementation phase |
| `bash` with test runner | Unit/integration tests | Red-green-refactor cycle |

---

## Typical Workflow

### Phase 1: RED (Write Tests)

```bash
1. read - Read contract + existing code
2. write - Create test file with failing tests
3. bash - Run tests (expect failure)
4. read - Verify test file is correct
5. bash rtk test - Check coverage (should be 0%)
```

### Phase 2: GREEN (Write Implementation)

```bash
1. write - Create implementation file
2. bash rtk test - Run tests (expect pass)
3. lsp_diagnostics - Check for type errors
4. bash rtk test - Final verification
```

### Phase 3: REFACTOR (Clean Up)

```bash
1. lsp_find_references - Understand usage
2. ast_grep_search - Find similar patterns
3. edit - Apply refactoring based on existing patterns
4. lsp_diagnostics - Verify no type errors introduced
5. bash rtk test - Verify tests still pass
6. bash rtk test - Final coverage check
```

### Phase 4: VERIFY (Acceptance)

```bash
1. read - Compare against contract acceptance criteria
2. bash rtk test - Full test suite
3. lsp_diagnostics - Final type safety
4. write - Generate implementation patch
```

---

## Tool Usage Rules

### File Reading

```bash
# Single file
read("src/auth/login.ts")

# Multiple files in parallel
read(["src/auth/login.ts", "src/auth/refresh.ts"])

# Search for similar files
glob("src/auth/**/*.ts")
```

### Code Editing

```bash
# Use edit for precise line changes (prefer this for small changes)
edit("src/auth/login.ts", 
     oldString="const result = await auth();",
     newString="const result = await auth();\n  log.info('Auth completed');")

# Use write for new files
write("src/auth/refresh.test.ts", "import...")

# Use ast_grep_replace for bulk pattern changes
ast_grep_replace(
    pattern="console.log($MSG)",
    rewrite="logger.info($MSG)",
    lang="typescript"
)
```

### Testing

```bash
# Run tests (use rtk prefix for token savings)
bash("rtk npm test")

# Run specific test file
bash("rtk npm test -- src/auth/refresh.test.ts")

# Check coverage
bash("rtk npm test -- --coverage")

# Run with watch mode (interactive)
bash("npm test -- --watch")
```

### Type Checking

```bash
# Check specific file
lsp_diagnostics("src/auth/refresh.ts")

# Check whole directory
lsp_diagnostics("src/auth/")

# Filter by severity
lsp_diagnostics("src/auth/", severity="error")
```

### Finding Code

```bash
# Find usages of a function
lsp_find_references("src/auth/login.ts", line=15, character=8)

# Find pattern across codebase
ast_grep_search(pattern="throw new Error($MSG)", lang="typescript")

# Find files by pattern
glob("src/**/*.test.ts")

# Search content
grep("async function.*login", "src/auth/")
```

---

## Anti-Patterns (Never Do)

| ❌ Anti-Pattern | ✅ Correct Approach |
|---|---|
| Use `as any` to suppress type error | Fix the underlying type issue |
| Skip tests to "save time" | Follow red-green-refactor strictly |
| Add features "while you're here" | Respect contract scope exactly |
| Ignore lsp_diagnostics warnings | Fix all type errors before done |
| Use `//@ts-ignore` or `//@ts-expect-error` | Resolve the type issue properly |
| Leave empty catch blocks | Handle errors explicitly |
| Inline all logic without helper functions | Extract functions for clarity |
| Commit without tests passing | Always verify tests pass first |
| Refactor during bug fix | Fix bugs minimally, refactor separately |

---

## Token Optimization (RTK)

Always prefix commands with `rtk` to reduce output verbosity:

```bash
# ❌ 5000 tokens output
npm test

# ✅ 500 tokens output
rtk npm test

# Works with git too
rtk git status
rtk git log
rtk git diff
```

Typical savings: 60-90% token reduction on build/test output.

---

## When to Escalate

If you cannot make progress due to any of these, escalate to CEO:

- ❌ Tool not working as expected
- ❌ Dependency conflicts prevent testing
- ❌ Configuration errors in project
- ❌ Permission denied errors
- ❌ Environment variable issues
- ❌ Architectural constraint violation detected
- ❌ Contract ambiguity discovered

Format: Create issue with type `blocked` and escalation reason.
