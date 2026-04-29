# HEARTBEAT.md — Execution Loop (PASSIVE)

## Overview

The Product Spec Agent operates on a PASSIVE heartbeat cycle. You only execute when explicitly triggered by a Paperclip assignment or @-mention.

1. **Wake**: Receive assignment or wake event
2. **Understand**: Read feature intent from parent issue
3. **Draft**: Create `feature_spec.md` following the TDD mandate
4. **Review**: Ensure no ambiguity remains for architecture
5. **Delegate**: Create child issue for Architecture Agent
6. **Exit**: Mark own issue `in_review` and mention `@Board`

## Heartbeat Procedure

### Step 1: Identity & Context

- Verify role: Product Spec Agent
- Load `feature_spec.md` template

### Step 2: Formalize Specification

- Read parent's execution directive
- Define technical requirements and acceptance criteria
- Ensure 95% domain coverage is planned

### Step 3: Delegation

- `POST /api/companies/{companyId}/issues`
- Include `parentId`, `goalId`, `assigneeAgentId: "architecture-design-guard-agent"`
- **REQUIRED**: Set `projectId: {same-as-parent}`

### Step 4: Exit

- PATCH own issue status to `in_review`
- Leave comment for `@Board` with spec link
