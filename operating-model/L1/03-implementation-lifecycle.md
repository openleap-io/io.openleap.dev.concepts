# L1 Spec 3 — Implementation Lifecycle

> **Document Type:** Platform Governance — Operating Model Detail (L1)
> **ID:** GOV-OM-L1-03
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — awaiting content
> **Author:** OpenLeap Architecture Team
> **Scope:** Scaffold → Code → Test mechanics for Backend services, UI applications, and BFFs; concrete agent-role catalog for implementation phase
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Owner (suggested):** Platform Owner + Domain Owner

---

## 1. Purpose

Operationalize phases Implement → Validate of GOV-OM-001 §4.1 across three implementation targets:
- **Backend service** (Java 25 / Spring Boot 4 per `dev.guidelines`)
- **UI application** (Vue 3 / Nuxt 4 per `ui.guidelines`)
- **BFF** (Backend-for-Frontend; existing `governance/bff-guideline.md`)

Define the concrete agent-role catalog for the Implement phase and the handoff rules between human developers and agents.

---

## 2. Scope

**In scope.**
- Scaffold mechanics per target (what templates, what seed files, what ADR instantiation)
- TDD discipline vs. tests-after pattern per target
- Agent-role definitions: Scaffold-Agent, Implementation-Agent, Test-Agent
- Human / agent handoff protocol (how an agent asks for human decision mid-task)
- Validate-Report schema — what MUST be in a validate report for Acceptance-Gate

**Out of scope.** Quality enforcement specifics (→ L1-4); build and release (→ L1-5).

---

## 3. Dependencies on L0

- GOV-OM-001 §4.1 — Phases Implement, Validate
- GOV-OM-001 §5.4 — Forward-link requirements from Code, Test-Result, Validate-Report
- GOV-OM-001 §7.3 — Agent-layer principles (produce/transform; never approve)
- GOV-OM-001 §8 — RACI rows for Implement, Validate

---

## 4. Dependencies on other L1 Specs

- L1-1 (Spec-Lifecycle) — Task-List format
- L1-2 (Version & Traceability) — `speckref` requirement, Task: trailer enforcement

---

## 5. Invariants derived from L0

- Phase 4 (Implement) has no exit-gate; control is in Validate (§4.1)
- Commit-level traceability via `Task:` trailer
- Validate-Report is the artifact presented at Acceptance-Gate; it carries compliance-evidence IDs

---

## 6. Open Questions

1. TDD mandated or encouraged? Evidence required at Acceptance-Gate?
2. Pair-programming / agent-pair-programming rules? Does the pair count as SoD in Team-Mode?
3. Coverage thresholds — domain-specific or platform-wide?
4. How does an Implementation-Agent request human decision? Comment-on-PR? Slack? Synchronous tool call?
5. Is there a "ready for review" marker on a PR that an agent may or may not set?
6. BFF-specific considerations from `governance/bff-guideline.md` — how does the Screen-Contract (AUI) drive BFF code generation?

---

## 7. Artifacts this L1 Registers in custody.md

- `Validate-Report` — schema finalization, retention policy
- Formalization of `Test-Result` (retention, access, ledger integration)

---

## 8. Agent Roles this L1 Introduces

- `Scaffold-Agent` — Spec + Task-List → Service / UI / BFF skeleton (templates, seed files, ADR stubs)
- `Implementation-Agent` — Task → Code (TDD cycle; one task at a time)
- `Test-Agent` — Test-Plan → Test-Cases; maintains coverage; runs locally before PR
- `Review-Agent` (optional) — pre-review of PR for ontology, style, obvious issues; presents findings, does not approve

---

## 9. CI Workflows this L1 Commissions

- Possible extension of `maven-build.yml` / `vue-build.yml` to emit Validate-Report
- Possible new: `validate-report-build.yml` — assembles a consolidated report at Acceptance-Gate

---

## 10. Completion Criteria

Per L1-roadmap.md §5.
