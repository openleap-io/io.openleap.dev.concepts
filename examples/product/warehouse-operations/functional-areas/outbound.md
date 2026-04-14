# Functional Area: Outbound

> **Template:** [Functional Area](../../../../artifacts/product/functional-area.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | outbound |
| name | Outbound |
| description | Covers all operations related to fulfilling delivery orders — from pick wave release through material picking, packing, labeling, loading, dispatch, and logistics partner notification. |
| rationale | Outbound operations share common actors (Warehouse Worker, Warehouse Manager, Logistics Partner), a common physical zone (picking aisles, packing station, dispatch dock), and a sequential flow from pick to ship. Separated from inbound because the triggers, actors, and KPIs differ. |
| dependsOn | storage |

---

## Includes

- Pick & Pack (process)
- Goods Issue (process)
- Shipping / Dispatch (sub-activity within Goods Issue)

---

## Excludes

- Goods Receipt (inbound)
- Put-Away (inbound)
- Sales Order management (SD, external)
- Transportation planning (logistics partner, external)

---

## Sub-Areas

| Name | Type |
|------|------|
| Picking | core |
| Packing | core |
| Shipping | core |
