# Actor: Warehouse Manager

> **Template:** [Actor](../../../../concepts/artifacts/product/actor.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | warehouse-manager |
| name | Warehouse Manager |
| type | internal |
| description | Supervises warehouse operations, manages staff assignments, monitors KPIs, releases pick waves, and approves high-value goods receipts. |
| decisionAuthority | Can release pick waves, approve high-value goods receipts (four-eye principle), override bin assignments, and manage warehouse layout. |

---

## Responsibilities

- Release pick waves for outbound orders
- Monitor warehouse utilization and throughput KPIs
- Approve goods receipts for high-value materials (BR-004)
- Manage bin location layout and capacity planning
- Assign workers to functional areas (inbound/outbound)
- Investigate and resolve inventory discrepancies

---

## Stakeholder

| Field | Value |
|-------|-------|
| role | Operations owner and decision maker |
| influence | high |
| interests | Throughput, accuracy, staff utilization, regulatory compliance |
| informationNeeds | Dashboard with bin utilization, aging stock, pick accuracy, receipt processing times |
