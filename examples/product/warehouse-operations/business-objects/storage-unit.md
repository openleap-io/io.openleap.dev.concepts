# Business Object: Storage Unit

> **Template:** [Business Object](../../../../concepts/artifacts/product/business-object.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | storage-unit |
| name | Storage Unit |
| description | A trackable unit of material stored in a specific bin location. Represents a quantity of a single material in a single bin, including lot/batch information for traceability. Storage units are created during put-away and consumed during picking. |
| ownerActorRef | warehouse-worker |
| functionalAreaRef | storage |
| relatedObjects | material, bin-location |
| usedInProcesses | put-away, pick-and-pack |

---

## Attributes

| Name | Type | Description |
|------|------|-------------|
| id | string | Unique identifier for the storage unit |
| materialRef | ref | Reference to the stored material |
| quantity | number | Current quantity in this storage unit |
| unit | string | Unit of measure (kg, pcs, m, etc.) |
| lotNumber | string | Lot/batch number for traceability and FIFO |
| binLocationRef | ref | Reference to the bin location where this unit is stored |
| storedAt | datetime | Timestamp when the material was placed in the bin |
| storedBy | ref (actor) | Actor who performed the put-away (warehouse-worker) |

---

## Lifecycle States

`created` --> `stored` --> `picked` --> `consumed`

| State | Description |
|-------|-------------|
| created | Storage unit record created during put-away, material in transit to bin |
| stored | Material confirmed in bin location, available for picking |
| picked | Material picked from bin, quantity decremented |
| consumed | Storage unit fully consumed (quantity = 0), record retained for audit |
