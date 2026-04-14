# Business Event: Material Put Away

> **Template:** [Business Event](../../../../concepts/artifacts/product/business-event.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | material-put-away |
| name | Material Put Away |
| description | Emitted when a material is successfully stored in a bin location. Signals that the storage unit is created, bin utilization is updated, and the material is available for picking. |
| triggerType | process_outcome |
| emittedByProcess | put-away |
| triggersProcesses | — |
| functionalAreaRef | inbound |

---

## Carried Data

| Name | Object Ref | Description |
|------|-----------|-------------|
| storageUnitId | storage-unit | ID of the created storage unit |
| binLocationId | bin-location | ID of the bin location where material was stored |
