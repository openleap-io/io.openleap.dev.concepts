# Agent Roles Catalog

> **Document Type:** Platform Governance — Operating Model Registry
> **ID:** GOV-OM-003
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — grows with L1 specs
> **Author:** OpenLeap Architecture Team
> **Scope:** Catalog of abstract agent roles defined across L1 specs
> **Parent:** GOV-OM-001 (L0 Operating Doctrine, §7.3)

---

## 1. Purpose

GOV-OM-001 §7.3 defers the concrete agent-role catalog to L1 specs. This file accumulates all agent roles introduced by L1 specs into a single registry, so that:

- Overlap between L1 specs is visible (one spec introduces `Spec-Review-Agent`; another must not reintroduce the same role under a different name)
- Implementation choices per role are tracked (Paperclip team member / standalone Claude Code agent / MCP server / GitHub Actions job / skill)
- The boundary between agent responsibility (produces/transforms) and human responsibility (approves) is auditable for every role

This file is **append-only**. Entries may be updated (implementation choice changes, defined-in reference added) but not removed — an obsolete role gets marked `status: superseded` with a link to the successor.

---

## 2. Entry Schema

| Field | Meaning |
|---|---|
| **Role Name** | Functional name (e.g., `Spec-Authoring-Agent`) |
| **Input Artifact(s)** | What the agent reads from |
| **Output Artifact(s)** | What the agent produces or transforms |
| **Lifecycle Phase** | Phase per GOV-OM-001 §4.1 |
| **Gate Participation** | Which gate the agent contributes to (agent recommends, human approves) |
| **Auto-Reject Checks Performed** | Which §6.6 checks this agent runs before presenting at the gate |
| **Implementation** | One of: `paperclip-team:<team>`, `standalone-agent:<framework>`, `mcp-server`, `ci-job`, `skill`, `TBD` |
| **Defined In** | L1 spec that introduced the role |
| **Status** | `active`, `superseded`, `deprecated` |

---

## 3. Agent Roles

_No entries yet. This section grows as L1 specs are merged._

Each L1 spec adds its agent roles in a section here upon merge; the section's structure mirrors the schema in §2. Example of a future entry (illustrative only, not yet active):

```
### Spec-Authoring-Agent
- Input: Intent-Document
- Output: Spec-Artifact (draft)
- Phase: Specify
- Gate: participates in Spec-Gate (recommends Architect+QS approval)
- Auto-Reject Checks: Template-Violation, Ontology-Violation, Traceability-Gap
- Implementation: TBD (L1 Spec 1)
- Defined In: L1 Spec 1 (Spec-Lifecycle & Template-Governance)
- Status: active
```

---

## 4. Amendment

Changes follow GOV-OM-001 §13 (Meta-Gate). Adding a new agent role normally happens through the L1 spec that introduces it; direct edits of this file without a corresponding L1-spec reference are Meta-Gate-eligible changes.
