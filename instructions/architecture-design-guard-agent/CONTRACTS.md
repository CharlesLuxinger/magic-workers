# Contracts: Architecture & Test Design Guard Agent

## Input Contract

### Feature Specification (from Product Spec Agent)

Required fields:
- **feature_name**: String. Human-readable feature name.
- **user_story**: String. Format: "As a [role], I want [capability], so that [value]"
- **acceptance_criteria**: Array of strings. Concrete, testable criteria.
- **domain_entity**: String. Which domain entity/aggregate is affected?
- **package_scope**: Array. Which packages contain the implementation?
- **existing_rules**: Array. Existing domain invariants that apply.
- **new_rules**: Array. New business rules introduced by this feature.

### Architecture Context (Company-wide)

Required:
- Hexagonal architecture definition
- Domain layer boundaries
- Package map
- Prohibited patterns
- Approved testing frameworks

## Output Contract

### artifact/architecture_report.md

**Purpose**: Document validation results

**Contents**:
1. Specification Summary
   - Feature name, scope, affected entities
2. Validation Results
   - Architecture boundary check: PASS/FAIL
   - Domain rule validation: PASS/FAIL
   - Package scope validation: PASS/FAIL
3. Identified Risks
   - Each risk includes: description, severity (critical/high/medium), mitigation
4. Decisions
   - Which layer gets which rules
   - Package organization decisions
5. Exceptions (if any)
   - Any architecture rule violations
   - Required approvals
6. Test Strategy Overview
   - Test categories
   - Coverage thresholds
   - Mocking policy

### artifact/implementation_contract.md

**Purpose**: Define how implementation must happen

**Contents**:
1. Domain Rules by Layer
   `
   ## Domain Layer
   - Rule: [description]
   - Where: [file/package]
   - Test: [test name]
   - Coverage: [threshold]
   
   ## Application Layer
   - [same structure]
   
   ## Adapter Layer
   - [same structure]
   `

2. Test Execution Order
   `
   1. Domain unit tests (Red)
   2. Domain unit tests pass (Green)
   3. Domain tests refactored (Refactor)
   4. Application tests (Red)
   5. ... (Green, Refactor)
   6. Integration tests
   7. Contract tests
   `

3. Coverage Contract
   - Global minimum: 90%
   - Domain/Application minimum: 95%
   - Critical adapters minimum: 85%

4. Mocking Policy
   - Allowed: outbound ports, gateways, clocks, auth/license
   - Forbidden: entities, aggregates, value objects, business rules

5. Red Flags (implementation must not proceed without approval if any exist)
   - Architecture violations
   - Missing critical tests
   - Insufficient coverage targets

## Validation Rules

### Architecture Validation

- [ ] No business logic in adapters
- [ ] No framework imports in domain
- [ ] No package leakage (lower layers don't depend on higher)
- [ ] All domain invariants are enforceable
- [ ] All new rules map to a layer
- [ ] No circular dependencies

### Test Validation

- [ ] Every business rule has a test
- [ ] Test names match the rule they test
- [ ] Coverage thresholds are achievable
- [ ] Mocking policy is enforced
- [ ] TDD order is clear

## Acceptance Criteria

Implementation may **only proceed** when:

- [ ] Architecture Report is complete and signed off
- [ ] Implementation Contract is produced and linked
- [ ] All architecture violations are resolved or approved
- [ ] All test coverage thresholds are defined
- [ ] Implementation Agent has acknowledged the contract
