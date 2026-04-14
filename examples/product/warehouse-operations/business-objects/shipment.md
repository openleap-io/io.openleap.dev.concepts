# Business Object: Shipment

> **Template:** [Business Object](../../../../concepts/artifacts/product/business-object.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | shipment |
| name | Shipment |
| description | An outbound shipment created from a completed pick wave, representing a physical consignment handed to a logistics partner for delivery. Tracks dispatch status and carrier tracking information. |
| ownerActorRef | warehouse-manager |
| functionalAreaRef | outbound |
| relatedObjects | pick-wave |
| usedInProcesses | goods-issue |

---

## Attributes

| Name | Type | Description |
|------|------|-------------|
| id | string | Unique identifier for the shipment |
| pickWaveRef | ref | Reference to the source pick wave |
| logisticsPartnerRef | ref (actor) | Reference to the logistics partner handling transport |
| dispatchedAt | datetime | Timestamp when the shipment was dispatched |
| trackingNumber | string | Carrier tracking number for the shipment |
| status | enum | Current lifecycle state |

---

## Lifecycle States

`created` --> `loaded` --> `dispatched` --> `delivered`

| State | Description |
|-------|-------------|
| created | Shipment record created from completed pick wave |
| loaded | Goods physically loaded onto logistics partner's vehicle |
| dispatched | Dispatch confirmed, logistics partner notified |
| delivered | Logistics partner confirms delivery to customer (external update) |
