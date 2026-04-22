# L1 Spec 2 — Version & Traceability

> **Document Type:** Platform Governance — Operating Model Detail (L1)
> **ID:** GOV-OM-L1-02
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — awaiting content
> **Author:** OpenLeap Architecture Team
> **Scope:** SemVer rules per artifact class operationalized; CHANGELOG format; workflow contracts for `spec-integrity-check` and `traceability-check`
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Owner (suggested):** Platform Owner

---

## 1. Purpose

Operationalize the versioning rules in GOV-OM-001 §5.3 and the mandatory forward-link structure in §5.4. Provide the concrete CI workflow contracts that mechanize auto-reject on traceability gaps (§6.6) and custody violations.

---

## 2. Scope

**In scope.**
- Operational SemVer rule per class, including the precise detection of "breaking" (schema diff tools, contract-bump automation, UVL diff for feature removal)
- `speckref` commit-trailer syntax and enforcement
- CHANGELOG format per repo class (service, library, UI, workflow)
- Deployment-Record schema (the JSON/YAML shape a record must have at deploy-gate)
- `spec-integrity-check.yml` contract: inputs, hash algorithm, diff reporting
- `traceability-check.yml` contract: inputs, commit-trailer parsing, deployment-record validation
- SPLE-specific metric collection (beyond DORA) — feature-adoption, variability-coverage, spec-drift

**Out of scope.** DORA metric collection (handled by existing `dora-metrics.yml`); issue-tracker integration (→ L1-7).

---

## 3. Dependencies on L0

- GOV-OM-001 §5.3 — Versioning rules
- GOV-OM-001 §5.4 — Mandatory forward-links
- GOV-OM-001 §5.5 — Custody rule (vendored-copy verification)
- GOV-OM-001 §6.6 — Auto-reject conditions
- GOV-OM-001 §9.2 — Mandated new workflows

---

## 4. Dependencies on other L1 Specs

- L1-1 (Spec-Lifecycle) — Spec ID stability, ADR append-only semantics

---

## 5. Invariants derived from L0

- Every commit that is part of a deployable change carries `Task:` trailer
- Every CHANGELOG entry references `speckref: <spec-id>@<version>`
- Vendored copies fail build on hash mismatch
- Deployment-Record is immutable and carries full forward-link chain (§5.4)

---

## 6. Open Questions

1. Tool for breaking-contract detection (OpenAPI diff, AsyncAPI diff)?
2. Tool for UVL semantic diff?
3. CHANGELOG format: Keep a Changelog vs. Conventional Commits vs. custom?
4. Where do SPLE-specific metrics live? Prometheus/Grafana? A ledger repo? Export to the same DORA pipeline?
5. How are spec-drift events detected (scheduled re-hash? PR-check?) and who is informed?

---

## 7. Artifacts this L1 Registers in custody.md

- `Metric-Snapshot` (SPLE-specific variant location)
- Formalization of existing entries (Code, Build-Artifact, Image) with the exact CHANGELOG-referencing mechanism

---

## 8. Agent Roles this L1 Introduces

- `Version-Bump-Agent` — analyzes PR diff, proposes SemVer bump, updates CHANGELOG with `speckref`
- `Traceability-Check-Agent` — runs the `traceability-check.yml` logic (may be a pure CI job rather than an agent role, to be decided here)

---

## 9. CI Workflows this L1 Commissions

- `spec-integrity-check.yml` (implementation; contract defined by this L1)
- `traceability-check.yml` (implementation; contract defined by this L1)
- Optional: `contract-diff.yml` — schema diff tool for OpenAPI/AsyncAPI at PR time, feeding breaking-change classification

---

## 10. Completion Criteria

Per L1-roadmap.md §5.
