# Feature Resolution: Warehouse Operations

> **Template:** [Feature Resolution](../../../artifacts/platform/feature-resolution.md)
> **Product:** [Warehouse Operations](../../product/warehouse-operations/product.md)
> **Resolved by:** Telos Decompose

---

## Product Reference

| Field | Value |
|-------|-------|
| productRef | warehouse-ops |
| resolvedAt | 2026-03-29 |
| catalogVersion | platform-catalog v1.2.0 |

---

## Capability Mappings

| # | Capability | ID | Status | Matched Feature(s) | Mode | Notes |
|---|-----------|-----|--------|--------------------|----- |-------|
| 1 | Receive Inbound Goods | CAP-001 | **MATCHED** | F-PPS-IM-001 "Post Goods Receipt" | FULL | Covers: delivery verification, quantity confirmation, GR posting. Quality check supported via extension point. |
| 2 | Store Materials in Warehouse | CAP-002 | **MATCHED** | F-PPS-WM-001 "Put-Away" | FULL | Covers: bin suggestion, transport confirmation, scan verification. FIFO logic via attribute `storageStrategy`. |
| 3 | Pick Materials for Orders | CAP-003 | **MATCHED** | F-PPS-WM-002 "Pick & Pack" | FULL | Covers: wave-based picking, scan-per-pick, packing, labeling. |
| 4 | Ship Outbound Goods | CAP-004 | **MATCHED** | F-PPS-WM-003 "Goods Issue" | FULL | Covers: loading confirmation, dispatch, logistics notification. |
| 5 | Track Inventory Levels | CAP-005 | **MATCHED** | F-PPS-IM-002 "Stock Overview" | FULL | Covers: real-time stock by material/location, movement history. |
| 6 | Manage Warehouse Layout | CAP-006 | **GAP** | — | — | No platform-feature exists for bin layout management. |

---

## Gap Analysis

| Capability | Gap Description | Resolution | Rationale |
|-----------|----------------|------------|-----------|
| CAP-006: Manage Warehouse Layout | The platform has no feature for creating/editing bin locations, defining aisle/rack/level structure, or managing storage zone types (standard/cold/hazmat). | **EXTEND_PLATFORM** | Bin layout management is a generic warehouse capability needed by multiple future products. Creating F-PPS-WM-004 "Warehouse Layout Management" is more valuable than a custom service. |

---

## Feature Selection

| Feature ID | Name | Suite | Mode | Attribute Defaults | Extension Points Available |
|-----------|------|-------|------|-------------------|--------------------------|
| F-PPS-IM-001 | Post Goods Receipt | pps (im) | FULL | `qualityCheckEnabled: true`, `autoPostThreshold: 0` | `ext.qualityCheckHook`, `ext.customFieldsOnReceipt` |
| F-PPS-WM-001 | Put-Away | pps (wm) | FULL | `storageStrategy: "FIFO"`, `suggestBin: true` | `ext.customBinSelectionRule` |
| F-PPS-WM-002 | Pick & Pack | pps (wm) | FULL | `pickMethod: "wave"`, `scanRequired: true` | `ext.customPackingRule` |
| F-PPS-WM-003 | Goods Issue | pps (wm) | FULL | `requireLoadConfirmation: true` | `ext.customDispatchNotification` |
| F-PPS-IM-002 | Stock Overview | pps (im) | FULL | `refreshInterval: "realtime"` | — |
| F-BP-001 | Business Partner Search | bp | READ_ONLY | — | — |

**Note:** F-BP-001 was auto-added because F-PPS-IM-001 has a `requires F-BP-001` constraint (goods receipt references supplier data from BP suite).

---

## Cross-Suite Dependencies (auto-resolved)

| Feature | Requires | Suite | Reason | Mode |
|---------|----------|-------|--------|------|
| F-PPS-IM-001 | F-BP-001 (Business Partner Search) | bp | Goods Receipt references supplier — needs read access to BP data | READ_ONLY |
| F-PPS-WM-002 | F-PPS-IM-002 (Stock Overview) | pps (im) | Pick & Pack needs current stock levels to validate picks | READ_ONLY |
| F-PPS-WM-003 | F-PPS-WM-002 (Pick & Pack) | pps (wm) | Goods Issue can only dispatch what was picked | FULL (same suite) |

---

## UVL Constraint Validation

