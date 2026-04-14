# Artifact: Business Event

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.3, §9.1 MongoDB Document Model

---

## Definition

A Business Event captures a **significant occurrence** that triggers or results from business activity. Events connect processes — when someone says "after the student signs up, then we create the contract," there are two events: one triggering, one resulting.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Event identifier |
| name | string | ✓ | Event name (e.g., "Student angemeldet") |
| description | string | ✓ | What this event represents |
| triggerType | enum | ✓ | process_outcome, external, temporal, condition |
| emittedByProcess | ref | | Process ID that produces this event |
| triggersProcesses | ref[] | | Process IDs that this event triggers |
| carriedData | CarriedData[] | | Data carried by this event |
| carriedData[].name | string | ✓ | Name of the carried data |
| carriedData[].objectRef | ref | | Reference to a BusinessObject |
| functionalAreaRef | ref | | Which Functional Area this belongs to |

---

## Constraints

1. A BusinessEvent MUST have a triggerType
2. An event SHOULD reference at least one process (emittedByProcess or triggersProcesses)

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | BusinessProcess | emittedByProcess |
| references | BusinessProcess | triggersProcesses |
| references | BusinessObject | carriedData[].objectRef |
| references | FunctionalArea | functionalAreaRef |
| referenced by | BusinessProcess | process TRIGGERED_BY / PRODUCES_EVENT (Knowledge Graph) |

---

## Existing Sources

- Elara Spec §4.2 Cat.3 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema: ⚠️ **NOT in Freeze schema — gap**
