# Actor: Warehouse Worker

> **Template:** [Actor](../../../../concepts/artifacts/product/actor.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | warehouse-worker |
| name | Warehouse Worker |
| type | internal |
| description | Warehouse floor employee responsible for physical handling of materials — unloading, transporting, scanning, picking, packing, and loading goods. |
| decisionAuthority | Can decide bin placement when system suggestion is overridden (manual override logged). |

---

## Responsibilities

- Unload incoming deliveries and count materials
- Transport materials to assigned bin locations
- Scan materials and bins during put-away and picking
- Pick materials according to pick lists
- Pack and label outbound shipments
- Load goods onto transport vehicles
- Report damaged or missing materials

---

## Stakeholder

| Field | Value |
|-------|-------|
| role | Primary system user on warehouse floor |
| influence | medium |
| interests | Efficient workflows, minimal manual data entry, clear task assignments |
| informationNeeds | Current pick list, bin location directions, task queue, material details |
