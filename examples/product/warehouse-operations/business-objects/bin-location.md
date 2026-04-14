# Business Object: Bin Location

> **Template:** [Business Object](../../../../artifacts/product/business-object.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | bin-location |
| name | Bin Location |
| description | A physical storage position within the warehouse, identified by aisle, rack, level, and position. Bin locations are static master data managed by the Warehouse Manager. Each bin has a defined capacity and type (standard, cold, hazmat) that constrains what can be stored there. |
| ownerActorRef | warehouse-manager |
| functionalAreaRef | storage |
| relatedObjects | storage-unit |
| usedInProcesses | put-away, pick-and-pack |

---

## Attributes

| Name | Type | Description |
|------|------|-------------|
| id | string | Unique identifier for the bin location |
| aisle | string | Aisle identifier (e.g., "A", "B", "C") |
| rack | string | Rack number within the aisle |
| level | string | Vertical level on the rack (e.g., "1", "2", "3") |
| position | string | Horizontal position on the level |
| capacity | number | Maximum storage capacity (in base unit, e.g., kg or pcs) |
| currentUtilization | number | Current used capacity |
| type | enum | Storage type: standard, cold, hazmat |

---

## Lifecycle States

None — Bin Location is static master data. Changes are managed through warehouse layout configuration.
