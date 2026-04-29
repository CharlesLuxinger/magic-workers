# magic-workers: Agent Specification Guide

## Repository Purpose

This repository contains operational instructions for **magic-workers**, a deterministic harness engine that transforms raw product intent into bounded, verified feature deliverables.

Each agent type has its own instruction directory under `instructions/{agent-name}/` with complete role, contract, execution rules, and tooling specs.

---

## Agent Types & Hierarchy

```text
Human Operator (feature request)
    ↓
Chief of Staff Agent           [scope gatekeeping, intent formalization]
    ↓
Product Spec Agent             [ambiguity elimination, bounded context definition]
    ↓
Architecture & Test Design Guard Agent  [architecture enforcement, TDD contract]
    ↓
Implementation Agent           [Red-Green-Refactor execution, sacred-main discipline]
    ↓
Test Verification Agent        [test quality & coverage validation]
    ↓
Semantic Verification Agent    [domain rule & contract verification]
    ↓
Domain Analytics Agent         [conditional: business metric & domain health]
    ↓
Harness Optimizer Agent        [on-demand: system efficiency & pattern learning]
```

---

## Core Characteristics

**Determinism First:** Each agent receives structured input, not raw intent. No downstream agent receives unformatted requests.

**TDD Mandatory:** Red-Green-Refactor is required. No implementation proceeds without test definition. Coverage minimums enforced (domain/app: 95%, global: 90%, critical adapters: 85%).

**Scope Discipline:** Features enter as single, bounded deliverables. No scope expansion without explicit re-routing through Chief of Staff.

**Sacred-Main:** Implementation commits only via pull request. main is immutable from agent perspective. No direct push or self-merge.

**Architecture Guardrails:** Hexagonal boundaries enforced. Domain invariants protected. Framework leakage prevented. Mocking policy strict (only outbound ports/gateways; never entities/aggregates/value objects).

---

## Finding Instructions

Each agent's directory contains six files:

- **AGENTS.md** — Role, objectives, responsibilities, position in hierarchy
- **HEARTBEAT.md** — Execution loop, state machine, interaction triggers
- **CONTRACTS.md** — Input/output schemas, validation rules, format requirements
- **SOUL.md** — Behavioral principles, decision-making patterns, identity
- **TOOLS.md** — Available capabilities, API methods, system integrations
- **RULES.md** — Hard constraints, forbidden actions, mandatory practices

**Reading order for new agents:**  
Start with `AGENTS.md` (role clarity) → `HEARTBEAT.md` (how work flows) → `CONTRACTS.md` (I/O specifics) → `RULES.md` (what breaks the system) → `TOOLS.md` (how to execute) → `SOUL.md` (behavioral nuance).

---

## Agent Quick Reference

| Agent | Entry Point | Output | Key Constraint |
|-------|-------------|--------|-----------------|
| **Chief of Staff** | Human intent | Scoped execution directive | Strategic control only; no architecture |
| **Product Spec** | Raw feature request | feature_spec.md | Formalization only; no implementation proposal |
| **Architecture Guard** | Feature specification | implementation_contract.md | TDD mandate; hexagonal enforcement |
| **Implementation** | Implementation contract | Feature branch + PR | Red-Green-Refactor discipline; main read-only |
| **Test Verification** | Implementation patch | Test report | Coverage gates; quality gates |
| **Semantic Verification** | Passing tests | Verification report | Domain rule validation; contract compliance |
| **Domain Analytics** | *(conditional)* | Domain health report | Metric extraction; pattern analysis |
| **Harness Optimizer** | *(on-demand)* | System recommendations | Efficiency optimization; learning capture |

---

## System Principles

1. **No Ambiguity Downstream** — Upstream agents eliminate ambiguity. Downstream agents execute with confidence.

2. **Single Feature Flow** — One feature at a time. Parallel streams possible, but each isolated. Chief of Staff sequences entry.

3. **Bounded Scope is Sacred** — Features defined with explicit IN/OUT scope. Scope drift triggers re-routing to Chief of Staff.

4. **Contract-Driven Execution** — Each agent receives explicit input contract. Work proceeds only within contract boundaries.

5. **Verification After Implementation** — Implementation is not final. Test + Semantic verification gates release. Main is protected.

6. **Execution Record Durable** — Every agent leaves durable progress. Task comments, documents, or work products. Next action is always clear.

---

## Getting Started

1. **Identify your agent type** from the hierarchy above.
2. **Read your agent's instructions** in `instructions/{your-agent-name}/`.
3. **Start with AGENTS.md** in that directory for role clarity.
4. **Check HEARTBEAT.md** for execution loop and trigger conditions.
5. **Verify CONTRACTS.md** for exact input/output formats.
6. **Review RULES.md** before taking action.

---

## Common Patterns

- **Features do not bypass Chief of Staff.** Even urgent work enters through scope gatekeeping.
- **Pull requests are immutable once opened.** Respond to every review comment; never push-force-and-merge.
- **Test first, code second.** Red phase defines contract. Green phase fulfills it. Refactor phase improves it.
- **Mocking is strict.** Only outbound ports and gateways. Never mock core domain.
- **Coverage is enforced.** 90% global, 95% domain/app, 85% critical adapters. Below threshold = implementation invalid.

---

## Questions or Clarifications

Refer to the specific agent's instruction directory. All edge cases, behavioral details, and system integrations are documented in their RULES.md or SOUL.md.
