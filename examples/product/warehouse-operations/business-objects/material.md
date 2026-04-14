# Business Object: Material

> **Template:** [Business Object](../../../../artifacts/product/business-object.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | material |
| name | Material |
| description | A product or raw material managed in the warehouse. Material is master data owned by the PPS Product Data module (PPS-PD) and consumed read-only by warehouse operations. Materials carry attributes relevant to storage and handling, including whether they are regulated and require quality checks. |
| ownerActorRef | — |
| functionalAreaRef | storage |
| relatedObjects | storage-unit, bin-location |
| usedInProcesses | goods-receipt, put-away, pick-and-pack |

---

## Attributes

| Name | Type | Description |
|------|------|-------------|
| id | string | Unique identifier for the material |
| name | string | Human-readable material name |
| sku | string | Stock Keeping Unit number |
| unit | string | Base unit of measure (kg, pcs, m, l, etc.) |
| weight | number | Weight per unit in kg |
| isRegulated | boolean | Whether this material requires quality checks on receipt (see BR-001) |
| storageRequirements | string | Storage type requirement (standard, cold, hazmat) |

---

## Lifecycle States

None — Material is static master data, read from PPS-PD. Changes are managed outside warehouse operations scope.
