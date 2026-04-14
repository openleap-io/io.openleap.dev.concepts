# Artifact: Business Object

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.3, §9.1 MongoDB Document Model

---

## Definition

A Business Object represents a **thing the business works with** — not a database table, but the business's own mental model of its data. Business Objects capture what flows between process steps, who is responsible for which data, and how data changes over time through lifecycle states.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Business object identifier |
| name | string | ✓ | Object name (e.g., "Ausbildungsvertrag") |
| description | string | ✓ | What this object represents in the business |
| attributes | Attribute[] | ✓ | Object attributes (min. 1) |
| attributes[].name | string | ✓ | Attribute name |
| attributes[].type | string | | Attribute type |
| attributes[].description | string | | Attribute description |
| lifecycleStates | string[] | | Ordered lifecycle states (e.g., ["draft", "active", "completed", "archived"]) |
| ownerActorRef | ref | | Actor responsible for this object |
| functionalAreaRef | ref | | Which Functional Area this belongs to |
| relatedObjects | ref[] | | Other BusinessObject IDs related to this one |
| usedInProcesses | ref[] | | Process IDs where this object is read or written |

---

## Constraints

1. A BusinessObject MUST have at least one attribute
2. lifecycleStates, when present, MUST be ordered (first = initial state, last = terminal state)

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | Actor | ownerActorRef |
| references | FunctionalArea | functionalAreaRef |
| references | BusinessObject | relatedObjects |
| references | BusinessProcess | usedInProcesses |
| referenced by | ProcessStep | step.businessObjectsRead, step.businessObjectsWritten |
| referenced by | BusinessEvent | event.carriedData[].objectRef |
| referenced by | GlossaryTerm | term DESCRIBES object (Knowledge Graph) |

---

## Existing Sources

- Elara Spec §4.2 Cat.3 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema: ⚠️ **NOT in Freeze schema — gap**
