# Business Object: Goods Receipt

> **Template:** [Business Object](../../../../artifacts/product/business-object.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | goods-receipt |
| name | Goods Receipt |
| description | A record of materials received from a supplier against a purchase order. Captures what was expected, what was actually received, and the verification status. Each goods receipt references a single purchase order and contains one or more line items. |
| ownerActorRef | receiving-clerk |
| functionalAreaRef | inbound |
| relatedObjects | material |
| usedInProcesses | goods-receipt |

---

## Attributes

| Name | Type | Description |
|------|------|-------------|
| id | string | Unique identifier for the goods receipt |
| purchaseOrderRef | string | Reference to the source purchase order (from PPS-PUR) |
| deliveryNoteNumber | string | Supplier's delivery note number |
| supplierRef | string | Reference to the supplier (from BP suite) |
| receivedAt | datetime | Timestamp when goods were physically received |
| receivedBy | ref (actor) | Actor who performed the receipt (receiving-clerk) |
| lines | array | Line items with material details |
| lines[].materialRef | ref | Reference to the material |
| lines[].expectedQty | number | Quantity expected per purchase order |
| lines[].actualQty | number | Quantity actually received and counted |
| lines[].unit | string | Unit of measure (kg, pcs, m, etc.) |
| status | enum | Current lifecycle state |

---

## Lifecycle States

`pending` --> `received` --> `posted` --> `cancelled`

| State | Description |
|-------|-------------|
| pending | Goods receipt created from delivery note, awaiting physical receipt |
| received | Materials physically received and quantities confirmed |
| posted | Goods receipt posted to inventory, downstream events emitted |
| cancelled | Goods receipt cancelled (e.g., delivery rejected, quality failure) |