```
requiredConstraintsSatisfied: true
validationErrors: []

Resolved constraints:
  ✅ F-PPS-IM-001 requires F-BP-001 → F-BP-001 included (READ_ONLY)
  ✅ F-PPS-WM-002 requires F-PPS-IM-002 → F-PPS-IM-002 included (FULL)
  ✅ F-PPS-WM-003 requires F-PPS-WM-002 → F-PPS-WM-002 included (FULL)
  ✅ No excludes constraints violated
```

---

## Product-Specific Attribute Overrides (suggested by PO)

| Feature | Attribute | Platform Default | Product Override | Rationale |
|---------|-----------|-----------------|-----------------|-----------|
| F-PPS-IM-001 | `qualityCheckEnabled` | true | true | Kept — regulated materials in scope (BR-001) |
| F-PPS-WM-001 | `storageStrategy` | "FIFO" | "FIFO" | Kept — perishable goods in scope (BR-002) |
| F-PPS-WM-002 | `pickMethod` | "wave" | "wave" | Kept — wave-based picking matches the 350 shipments/week volume |
| F-PPS-WM-002 | `scanRequired` | true | true | Kept — scanner hardware available (Zebra TC52) |
| F-PPS-IM-002 | `refreshInterval` | "5min" | "realtime" | **Override** — goal G-002 requires real-time inventory accuracy |

---

## Extension Points to Fill

| Feature | Extension Point | Product Requirement | Implementation |
|---------|----------------|-------------------|----------------|
| F-PPS-IM-001 | `ext.qualityCheckHook` | BR-001: Regulated materials must undergo quality check | Call PPS-QM quality check API if `material.isRegulated == true` |
| F-PPS-IM-001 | `ext.customFieldsOnReceipt` | Add delivery note number and transport temperature fields | Custom fields: `deliveryNoteNumber (string)`, `transportTemp (decimal, °C)` |
| F-PPS-WM-003 | `ext.customDispatchNotification` | Notify logistics partner API on dispatch | POST to Logistics Partner API with shipmentId, trackingNumber |

---

## BFF Configuration (derived)

| Field | Value |
|-------|-------|
| deploymentModel | PER_PRODUCT |
| activeFeatures | F-PPS-IM-001, F-PPS-WM-001, F-PPS-WM-002, F-PPS-WM-003, F-PPS-IM-002, F-BP-001 |

### Feature Gating

| Feature | Mode | BFF Behavior |
|---------|------|-------------|
| F-PPS-IM-001 | FULL | All endpoints: POST (create GR), PUT (update GR), GET (query GR) |
| F-PPS-WM-001 | FULL | All endpoints: POST (put-away), GET (suggestions) |
| F-PPS-WM-002 | FULL | All endpoints: POST (release wave, confirm pick), GET (pick lists) |
| F-PPS-WM-003 | FULL | All endpoints: POST (dispatch), GET (shipments) |
| F-PPS-IM-002 | FULL | GET only (stock queries — no mutations on stock overview) |
| F-BP-001 | READ_ONLY | **GET only** — search and detail endpoints. POST/PUT/DELETE blocked. |

### Extension Routing

| Extension Point | Route Target |
|----------------|-------------|
| `ext.qualityCheckHook` | `pps-qm-svc` → `POST /api/pps/qm/v1/checks` |
| `ext.customFieldsOnReceipt` | Product-local custom fields service (custom service) |
| `ext.customDispatchNotification` | External: Logistics Partner API |

### View-Model Aggregation

| Screen | Services Aggregated | View-Model |
|--------|-------------------|-----------|
| Goods Receipt Form | pps-im-svc (GR data), pps-pd-svc (material details), bp-svc (supplier name) | `{ goodsReceipt: {...}, material: {name, sku, unit, isRegulated}, supplier: {name, id} }` |
| Put-Away Screen | pps-wm-svc (bin suggestion), pps-im-svc (storage unit) | `{ storageUnit: {...}, suggestedBin: {aisle, rack, level}, material: {name} }` |
| Pick List | pps-wm-svc (pick wave, lines), pps-im-svc (stock per bin) | `{ pickWave: {id, lines: [{material, qty, bin, currentStock}]} }` |
| Stock Overview | pps-im-svc (stock by material/location) | `{ stockEntries: [{material, bin, qty, lastMovement}], totals: {...} }` |
