# Artifact: Workflow Candidate

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.5, §9.1 MongoDB Document Model

---

## Definition

A Workflow Candidate is a **process pattern flagged for Telos** — batch jobs, sagas, scheduled tasks, and other patterns that do not belong in BPMN but were discovered during business process analysis. Workflow Candidates bridge the gap between business discovery (Elara) and technical workflow definition (Telos).

**KEY ROLE:** Workflow Candidates **become Workflow Specs in Platform Space** — they are handed off to Telos for Temporal workflow definition.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Candidate identifier |
| name | string | ✓ | Candidate name (e.g., "Nightly lesson report generation") |
| discoveredFromProcess | ref | | Process ID where this candidate was discovered |
| reason | string | ✓ | Why this is a workflow candidate (e.g., "recurring batch pattern", "saga pattern detected") |
| estimatedTrigger | string | | Expected trigger (e.g., "cron 02:00", "on failure") |
| notes | string | | Additional notes and context |

---

## Constraints

1. A WorkflowCandidate MUST have a reason explaining why it was flagged

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | BusinessProcess | discoveredFromProcess |
| referenced by | Product | product.workflowCandidates |
| becomes | Workflow Spec (Platform Space) | Handoff to Telos for Temporal workflow definition |

---

## Existing Sources

- Elara Spec §4.2 Cat.5 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema: ⚠️ **NOT in Freeze schema — gap**
