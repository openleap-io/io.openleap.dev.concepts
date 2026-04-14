# Artifact: Decision

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.5, §9.1 MongoDB Document Model, Freeze schema `decisions`

---

## Definition

A Decision captures an **important choice** made during discovery — the "why" behind the model's structure. Decisions record context, rationale, alternatives considered, and what areas are affected.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Decision identifier |
| title | string | ✓ | Decision title (e.g., "Treat Theorie and Praxis as separate functional areas") |
| context | string | ✓ | Background and circumstances leading to this decision |
| decision | string | ✓ | What was decided |
| rationale | string | ✓ | Why this choice was made |
| alternatives | string[] | | Alternatives that were considered and rejected |
| decidedBy | string | | Who made the decision |
| date | date | | When the decision was made |
| affectedAreas | ref[] | | FunctionalArea IDs affected by this decision |
| affectedProcesses | ref[] | | Process IDs affected by this decision |

---

## Constraints

1. A Decision MUST have context, decision, and rationale (the three core ADR fields)

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | FunctionalArea | affectedAreas |
| references | BusinessProcess | affectedProcesses |
| referenced by | Product | product.decisions |

---

## Existing Sources

- Elara Spec §4.2 Cat.5 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema `decisions` ✅
- Status: **Fully defined, no gaps**
