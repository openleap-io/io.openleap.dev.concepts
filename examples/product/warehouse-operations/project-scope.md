# Project Scope: Warehouse Operations

> **Template:** [Project Scope](../../../artifacts/product/project-scope.md)

---

## Business Problem

The company operates a 5,000 m² warehouse with 12 employees handling ~200 inbound deliveries and ~350 outbound shipments per week. Currently, goods receipt is tracked on paper forms, bin assignments are managed in Excel, and pick lists are printed daily. This leads to:
- Frequent mislocation of materials (estimated 3–5% of stock "lost" in wrong bins)
- Manual data entry into the ERP system 1–2 days after physical receipt (inventory data is always stale)
- Pick errors averaging 2.1% per week (wrong material, wrong quantity)
- No real-time visibility of warehouse utilization for the manager

---

## Goals

| # | Goal | Success Criteria | Priority |
|---|------|-----------------|----------|
| G-001 | Eliminate paper-based goods receipt | 100% of goods receipts captured digitally at point of receipt via scanner | must-have |
| G-002 | Real-time inventory accuracy | Inventory data reflects physical stock within 15 minutes of any movement | must-have |
| G-003 | Reduce pick errors | Pick error rate below 0.5% per week | must-have |
| G-004 | Warehouse utilization visibility | Manager dashboard showing bin utilization, aging stock, throughput metrics | should-have |
| G-005 | Reduce goods receipt processing time | From delivery arrival to ERP posting: < 30 minutes (currently ~24 hours) | should-have |

---

## In Scope

- Goods Receipt (inbound: receiving, quality check, posting)
- Put-Away (bin assignment, physical storage, confirmation)
- Pick & Pack (wave-based picking, packing, labeling)
- Goods Issue (outbound: loading, dispatch, confirmation)
- Inventory overview and stock queries
- Bin location management
- Mobile UI for warehouse floor (tablet / handheld scanner)
- Manager dashboard (web)

---

## Out of Scope

- Procurement (Purchase Order creation, supplier management) — handled by PPS-PUR, consumed as read-only
- Sales Order management — handled by SD, consumed as read-only
- Transportation planning and route optimization — handled by logistics partner
- Accounting and financial postings — handled by FI, triggered via events
- Quality Management laboratory processes — handled by PPS-QM (only the initial quality check on receipt is in scope)
- Manufacturing execution — handled by PPS-MES

---

## Constraints

| Constraint | Type | Impact |
|-----------|------|--------|
| Must integrate with existing barcode scanner hardware (Zebra TC52) | technology | Mobile UI must support Android WebView |
| Warehouse operates 06:00–22:00, six days a week | organizational | System availability SLA scoped to these hours |
| All material movements must be traceable for audit (ISO 9001) | regulatory | Every movement needs timestamp, actor, source/destination |
| ERP system (legacy) must receive goods receipt postings within 30 min | technology | Real-time event or near-real-time batch to ERP |

---

## Assumptions

- All materials have barcodes or RFID tags (no manual identification)
- WiFi coverage in the entire warehouse is reliable
- Warehouse workers are comfortable using touchscreen devices
- The company uses metric units (kg, m, pcs) — no imperial
