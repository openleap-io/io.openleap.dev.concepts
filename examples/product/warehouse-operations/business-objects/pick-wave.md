# Business Object: Pick Wave

> **Template:** [Business Object](../../../../concepts/artifacts/product/business-object.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | pick-wave |
| name | Pick Wave |
| description | A batch of pick tasks grouped from one or more delivery orders, assigned to a single Warehouse Worker. Pick waves optimize walking routes and allow the manager to control the flow of outbound work. |
| ownerActorRef | warehouse-manager |
| functionalAreaRef | outbound |
| relatedObjects | material, bin-location, shipment |
| usedInProcesses | pick-and-pack, goods-issue |

---

## Attributes

| Name | Type | Description |
|------|------|-------------|
| id | string | Unique identifier for the pick wave |
| deliveryOrderRefs | array (string) | References to the delivery orders included in this wave (from SD) |
| lines | array | Pick line items |
| lines[].materialRef | ref | Reference to the material to pick |
| lines[].qty | number | Quantity to pick |
| lines[].binLocationRef | ref | Bin location to pick from |
| assignedTo | ref (actor) | Warehouse Worker assigned to this pick wave |
| releasedAt | datetime | Timestamp when the wave was released by the manager |
| status | enum | Current lifecycle state |

---

## Lifecycle States

`planned` --> `released` --> `in-progress` --> `completed`

| State | Description |
|-------|-------------|
| planned | Pick wave created from delivery orders, not yet released |
| released | Manager has released the wave and assigned it to a worker |
| in-progress | Worker is actively picking materials |
| completed | All items picked and packed, ready for goods issue |
