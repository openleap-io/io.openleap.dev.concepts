# Domain/Service Spec: pps-im-svc — Inventory Management

> **Template:** [Domain/Service Spec](../../../artifacts/platform/domain-spec.md)
> **Schema:** `service-layer.schema.json`
> **Suite:** [PPS](../pps-suite/suite-spec.md)

---

## Metadata

| Field | Value |
|-------|-------|
| id | pps-im-svc |
| name | Inventory Management Service |
| suite | pps |
| domain | im |
| version | 1.0.0 |
| status | active |
| boundedContextRef | bc:inventory |
| apiBasePath | /api/pps/im/v1 |
| team.name | team-pps-im |

---

## Scope

### Purpose

Records all material movements in and out of the warehouse — goods receipts (inbound from suppliers or production), goods issues (outbound to customers or production consumption), and internal transfers. Maintains real-time stock levels per material and location. The single source of truth for "how much of what is where."

### In Scope

- Goods Receipt posting (from purchase orders, production orders, free receipts)
- Goods Issue posting (for deliveries, production consumption, scrapping)
- Stock level queries (by material, by location, by lot)
- Stock movement history
- Inventory valuation snapshots
- Integration with PPS-WM for bin-level detail

### Out of Scope

- Bin location management (→ pps-wm-svc)
- Pick waves and shipping (→ pps-wm-svc)
- Purchase Order management (→ pps-pur-svc)
- Quality inspections (→ pps-qm-svc, triggered via event)
- Financial postings (→ fi-gl-svc, triggered via event)

---

## Business Context

### Problems Solved

| Problem | Solution | Business Value |
|---------|----------|---------------|
| Inventory data is stale (24h delay from paper-based process) | Real-time stock updates on every movement | Accurate planning, fewer stockouts |
| No traceability of material movements | Every movement recorded with timestamp, actor, source/destination | ISO 9001 compliance, audit trail |
| Stock discrepancies discovered too late | Real-time stock vs. expected comparison | Faster issue detection, higher accuracy |

### Stakeholders

| Role | Interest |
|------|----------|
| Warehouse Worker | Fast, reliable goods receipt and issue confirmation |
| Warehouse Manager | Real-time stock visibility, accuracy KPIs |
| Production Planner | Accurate stock for MRP runs |
| Procurement Officer | Goods receipt confirmation against purchase orders |
| Finance Controller | Accurate valuations for period close |

---

## Domain Model

### Aggregates

#### Aggregate: GoodsReceipt

| Field | Value |
|-------|-------|
| id | agg:goods-receipt |
| name | GoodsReceipt |
| businessPurpose | Records the inbound arrival of materials — from suppliers (against PO), from production (against work order), or free receipt. |

**Root attributes:**

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| goodsReceiptId | string (uuid) | ✓ | Unique GR identifier |
| referenceType | enum: PURCHASE_ORDER, WORK_ORDER, FREE | ✓ | What triggered this receipt |
| referenceId | string | | PO or WO ID if applicable |
| supplierRef | string | | BP supplier ID (if from PO) |
| receivedAt | string (date-time) | ✓ | When goods arrived |
| receivedBy | string | ✓ | IAM user ID of receiving clerk |
| status | enum: PENDING, RECEIVED, POSTED, CANCELLED | ✓ | Current state |
| version | integer | ✓ | Optimistic locking version |

**Root lifecycle:**

| State | → Transitions |
|-------|--------------|
| PENDING | → RECEIVED (goods counted and verified) |
| RECEIVED | → POSTED (quantities confirmed, stock updated) |
| RECEIVED | → CANCELLED (delivery rejected) |
| POSTED | terminal |
| CANCELLED | terminal |

**Root invariants:**

| Rule ID | Description |
|---------|-------------|
| BR-GR-001 | A goods receipt MUST have at least one line |
| BR-GR-002 | Posted goods receipt MUST NOT be modified (immutable after posting) |
| BR-GR-003 | Actual quantity per line MUST be ≥ 0 |

**Root domain events:**

