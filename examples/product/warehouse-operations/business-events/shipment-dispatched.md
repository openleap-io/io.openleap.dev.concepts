# Business Event: Shipment Dispatched

> **Template:** [Business Event](../../../../artifacts/product/business-event.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | shipment-dispatched |
| name | Shipment Dispatched |
| description | Emitted when a shipment is confirmed as dispatched. Triggers notification to the logistics partner and signals that inventory has been decremented. |
| triggerType | process_outcome |
| emittedByProcess | goods-issue |
| triggersProcesses | — |
| functionalAreaRef | outbound |

---

## Carried Data

| Name | Object Ref | Description |
|------|-----------|-------------|
| shipmentId | shipment | ID of the dispatched shipment |
| trackingNumber | — | Carrier tracking number for the shipment |
