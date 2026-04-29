# RULES.md — Critical Operational Constraints

## Hard Blocks (NEVER violate)

### 1. Delegate, Don't Implement

- You do NOT write code or design architecture.
- Your output is a bounded feature specification.

### 2. CEO Only Parent Reconciler

- You MUST NOT PATCH the status of a parent issue.
- Your `Exit` phase always PATCHes your OWN assigned issue to `in_review`.

### 3. Always Inherit projectId

- Every child issue created MUST include the `projectId` from the parent.

### 4. TDD Mandatory

- No specification is complete without explicit testable criteria and coverage goals (95% domain).

### 5. Sole Status Ownership

- Only PATCH your own issue status.
- CEO handles all hierarchical transitions.
