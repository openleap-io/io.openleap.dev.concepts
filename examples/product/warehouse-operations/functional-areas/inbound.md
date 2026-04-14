# Functional Area: Inbound

> **Template:** [Functional Area](../../../../concepts/artifacts/product/functional-area.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | inbound |
| name | Inbound |
| description | Covers all operations related to receiving goods into the warehouse and storing them — from supplier delivery arrival through quality check, goods receipt posting, bin assignment, and confirmed put-away. |
| rationale | Inbound operations share common actors (Receiving Clerk, Warehouse Worker), a common physical zone (receiving dock and storage aisles), and a sequential flow from receipt to storage. Separated from outbound because the actors, zones, and KPIs differ. |
| dependsOn | storage |

---

## Includes

- Goods Receipt (process)
- Put-Away (process)
- Quality Check (sub-activity within Goods Receipt)

---

## Excludes

- Pick & Pack (outbound)
- Goods Issue (outbound)
- Procurement / Purchase Order management (PPS-PUR, external)

---

## Sub-Areas

| Name | Type |
|------|------|
| Receiving | core |
| Put-Away | core |
