# L1 Spec 7 — Issue & Incident Management

> **Document Type:** Platform Governance — Operating Model Detail (L1)
> **ID:** GOV-OM-L1-07
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — awaiting content
> **Author:** OpenLeap Architecture Team
> **Scope:** Issue-Flow operationalized (tool decision), incident severity per GOV-DORA-003, triage SLA enforcement
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Related:** governance/incident-management-policy.md (GOV-DORA-003)
> **Owner (suggested):** Platform Owner + Domain Owners

---

## 1. Purpose

Operationalize the Issue-Flow (GOV-OM-001 §10.2) and integrate with the existing Incident Management Policy (GOV-DORA-003). Decide the concrete tools for the Issue and Incident custody entries (both currently TBD in custody.md) and specify the routing, triage SLA, and escalation mechanics.

---

## 2. Scope

**In scope.**
- Issue sources (Incident, Metric-Anomaly, External-Feedback, Dependency/CVE-Alert)
- Routing rule operationalized (tier-based, parallel security routing)
- Triage SLA (14d per L0 §10.2) enforcement mechanic
- Tool selection for Issue ledger (GitHub Issues per repo vs. central repo vs. external)
- Tool/process for Incident ledger (respects GOV-DORA-003 severity classification)
- Feedback loop from Issue-Rejected back to originating source
- Escalation for Sev1/Sev2 incidents per §8 RACI note

**Out of scope.** CVE-specific flow (→ L1-8); metric generation (→ L1-2).

---

## 3. Dependencies on L0

- GOV-OM-001 §4.1 — Phase Operate
- GOV-OM-001 §6.4 — Operate-Gate mechanics
- GOV-OM-001 §8 — RACI for Operate (Incident)
- GOV-OM-001 §10.2 — Issue-Flow

---

## 4. Dependencies on other L1 Specs

- L1-1 (Spec-Lifecycle) — Intent format; an Accepted-as-Intent issue becomes an Intent

---

## 5. Invariants derived from L0

- No issue untriaged longer than 14 days
- Tier-based routing is mechanical; security-tag adds Security Authority in parallel (does not replace)
- Issue → Intent is explicit; Issue → Rejected requires audit-record
- Sev1/Sev2 incident escalates to PO + Architect automatically (§8)

---

## 6. Open Questions

1. Issue ledger: GitHub Issues per repo with label conventions? Central `io.openleap.issues` repo? External (Linear, Jira)?
2. Incident ledger: same as issue or separate? Linkage to GOV-DORA-003 evidence requirements?
3. How is the 14d SLA enforced automatically? Weekly report? PR-block on next release from the affected component until triage completes?
4. Triage-routing automation: bot that auto-assigns by tier+label? Manual?
5. External-feedback ingestion: email → issue automation?
6. Per-domain SLA overrides allowed?

---

## 7. Artifacts this L1 Registers in custody.md

- `Issue` — tool selection finalized
- `Incident` — tool selection finalized (respecting GOV-DORA-003)

---

## 8. Agent Roles this L1 Introduces

- `Issue-Triage-Agent` — auto-routes issues, flags SLA misses
- `Incident-Agent` — monitors, creates Incident-Records, escalates per severity
- `Feedback-Agent` (optional) — ingests external feedback into the issue flow

---

## 9. CI Workflows this L1 Commissions

- Possible: `issue-sla-report.yml` — scheduled report on triage backlog
- Possible: `incident-record-append.yml` — appends to incident ledger

---

## 10. Completion Criteria

Per L1-roadmap.md §5.