- `pps.im.goodsreceipt.posted` — emitted when status transitions to POSTED

**Entity: GoodsReceiptLine (one-to-many)**

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| lineId | string (uuid) | ✓ | Line identifier |
| materialRef | string | ✓ | Material ID (from pps-pd-svc) |
| expectedQuantity | number (decimal) | | Expected qty (from PO/WO) |
| actualQuantity | number (decimal) | ✓ | Actually received qty |
| unit | string | ✓ | Unit of measure (from T1 si-svc) |
| lotNumber | string | | Lot/batch number if tracked |
| qualityStatus | enum: PENDING, PASSED, FAILED, NOT_REQUIRED | ✓ | Quality check outcome |

#### Aggregate: Stock

| Field | Value |
|-------|-------|
| id | agg:stock |
| name | Stock |
| businessPurpose | Tracks the current quantity of a material at a location. Updated by goods receipt (increment) and goods issue (decrement). The fundamental "how much of what is where" data. |

**Root attributes:**

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| stockId | string (uuid) | ✓ | Unique stock record |
| materialRef | string | ✓ | Material ID |
| locationRef | string | ✓ | Warehouse or bin location |
| quantity | number (decimal) | ✓ | Current on-hand quantity |
| unit | string | ✓ | Unit of measure |
| lotNumber | string | | Lot/batch if tracked |
| lastMovementAt | string (date-time) | ✓ | When stock was last changed |

**Root invariants:**

| Rule ID | Description |
|---------|-------------|
| BR-STK-001 | Stock quantity MUST NOT go below zero (no negative stock) |
| BR-STK-002 | Stock is identified by materialRef + locationRef + lotNumber (composite key) |

#### Aggregate: GoodsIssue

*(Analogous to GoodsReceipt but for outbound — omitted for brevity, same pattern)*

### Shared Types

| Type | Name | Attributes | Used By |
|------|------|-----------|---------|
| type:quantity | Quantity | value (decimal), unit (string) | agg:goods-receipt, agg:stock, agg:goods-issue |

---

## Business Rules

| ID | Name | Description | Enforcement | Error Code |
|----|------|-------------|------------|-----------|
| BR-GR-001 | At least one line | Goods receipt must have ≥ 1 line item | Application layer — CreateGoodsReceiptCommand validation | IM-GR-001 |
| BR-GR-002 | Immutable after posting | Posted GR cannot be modified | Domain layer — aggregate rejects mutations in POSTED state | IM-GR-002 |
| BR-GR-003 | Non-negative quantity | Actual quantity per line ≥ 0 | Domain layer — GoodsReceiptLine constructor | IM-GR-003 |
| BR-STK-001 | No negative stock | Stock quantity must not go below zero | Domain layer — Stock.decrease() throws if result < 0 | IM-STK-001 |
| BR-STK-002 | Composite stock key | Unique by material + location + lot | Infrastructure — database unique constraint | IM-STK-002 |

---

## Contracts

### REST API

**Base path:** `/api/pps/im/v1`
**Auth:** Bearer JWT (OAuth2)

| Endpoint ID | Method | Path | Summary | Role Required | Events Published |
|------------|--------|------|---------|--------------|-----------------|
| endpoint:create-gr | POST | /goods-receipts | Create a new goods receipt | ROLE_IM_CLERK | — |
| endpoint:post-gr | PUT | /goods-receipts/{id}/post | Post (confirm) a goods receipt → updates stock | ROLE_IM_CLERK | pps.im.goodsreceipt.posted |
| endpoint:get-gr | GET | /goods-receipts/{id} | Get goods receipt by ID | ROLE_IM_VIEWER | — |
| endpoint:list-gr | GET | /goods-receipts | List goods receipts (paginated, filtered) | ROLE_IM_VIEWER | — |
| endpoint:get-stock | GET | /stock | Query stock by material / location / lot | ROLE_IM_VIEWER | — |
| endpoint:get-stock-material | GET | /stock/by-material/{materialRef} | Stock for a specific material across all locations | ROLE_IM_VIEWER | — |
| endpoint:create-gi | POST | /goods-issues | Create and post a goods issue → decrements stock | ROLE_IM_CLERK | pps.im.goodsissue.posted |
| endpoint:get-movements | GET | /movements | Stock movement history (paginated) | ROLE_IM_VIEWER | — |

