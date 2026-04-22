# L1 Spec 6 — Deployment

> **Document Type:** Platform Governance — Operating Model Detail (L1)
> **ID:** GOV-OM-L1-06
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — awaiting content
> **Author:** OpenLeap Architecture Team
> **Scope:** Environment graph, promotion rules, non-prod vs prod gate mechanics, rollback protocol, Deployment-Record ledger
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Owner (suggested):** Release Authority

---

## 1. Purpose

Operationalize phase Deploy of GOV-OM-001 §4.1. Define the environment graph (dev / test / staging / prod / per-tenant), the promotion rules between environments, and the mechanics of the Prod-Deploy-Gate including the three-approver requirement (RA + SA + QS veto). Complete the Deployment-Record ledger tool decision (custody open item).

---

## 2. Scope

**In scope.**
- Environment graph and naming conventions
- Promotion rules from env-to-env (automatic / approval-gated)
- Non-Prod-Deploy-Gate mechanics (single approver)
- Prod-Deploy-Gate mechanics (RA + SA accountable shared, QS veto, three distinct persons in Team-Mode)
- Rollback protocol (automatic vs. manual; linkage to CVE-Fast-Path Auto-Rollback → L1-8)
- Deployment-Record schema, ledger tool selection, immutability enforcement
- Rollout strategies (canary, blue-green, progressive) mapped to environment promotion rules

**Out of scope.** Build and publish (→ L1-5); CVE-specific emergency deploy (→ L1-8).

---

## 3. Dependencies on L0

- GOV-OM-001 §4.1 — Phase Deploy
- GOV-OM-001 §5.4 — Deployment-Record forward-link requirements (Image + Env + Approvers + UVL-Binding)
- GOV-OM-001 §6.4 — Non-Prod-Deploy-Gate, Prod-Deploy-Gate definitions
- GOV-OM-001 §6.3 — SoD requirements (three distinct persons for Prod in Team-Mode)
- GOV-OM-001 §7.1 — Release Authority + Security Authority + Quality Steward roles
- GOV-OM-001 §8 — RACI row for Deploy prod

---

## 4. Dependencies on other L1 Specs

- L1-5 (Build & Release) — upstream artifact (Image) and its forward-links

---

## 5. Invariants derived from L0

- Prod-Deploy requires RA **and** SA approval with QS veto available (§6.4)
- In Team-Mode, three distinct persons for Prod (§6.3)
- Every deploy produces an immutable Deployment-Record with full forward-link chain (§5.4)
- No implicit skip of Deploy gate; no emergency bypass except CVE-Fast-Path (§6.5)

---

## 6. Open Questions

1. Deployment-Record ledger location: `io.openleap.k8s/deployments/ledger/`? Own repo? External system?
2. Canonical environment list and promotion graph — which envs are auto-promoted, which gate-promoted?
3. GitOps or push-based deploys? Which tool(s)?
4. Per-tenant deploys: own environment class or tenant-tag in the prod env?
5. Rollback mechanics: how does an auto-rollback (CVE-Fast-Path) find the previous good state?
6. Time-of-day / business-hours rules for prod deploys?

---

## 7. Artifacts this L1 Registers in custody.md

- `Deployment-Record` — tool choice and ledger format finalized

---

## 8. Agent Roles this L1 Introduces

- `Deploy-Agent` — applies manifests to env, creates Deployment-Record
- `Promotion-Agent` — moves image from env to env per rules (possibly CI-job rather than agent)
- `Rollback-Agent` — executes rollback on trigger (ties into CVE-Fast-Path → L1-8)

---

## 9. CI Workflows this L1 Commissions

- `deploy-non-prod.yml` — non-prod deploy workflow
- `deploy-prod.yml` — prod deploy with three-approver mechanic
- `rollback.yml` — callable rollback workflow
- Possible: `deployment-ledger-append.yml` — appends Deployment-Record to chosen ledger

---

## 10. Completion Criteria

Per L1-roadmap.md §5.
