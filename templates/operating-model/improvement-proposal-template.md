# Improvement-Proposal Template — Template for `Improvement-Proposal` Artifacts

> **Template ID:** TPL-OM-IMP-001
> **Artifact Class:** Improvement-Proposal (GOV-OM-001 §10.5, custody.md §4)
> **Produced In:** Learn phase
> **Exit Gate:** Learn-Gate → one of three paths (Meta-Gate / new Intent / Platform-Owner Intent) per §10.5

---

## Purpose

An Improvement-Proposal is the output of the Learn phase. It analyzes metric evidence and proposes a change — to the Doctrine itself (Meta-Governance), to a domain/service (regular Intent), or to a tool/workflow (Platform-Owner Intent).

A proposal is NOT an Intent. It explicitly links to metric sources as evidence and identifies which of the three output paths applies. The destination gate decides whether the proposal becomes an actionable Intent.

---

## Instantiation

Create at `io.openleap.dev.concepts/operating-model/improvements/IMP-nnnn-<slug>.md`.

Improvement-Proposals are **immutable once submitted**. A rejected or superseded proposal is not deleted; it is marked `status: rejected` or `status: superseded`, with a supersession link if applicable.

---

## Template

```markdown
# Improvement-Proposal IMP-nnnn — <Slug>

> **ID:** IMP-nnnn
> **Created:** YYYY-MM-DD
> **Proposer:** <human or agent>
> **Status:** PROPOSED | ACCEPTED | REJECTED | SUPERSEDED
> **Target Path:** Meta-Governance | New-Intent | Platform-Owner-Intent  ⚑

## 1. Observation

One paragraph: what pattern, trend, or anomaly was observed in the metric evidence?

## 2. Evidence (forward-links mandatory)

At least one metric source is required. Forward-link format:
- `metric-snapshot:<id>` — specific snapshot
- `metric-trend:<kpi>:<time-range>` — trend over a window
- `incident:<incident-id>` — incident as evidence
- `dora:<kpi>:<period>` — DORA-specific

Example:
```
- dora:lead-time:2026-Q1 — median lead time increased from 2d to 5d
- metric-trend:gate-reject-rate:2026-03 — Spec-Gate reject rate rose from 4% to 18%
- incident:INC-0212 — post-mortem identified the same root cause
```

## 3. Root-Cause Hypothesis

What you believe caused the pattern. This is a hypothesis, not a proven fact. The accepting gate will decide whether to act on it or require further analysis.

## 4. Proposed Change

Describe the change at the appropriate abstraction for the target path:

- **Meta-Governance target**: what section(s) of L0 / ontology / templates change? Draft text if small; reference an Amendment-ADR (`ADR-OM-nnn`) if larger.
- **New-Intent target**: a one-paragraph intent description that, if accepted, becomes a draft Intent.
- **Platform-Owner-Intent target**: the tool/workflow change proposed; which reusable workflow(s) are affected.

## 5. Expected Impact

Quantified if possible:
- Which KPIs improve?
- By how much?
- Over what horizon?

If impact is not quantifiable, state so and indicate what observable signal will confirm the improvement worked.

## 6. Costs and Risks

- Implementation cost (rough estimate in person-days or agent-hours)
- Operational risk of the change itself (could break something)
- Rollback strategy if the change causes regression

## 7. Alternatives Considered

One sentence each for at least two alternatives (including "do nothing") and why they were rejected.

## 8. Gate Record

> _Learn-Gate Record_
>
> - **Target Path:** Meta-Governance | New-Intent | Platform-Owner-Intent
> - **Approver (Architect):** <name / self@audit>
> - **PO Co-sign** (required for Meta-Governance path): <name / self@audit>
> - **Decision:** ACCEPT | REJECT | DEFER-WITH-CONDITIONS
> - **Decided at:** YYYY-MM-DD
> - **If ACCEPTED:** link to resulting Amendment-ADR / Intent: `<link>`
> - **If REJECTED:** reason in one paragraph.

## 9. Supersedes / Superseded By

- Supersedes: `IMP-xxxx` (if this replaces an earlier proposal)
- Superseded by: `IMP-yyyy` (filled in later if this is replaced)
```
