# Artifact: Actor

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.2, Freeze schema `actors`

---

## Definition

A person, role, or organization that participates in business processes. Actors have responsibilities, decision authority, and optionally stakeholder interests.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Actor identifier |
| name | string | ✓ | Role or organization name |
| type | enum | ✓ | internal, external, system |
| description | string | | What this actor does |
| responsibilities | string[] | ✓ | What they are responsible for |
| decisionAuthority | string | | What they can decide |
| stakeholder | Stakeholder | | Interest layer (embedded) |

### Stakeholder (embedded)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| role | string | | Stakeholder role description |
| influence | enum | | high, medium, low |
| interests | string[] | | What they care about |
| informationNeeds | string[] | | What information they need |

---

## Constraints

1. Every Actor MUST have at least one responsibility

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| referenced by | ProcessStep | step.actorRef |
| referenced by | BusinessObject | object.ownerActorRef |
| referenced by | Persona | persona.actorRefs |

---

## Existing Sources

- Elara Spec §4.2 Cat.2 ✅
- Freeze schema `actors` ✅
- Status: **Fully defined, no gaps**
