# Intent Template — Template for `Intent` Artifacts

> **Template ID:** TPL-OM-INT-001
> **Artifact Class:** Intent (GOV-OM-001 §5, custody.md §4)
> **Produced In:** Conceive phase (GOV-OM-001 §4.1)
> **Exit Gate:** Intent-Gate, approved by Product Owner (§6.4)

---

## Purpose

An Intent captures **what we are about to do and why**, before any spec is drafted. It is the hand-off artifact from Conceive to Specify. Every spec draft is traceable to exactly one Intent via forward-link (§5.4).

Intent IDs are immutable, monotonic (e.g., `INT-0042`). Once approved, an Intent's content is not edited — superseding intents reference the original by ID.

---

## Instantiation

Create the file at the custody location per `operating-model/custody.md` row `Intent` (default: `io.openleap.dev.spec/T*/<module>/intents/INT-nnn-<slug>.md`; product intents under `io.openleap.dev.spec/products/<product>/intents/`).

Fill in every section marked ⚑ before presenting at Intent-Gate.

---

## Template

```markdown
# Intent INT-nnnn — <Slug>

> **ID:** INT-nnnn
> **Created:** YYYY-MM-DD
> **Context:** Platform-Evolution | Domain-Evolution | Product-Derivation  ⚑
> **Tier:** T1 | T2 | T3/<domain> | T4  ⚑  *(omit if Product-Derivation)*
> **Product:** <product-id>  ⚑  *(only if Product-Derivation)*
> **Originator:** <human or agent that proposed this intent>
> **Status:** PROPOSED | APPROVED | SUPERSEDED | REJECTED

## 1. Problem / Motivation  ⚑

What is wrong, missing, or new that triggers this intent? One paragraph.

## 2. Source(s)  ⚑

Forward-links to the origin. At least one MUST be provided. Permitted sources:
- Issue: `issue:<issue-id>`
- Incident: `incident:<incident-id>`
- Metric-Anomaly: `metric-snapshot:<id>#<anomaly>`
- Improvement-Proposal: `imp:<imp-id>`
- External-Feedback: `feedback:<channel>:<ref>`
- Roadmap-Entry: `roadmap:<entry-id>`
- (Product-Intent only) Product-Request: `product:<product-id>:<request>`

## 3. Intended Outcome  ⚑

What will be true after this is done that is not true now? One paragraph. Does not describe HOW.

## 4. Scope and Non-Scope  ⚑

- **In**: bulleted list of what is in scope
- **Out**: bulleted list of what is NOT in scope (to prevent spec bloat)

## 5. Affected Artifacts (best-effort pre-list)

Which existing Specs, UVLs, ADRs, or Contracts are expected to change? A rough list is fine; the Spec phase will refine.

## 6. Constraints

Hard constraints that the Spec will inherit (e.g., must not break existing Contract X; must meet performance target Y; must comply with GOV-DORA-003 severity rules).

## 7. Open Questions

Questions the Product Owner will answer at Intent-Gate or explicitly defer. A deferred question is acceptable; an unanswered mandatory question is not.

## 8. Approval

> _Intent-Gate Record_
>
> - **Approver (Product Owner):** <name / self@audit>
> - **Approved on:** YYYY-MM-DD
> - **Co-sign (Architect, if Tier-Escalation):** <name / self@audit>
> - **Audit Record Path (Solo-Mode):** <commit-sha or compliance-report-ref>

## 9. Supersedes / Superseded By

- Supersedes: `INT-xxxx` (if this replaces an earlier intent)
- Superseded by: `INT-yyyy` (filled in when this intent is replaced)
```
