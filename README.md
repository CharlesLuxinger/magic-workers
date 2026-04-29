# Magic Workers - Harness Engine

A deterministic harness engine that transforms raw product intent into bounded, verified feature deliverables through a pipeline of specialized agents.

---

## Quick Start

**New to this repo?** Read in this order:

1. **[AGENTS.md](./AGENTS.md)** — System overview, agent hierarchy, core principles (5 min)
2. **Your agent's directory** — Role-specific instructions (see "Finding Your Role" below)

**Need to execute work?** Go directly to your agent's `HEARTBEAT.md` (execution loop).

---

## Repository Structure

```text
betrader-agents-specs/
├── AGENTS.md                                ← Start here (system overview)
├── README.md                                ← You are here
└── instructions/
    ├── chief-of-staff-agent/               [Strategic control layer]
    ├── product-spec-agent/                 [Feature formalization]
    ├── architecture-design-guard-agent/    [Architecture + TDD contract]
    ├── implementation-agent/               [Red-Green-Refactor execution]
    ├── test-verification-agent/            [Coverage & quality gates]
    ├── semantic-verification-agent/        [Domain rule validation]
    ├── domain-analytics-agent/             [Business metrics (conditional)]
    └── harness-optimizer-agent/            [System efficiency (on-demand)]
```

Each agent directory contains:

- **AGENTS.md** — Role, responsibilities, system position
- **HEARTBEAT.md** — Execution loop and trigger conditions
- **CONTRACTS.md** — Input/output specs and validation
- **SOUL.md** — Behavioral principles and decision patterns
- **TOOLS.md** — Available capabilities and APIs
- **RULES.md** — Hard constraints and forbidden actions

---

## Finding Your Role

| You are... | Start here | Then read |
|-----------|-----------|-----------|
| **Strategic lead / manager** | `chief-of-staff-agent/AGENTS.md` | HEARTBEAT → CONTRACTS |
| **Product manager / analyst** | `product-spec-agent/AGENTS.md` | HEARTBEAT → CONTRACTS |
| **Architect** | `architecture-design-guard-agent/AGENTS.md` | HEARTBEAT → RULES → CONTRACTS |
| **Developer** | `implementation-agent/AGENTS.md` | HEARTBEAT → RULES → CONTRACTS |
| **QA / test engineer** | `test-verification-agent/AGENTS.md` | HEARTBEAT → CONTRACTS |
| **Domain expert** | `semantic-verification-agent/AGENTS.md` | HEARTBEAT → CONTRACTS |
| **System observer** | `harness-optimizer-agent/AGENTS.md` | HEARTBEAT |

---

## Core Principles

1. **No ambiguity downstream** — Each upstream agent eliminates ambiguity before passing to the next.
2. **One feature at a time** — Features enter through Chief of Staff, exit with verification.
3. **Bounded scope is sacred** — All features have explicit IN/OUT scope boundaries.
4. **Contract-driven execution** — Each agent receives a detailed execution contract, not vague requests.
5. **Test before code** — Red-Green-Refactor is mandatory; TDD is enforced.
6. **Main is protected** — All implementation flows through pull request; direct pushes forbidden.

---

## The Feature Pipeline

```text
Human Intent
    ↓ (Chief of Staff)
Execution Directive
    ↓ (Product Spec)
Feature Specification
    ↓ (Architecture Guard)
Implementation Contract
    ↓ (Implementation)
Feature Branch + PR
    ↓ (Test Verification)
Coverage Report
    ↓ (Semantic Verification)
Domain Health Report
    ↓ (optional: Domain Analytics)
System Recommendations
    ↓ (optional: Harness Optimizer)
Main Branch ← Protected
```

---

## Key Constraints

- **TDD mandatory**: No code without tests first. Red → Green → Refactor.
- **Coverage floors**: Global 90%, Domain/App 95%, Critical Adapters 85%.
- **Hexagonal architecture**: Domain isolation, no framework leakage, strict mocking policy.
- **Sacred-main discipline**: Feature branches only. Pull requests required. No self-merge.
- **Scope discipline**: Expansion requires re-routing through Chief of Staff.

---

## Where to Find Specific Information

| Question | Answer Location |
|----------|-----------------|
| What does my agent do? | `instructions/{agent}/AGENTS.md` |
| How do I execute my role? | `instructions/{agent}/HEARTBEAT.md` |
| What inputs do I receive? | `instructions/{agent}/CONTRACTS.md` |
| What's forbidden? | `instructions/{agent}/RULES.md` |
| What tools do I have? | `instructions/{agent}/TOOLS.md` |
| How should I behave? | `instructions/{agent}/SOUL.md` |
| System overview? | `AGENTS.md` (root) |

---

## Getting Help

- **Role confusion?** Read [AGENTS.md](./AGENTS.md) hierarchy section.
- **Execution questions?** Check your agent's `HEARTBEAT.md`.
- **Format/validation issues?** See your agent's `CONTRACTS.md`.
- **Constraints/forbidden actions?** Check your agent's `RULES.md`.
- **System principles?** Review [AGENTS.md](./AGENTS.md) section "System Principles".

---

## For Future Sessions

This repository is designed for **durable agent context**. When you return:

1. Your agent role is defined in `instructions/{your-agent}/AGENTS.md`
2. Your execution loop is in `HEARTBEAT.md`
3. Your input/output contracts are in `CONTRACTS.md`
4. Your constraints are in `RULES.md`

No ambiguity. No guessing. Every session, same clarity.
