# Business Event: Goods Received

> **Template:** [Business Event](../../../../artifacts/product/business-event.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | goods-received |
| name | Goods Received |
| description | Emitted when a goods receipt is successfully posted. Signals that materials have been physically received, counted, verified, and are ready for put-away. |
| triggerType | process_outcome |
| emittedByProcess | goods-receipt |
| triggersProcesses | put-away |
| functionalAreaRef | inbound |

---

## Carried Data

| Name | Object Ref | Description |
|------|-----------|-------------|
| goodsReceiptId | goods-receipt | ID of the posted goods receipt |
| materialId | material | ID of the received material |
| quantity | — | Actual received quantity |
