# L1 Spec 4 — Quality Control

> **Document Type:** Platform Governance — Operating Model Detail (L1)
> **ID:** GOV-OM-L1-04
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — awaiting content
> **Author:** OpenLeap Architecture Team
> **Scope:** Ontology enforcement, ADR adherence, code-review protocol, Quality Steward veto procedure
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Owner (suggested):** Quality Steward

---

## 1. Purpose

Operationalize the Quality Steward role (GOV-OM-001 §7.1 #7) across all gates where QS appears as Accountable or Veto. Specify the mechanics by which ontology violations, ADR non-adherence, template violations, and compliance gaps become machine-detectable auto-rejects (§6.6), and define the human workflow when auto-reject is not achievable.

---

## 2. Scope

**In scope.**
- Ontology-enforcement mechanics (dev.guidelines for backend, ui.guidelines for frontend)
- ADR-adherence checks (how "the ADR says X, the code does Y" is detected)
- Code-review protocol distinguishing QS-required reviews from Domain-Owner/Platform-Owner reviews
- QS-veto procedure at Prod-Deploy-Gate (who notifies whom, how is veto recorded, how is it appealed)
- Compliance-Evidence schema — the object referenced by Validate-Report forward-links

**Out of scope.** Security-specific compliance (→ L1-8); template content (→ L1-1).

---

## 3. Dependencies on L0

- GOV-OM-001 §6.4 — Spec-Gate, Design-Gate, Acceptance-Gate, Prod-Deploy-Gate approvers
- GOV-OM-001 §6.6 — Auto-reject on Ontology-Violation, Template-Violation
- GOV-OM-001 §7.1 — Quality Steward role definition
- GOV-OM-001 §8 — RACI cells where QS is A or has Veto

---

## 4. Dependencies on other L1 Specs

- L1-1 (Spec-Lifecycle) — template and ontology evolution
- L1-3 (Implementation) — Validate-Report schema

---

## 5. Invariants derived from L0

- QS is Enforcer, not Producer — never has R except in CVE-Patch post-hoc (§8)
- QS veto at Prod-Deploy stops deploy regardless of RA/SA approval (§6.4)
- Auto-rejects are agent-performed before presentation at gate (§6.6)

---

## 6. Open Questions

1. Which tool runs ontology checks? Static analyzer? Custom linter? Existing tooling in dev.guidelines?
2. How is ADR-adherence checked — code scan? Dependency-inversion test? Manual review only?
3. What triggers an "exception request" when a developer needs to violate an ontology rule?
4. QS-veto at Prod-Deploy: must be machine-invokable (blocks deploy workflow) or manual (requires coordination)?
5. How is QS notified of upcoming Prod-Deploys to have time to review?
6. Compliance-Evidence: structured JSON or free-form attached docs?

---

## 7. Artifacts this L1 Registers in custody.md

- `Compliance-Evidence` (schema, retention, linkage)
- Formalization of `Validate-Report` §Compliance section

---

## 8. Agent Roles this L1 Introduces

- `Ontology-Check-Agent` — runs rules from dev.guidelines against a PR; outputs findings
- `ADR-Adherence-Agent` — verifies code honors its ADRs
- `Compliance-Aggregator-Agent` — assembles Compliance-Evidence for Acceptance-Gate and Prod-Deploy-Gate

---

## 9. CI Workflows this L1 Commissions

- `ontology-check.yml` — runs ontology rules on PR
- `adr-adherence-check.yml` — runs ADR-based checks
- Possible extension of `dora-metrics.yml` to emit gate-reject-rate metric

---

## 10. Completion Criteria

Per L1-roadmap.md §5.
