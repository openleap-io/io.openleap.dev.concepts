# Product: Warehouse Operations

> **Template:** [Product](../../../artifacts/product/product.md)

---

## Identity

| Field | Value |
|-------|-------|
| id | warehouse-ops |
| name | Warehouse Operations |
| description | Operational application for warehouse staff managing inbound goods, storage, picking, and outbound shipments in a mid-size manufacturing company. |

---

## Scope

→ [project-scope.md](project-scope.md)

---

## Personas

→ [personas/](personas/)
- [Warehouse Worker](personas/warehouse-worker.md)
- [Warehouse Manager](personas/warehouse-manager.md)
- [Receiving Clerk](personas/receiving-clerk.md)

---

## Business Processes

→ [processes/](processes/)
- [Goods Receipt](processes/goods-receipt.md) (L0)
- [Put-Away](processes/put-away.md) (L0)
- [Pick & Pack](processes/pick-and-pack.md) (L0)
- [Goods Issue](processes/goods-issue.md) (L0)

---

## Actors

→ [actors/](actors/)
- [Warehouse Worker](actors/warehouse-worker.md) (internal)
- [Warehouse Manager](actors/warehouse-manager.md) (internal)
- [Receiving Clerk](actors/receiving-clerk.md) (internal)
- [Supplier](actors/supplier.md) (external)
- [Logistics Partner](actors/logistics-partner.md) (external)

---

## Business Objects

→ [business-objects/](business-objects/)
- [Goods Receipt](business-objects/goods-receipt.md)
- [Storage Unit](business-objects/storage-unit.md)
- [Bin Location](business-objects/bin-location.md)
- [Pick Wave](business-objects/pick-wave.md)
- [Shipment](business-objects/shipment.md)
- [Material](business-objects/material.md)

---

## Business Events

→ [business-events/](business-events/)
- [Goods Received](business-events/goods-received.md)
- [Material Put Away](business-events/material-put-away.md)
- [Pick Wave Released](business-events/pick-wave-released.md)
- [Shipment Dispatched](business-events/shipment-dispatched.md)

---

## Business Rules

→ [business-rules/](business-rules/)
- [BR-001: Quality Check Required for Regulated Materials](business-rules/br-001-quality-check.md)
- [BR-002: FIFO Principle for Perishable Goods](business-rules/br-002-fifo.md)
- [BR-003: Bin Capacity Must Not Be Exceeded](business-rules/br-003-bin-capacity.md)
- [BR-004: Four-Eye Principle for High-Value Goods](business-rules/br-004-four-eye.md)

---

## Business Capabilities

→ [business-capabilities/](business-capabilities/)
- [CAP-001: Receive Inbound Goods](business-capabilities/cap-001-receive-goods.md)
- [CAP-002: Store Materials in Warehouse](business-capabilities/cap-002-store-materials.md)
- [CAP-003: Pick Materials for Orders](business-capabilities/cap-003-pick-materials.md)
- [CAP-004: Ship Outbound Goods](business-capabilities/cap-004-ship-goods.md)
- [CAP-005: Track Inventory Levels](business-capabilities/cap-005-track-inventory.md)
- [CAP-006: Manage Warehouse Layout](business-capabilities/cap-006-manage-layout.md)

---

## Glossary Terms

→ [glossary/](glossary/)
- [Material](glossary/material.md)
- [Goods Receipt](glossary/goods-receipt.md)
- [Bin Location](glossary/bin-location.md)
- [Storage Unit](glossary/storage-unit.md)
- [Pick Wave](glossary/pick-wave.md)
- [Lot / Batch](glossary/lot-batch.md)
- [FIFO](glossary/fifo.md)
- [Goods Issue](glossary/goods-issue.md)

---

## Quality Requirements

→ [quality-requirements/](quality-requirements/)
- [QR-001: Scan-to-confirm latency < 1s](quality-requirements/qr-001-scan-latency.md)
- [QR-002: System available 99.5% during warehouse hours (06:00–22:00)](quality-requirements/qr-002-availability.md)
- [QR-003: Inventory accuracy ≥ 99.8%](quality-requirements/qr-003-accuracy.md)

---

## External Interfaces

→ [external-interfaces/](external-interfaces/)
- [Barcode / RFID Scanner](external-interfaces/scanner.md)
- [ERP Procurement (Purchase Orders)](external-interfaces/erp-procurement.md)
- [ERP Sales (Delivery Orders)](external-interfaces/erp-sales.md)
- [Logistics Partner API](external-interfaces/logistics-api.md)

---

## KPIs

→ [kpis/](kpis/)
- [KPI-001: Goods Receipt Processing Time](kpis/kpi-001-receipt-time.md)
- [KPI-002: Pick Accuracy Rate](kpis/kpi-002-pick-accuracy.md)
- [KPI-003: Warehouse Utilization](kpis/kpi-003-utilization.md)

---

## Functional Areas

→ [functional-areas/](functional-areas/)
- [Inbound](functional-areas/inbound.md) — Goods Receipt, Put-Away, Quality Check
- [Storage](functional-areas/storage.md) — Bin Management, Inventory Tracking
- [Outbound](functional-areas/outbound.md) — Pick & Pack, Goods Issue, Shipping

---

## Decisions

→ [decisions/](decisions/)
- [DEC-001: Separate Inbound and Outbound areas](decisions/dec-001-separate-areas.md)
- [DEC-002: Mobile-first UI for warehouse floor](decisions/dec-002-mobile-first.md)

---

## Workflow Candidates

→ [workflow-candidates/](workflow-candidates/)
- [WC-001: Nightly Inventory Reconciliation](workflow-candidates/wc-001-inventory-recon.md)
- [WC-002: Automatic Replenishment Trigger](workflow-candidates/wc-002-replenishment.md)

---

## Required Capabilities

These capabilities are sent to the Platform (Telos Decompose) for Feature Resolution:

| Capability ID | Name | Priority | Expected Suite |
|--------------|------|----------|---------------|
| CAP-001 | Receive Inbound Goods | core / must-have | pps (im) |
| CAP-002 | Store Materials in Warehouse | core / must-have | pps (wm) |
| CAP-003 | Pick Materials for Orders | core / must-have | pps (wm) |
| CAP-004 | Ship Outbound Goods | core / must-have | pps (wm) |
| CAP-005 | Track Inventory Levels | core / must-have | pps (im) |
| CAP-006 | Manage Warehouse Layout | supporting / should-have | pps (wm) |

**Cross-suite reads expected:**
- Supplier data from BP suite (Business Partner)
- Purchase Order data from PPS-PUR suite (Procurement)
- Delivery Order data from SD suite (Sales)

---

## Feature Resolution

→ Returned by Platform — see [feature-resolution.md](feature-resolution.md) (to be produced by Telos Decompose)

---

## BFF Configuration

→ Derived from Feature Resolution — see [bff-configuration.md](bff-configuration.md) (to be produced after resolution)

---

## Navigation Architecture

Mobile-first (tablet/handheld scanner):
- Home → Today's Tasks (Goods Receipts pending, Picks pending)
- Inbound → Receive Goods, Put-Away
- Outbound → Pick Waves, Pack & Ship
- Inventory → Stock Overview, Bin Management
- Settings → Profile, Scanner Config
