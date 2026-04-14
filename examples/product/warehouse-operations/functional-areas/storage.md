# Functional Area: Storage

> **Template:** [Functional Area](../../../../concepts/artifacts/product/functional-area.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | storage |
| name | Storage |
| description | Covers the management of the warehouse's physical layout and real-time inventory tracking. Includes bin location master data, capacity management, stock level monitoring, and inventory reconciliation. |
| rationale | Storage is a shared foundation that both Inbound and Outbound depend on. Bin locations and inventory data are read and written by all processes. Managed separately because layout changes and inventory tracking are continuous activities, not tied to a single inbound or outbound flow. |
| dependsOn | — |

---

## Includes

- Bin Management (bin location master data, capacity)
- Inventory Tracking (stock levels, movements, reconciliation)

---

## Excludes

- Physical material movement (handled by Inbound and Outbound processes)
- Material master data (owned by PPS-PD, external)

---

## Sub-Areas

| Name | Type |
|------|------|
| Bin Management | core |
| Inventory Tracking | core |
