# L1 Spec 1 — Spec-Lifecycle & Template-Governance

> **Document Type:** Platform Governance — Operating Model Detail (L1)
> **ID:** GOV-OM-L1-01
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — awaiting content
> **Author:** OpenLeap Architecture Team
> **Scope:** Mechanics of authoring specs, reviewing specs, evolving templates, adding new artifact classes, and catalog curation
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Owner (suggested):** Architect

---

## 1. Purpose

Operationalize phases Conceive → Specify → Design of GOV-OM-001 §4.1, plus the meta-activities of evolving the `dev.concepts/templates/` catalog, the `dev.guidelines` ontology, and the `artifact-catalog.md` taxonomy. Extends `governance/template-governance.md` (GOV-TPL-001) with role-explicit RACI and gate mechanics.

---

## 2. Scope

**In scope.**
- Intent → Spec authoring workflow (who drafts, who reviews, what artifacts are produced by each gate)
- Template evolution (adding/modifying templates in `dev.concepts/templates/`)
- ADR catalog evolution (adding/superseding ADRs in `dev.guidelines/ADR/` and per-service ADR folders)
- Ontology evolution (the 2137+ rules in `dev.guidelines`; the UI counterparts in `ui.guidelines`)
- New artifact class process — how a new entry joins `artifact-catalog.md` and `operating-model/custody.md`
- Spec-Authoring-Agent, Spec-Review-Agent, Design-Agent role definitions and gate participation

**Out of scope.** Implementation of specs (→ L1-3), test and compliance enforcement during Validate (→ L1-4).

---

## 3. Dependencies on L0

- GOV-OM-001 §4.1 — Phases Conceive, Specify, Design
- GOV-OM-001 §5.2 — Identity requirement for artifacts
- GOV-OM-001 §5.4 — Forward-link requirements from Spec, UVL, ADR, Contract, Task-List
- GOV-OM-001 §6.1 — Architectural and Tactical decision classes
- GOV-OM-001 §6.4 — Intent-Gate, Spec-Gate, Design-Gate, Meta-Gate
- GOV-OM-001 §8 — RACI rows for Conceive, Specify, Design, Meta-Change
- GOV-OM-001 §13 — Amendment process for meta-artifacts

---

## 4. Dependencies on other L1 Specs

None (this is the foundation L1).

---

## 5. Invariants derived from L0

- Spec-Gate cannot pass without Quality Steward approval of template + ontology conformance (§6.4)
- New artifact class requires Meta-Gate approval AND custody entry before production use (§5.5)
- ADRs are append-only (§5.3)
- In Team-Mode, Spec-Author ≠ Approving Architect ≠ Quality Steward (§6.3)

---

## 6. Open Questions

1. What concrete intent-registry tool is used? Markdown per spec folder vs. central ledger?
2. How is spec ID stability enforced across renames?
3. Which tool (if any) automates template-conformance checks before Spec-Gate presentation?
4. What is the trigger and cadence for re-certifying older specs against evolved templates (template-drift handling)?
5. Who drafts an Amendment-ADR to the ontology — the Quality Steward (enforcer) or the Architect (evolver) or a third party?

---

## 7. Artifacts this L1 Registers in custody.md

- `Task-List` — format and file location decision
- Optional: `Intent-Registry` (if central-ledger path chosen over per-folder)

---

## 8. Agent Roles this L1 Introduces

- `Discovery-Agent` (converts raw input to candidate Intent)
- `Spec-Authoring-Agent` (Intent → Spec draft)
- `Spec-Review-Agent` (template/ontology/traceability pre-check before Spec-Gate)
- `Design-Agent` (Spec → ADRs, Contracts, Task-List)

For each, §7.3 of L0 requires: input artifact, output artifact, phase, gate participation, auto-reject checks, implementation choice, defined-in reference.

---

## 9. CI Workflows this L1 Commissions

- Extension of `spec-integrity-check.yml` to cover spec-template conformance (not just hash verify)
- Optional: `ontology-check.yml` — runs guideline ontology checks on draft specs before Spec-Gate

---

## 10. Completion Criteria

Per L1-roadmap.md §5: Scope / Dependencies / Invariants fully specified; all Open Questions resolved; custody entries + agent-role entries committed; any new reusable workflows added; Meta-Gate approval obtained.