### Events

**Outbound:**

| Event ID | Routing Key | Business Meaning | When Published | Consumers |
|----------|------------|-----------------|----------------|-----------|
| event:gr-posted | pps.im.goodsreceipt.posted | Material has been received and stock updated | After POST /goods-receipts/{id}/post succeeds | pps-pur-svc (confirm against PO), pps-qm-svc (trigger inspection), fi-gl-svc (valuation posting), T4 bi |
| event:gi-posted | pps.im.goodsissue.posted | Material has left the warehouse and stock decremented | After POST /goods-issues succeeds | fi-gl-svc (valuation posting), T4 bi |
| event:stock-changed | pps.im.stock.changed | Stock level changed (any reason) | After any stock mutation | pps-mes-svc (consumption tracking) |

**Inbound:**

| Event ID | Routing Key | Producer | Purpose |
|----------|------------|----------|---------|
| event:po-created | pps.pur.po.created | pps-pur-svc | Set up goods receipt expectation (expected material + qty) |
| event:delivery-created | sd.sd.delivery.created | sd-svc | Trigger goods issue for outbound delivery |

---

## Integration

### Outbound Dependencies

| Service | Type | Criticality | Used For |
|---------|------|------------|----------|
| pps-pd-svc | sync_api | high | Material master data lookup (name, unit, isRegulated) |
| bp-svc | sync_api | medium | Supplier name/details for goods receipt display |
| T1 si-svc | sync_api | low | Unit of measure validation |
| T1 ref-svc | sync_api | low | Reference code lookups |

### Follows Suite Pattern

Yes — event-driven with sync API for data lookups.

---

## Data Model

### Storage Technology

PostgreSQL 16, schema: `pps_im`

### Tables

| Table | Description | Key Columns |
|-------|-------------|------------|
| goods_receipt | Goods receipt header | id (PK), reference_type, reference_id, supplier_ref, status, received_at, received_by, version |
| goods_receipt_line | GR line items | id (PK), goods_receipt_id (FK), material_ref, expected_qty, actual_qty, unit, lot_number, quality_status |
| stock | Current stock levels | id (PK), material_ref, location_ref, lot_number, quantity, unit, last_movement_at. UNIQUE(material_ref, location_ref, lot_number) |
| goods_issue | Goods issue header | id (PK), reference_type, reference_id, status, issued_at, issued_by, version |
| goods_issue_line | GI line items | id (PK), goods_issue_id (FK), material_ref, quantity, unit, lot_number |
| stock_movement | Movement history (audit) | id (PK), movement_type, material_ref, location_ref, quantity, unit, actor, timestamp, source_document_id |
| outbox_events | Transactional outbox | id (PK), event_type, routing_key, payload, created_at, published_at |

---

## Security

| Field | Value |
|-------|-------|
| dataClassification | internal |
| suiteComplianceRefs | ISO 9001 (traceability) |

### Access Control

| Role | Permissions |
|------|------------|
| ROLE_IM_VIEWER | Read: goods receipts, stock, movements |
| ROLE_IM_CLERK | Read + Write: goods receipts, goods issues |
| ROLE_IM_ADMIN | All + configuration |

---

## Feature Dependencies

Platform-Features that call this service:

| Feature ID | Feature Name | Endpoints Used | Mode |
|-----------|-------------|---------------|------|
| F-PPS-IM-001 | Post Goods Receipt | create-gr, post-gr, get-gr, list-gr | FULL (read + write) |
| F-PPS-IM-002 | Stock Overview | get-stock, get-stock-material, get-movements | FULL (read only — no mutations on this feature) |
| F-PPS-WM-002 | Pick & Pack | get-stock (validate picks against stock) | READ_ONLY |
