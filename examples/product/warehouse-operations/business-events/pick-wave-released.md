# Business Event: Pick Wave Released

> **Template:** [Business Event](../../../../artifacts/product/business-event.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | pick-wave-released |
| name | Pick Wave Released |
| description | Emitted when a Warehouse Manager releases a pick wave for execution. Signals that pick tasks are available for the assigned worker on their handheld device. |
| triggerType | process_outcome |
| emittedByProcess | pick-and-pack |
| triggersProcesses | — |
| functionalAreaRef | outbound |

---

## Carried Data

| Name | Object Ref | Description |
|------|-----------|-------------|
| pickWaveId | pick-wave | ID of the released pick wave |
| assignedWorkerId | — | ID of the warehouse worker assigned to the wave |
