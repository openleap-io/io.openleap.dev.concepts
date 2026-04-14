# Suite Spec: PPS — Production, Planning & Shopfloor

> **Template:** [Suite Spec](../../../artifacts/platform/suite-spec.md)
> **Schema:** `suite-layer.schema.json`

---

## Metadata

| Field | Value |
|-------|-------|
| id | pps |
| name | Production, Planning & Shopfloor |
| description | Manufacturing ERP suite covering product definition, planning, procurement, production execution, inventory, warehousing, quality, and asset management. |
| version | 1.0.0 |
| status | active |
| owner.team | team-pps |
| owner.email | pps-team@openleap.io |
| boundedContexts | bc:product-definition, bc:material-planning, bc:procurement, bc:manufacturing, bc:inventory, bc:warehousing, bc:quality, bc:asset-management |

---

## Purpose

### Business Purpose

PPS enables manufacturing companies to define their products, plan material and production requirements, procure raw materials, execute manufacturing operations, manage inventory and warehouse logistics, ensure quality, and maintain production assets — from order-to-delivery in a single cohesive suite.

### In Scope

- Product master data: BOMs, routings, production versions
- Material requirements planning (MRP)
- Procurement: purchase orders, supplier management integration (via BP)
- Manufacturing execution: work orders, confirmations, shop floor control
- Inventory management: stock movements, goods receipts, goods issues, valuations
- Warehouse management: bin locations, put-away, picking, packing, shipping
- Quality management: inspection lots, quality checks, defect recording
- Asset management: equipment, maintenance schedules, downtime tracking

### Out of Scope

- Finance & Accounting (→ FI suite): cost accounting, valuations, GL postings triggered via events
- Sales & Distribution (→ SD suite): sales orders, pricing, customer management
- Business Partner master data (→ BP suite, T2): supplier/customer records consumed via API
- HR & Workforce management (→ HR suite)
- Business Intelligence / Analytics (→ T4 BI): consumes PPS events

### Target Users

| Role | Interest |
|------|----------|
| Production Planner | MRP runs, production scheduling, capacity planning |
| Shop Floor Operator | Work order execution, confirmations, quality checks |
| Warehouse Worker | Goods receipt, put-away, picking, shipping |
| Warehouse Manager | Bin layout, inventory levels, throughput KPIs |
| Procurement Officer | Purchase orders, supplier delivery tracking |
| Quality Inspector | Inspection lots, defect analysis |

---

## Ubiquitous Language

The UBL is the defining characteristic of PPS as a suite. All domains within PPS share these terms with consistent meaning.

| ID | Term | Aliases | Definition |
|----|------|---------|-----------|
| pps:glossary:material | Material | Item, Part, Component | A tangible item tracked by the suite — can be raw material, semi-finished, or finished goods. Identified by SKU, measured in a base unit (pcs, kg, m). Every PPS domain uses "Material" to mean the same thing. |
| pps:glossary:bom | Bill of Materials (BOM) | Parts List, Recipe | Hierarchical list of materials and quantities needed to produce one unit of a product. Owned by pd, referenced by mrp, mes, qm. |
| pps:glossary:work-order | Work Order | Production Order, Manufacturing Order | An instruction to produce a specific quantity of a material. Created by mrp, executed by mes, confirmed against by im (goods receipt of produced goods). |
| pps:glossary:stock | Stock | Inventory, On-Hand | Quantity of a material at a specific location. Managed by im, queried by mrp (for planning), wm (for bin-level detail), mes (for consumption). |
| pps:glossary:goods-receipt | Goods Receipt | GR, Inbound Delivery | Recording of material arriving at the warehouse — from a supplier (purchase order), from production (work order), or from a transfer. Owned by im. |
| pps:glossary:goods-issue | Goods Issue | GI, Outbound Delivery | Recording of material leaving the warehouse — for a customer delivery, for production consumption, or for scrapping. Owned by im. |
| pps:glossary:bin-location | Bin Location | Storage Bin, Slot | A specific physical position in the warehouse identified by aisle-rack-level-position. Owned by wm. Referenced by im (stock-per-bin queries). |
| pps:glossary:lot-batch | Lot / Batch | Charge, Batch Number | A tracked group of identical material produced or received together. Enables traceability (which batch was used where) and FIFO/FEFO strategies. |
| pps:glossary:pick-wave | Pick Wave | Wave, Pick Run | A grouped set of pick tasks released together for efficiency. Created by wm, driven by delivery orders from SD. |
| pps:glossary:routing | Routing | Operation Plan | Sequence of manufacturing operations needed to produce a material, with work centers, setup times, and run times. Owned by pd, used by mes. |

**UBL Boundary Test:** FI uses "Posting" (a debit/credit entry) — PPS uses "Goods Receipt" (a physical material movement). Same business event, different vocabulary. This confirms PPS and FI are separate suites.

---

## Domain Model

### Core Concepts

| Concept | Owner (Service) | Description |
|---------|----------------|-------------|
| Product | pps-pd-svc | The definition of what can be manufactured: BOM, routing, production version |
| Material | pps-pd-svc (master), pps-im-svc (stock) | Master data owned by pd, stock quantities managed by im |
| Purchase Order | pps-pur-svc | Procurement document for acquiring materials from suppliers |
| Work Order | pps-mes-svc | Production execution instruction |
| Stock | pps-im-svc | Material quantities at locations |
| Bin Location | pps-wm-svc | Physical warehouse positions |
| Inspection Lot | pps-qm-svc | Quality check container |

### Shared Kernel

| Concept | Owner | Shared With | Mechanism |
|---------|-------|-------------|-----------|
| Material (identity + base attributes) | pps-pd-svc | All PPS services | API lookup |
| Unit of Measure | T1 si-svc | All PPS services | API lookup |
| Lot/Batch (identity + traceability) | pps-im-svc | pps-wm-svc, pps-qm-svc, pps-mes-svc | Event + API |

### Bounded Context Map (intra-suite)

| Upstream | Downstream | Pattern | Description |
|----------|-----------|---------|-------------|
| bc:product-definition | bc:material-planning | customer-supplier | pd releases products, mrp plans them |
| bc:product-definition | bc:manufacturing | customer-supplier | pd provides routings, mes executes them |
| bc:material-planning | bc:procurement | customer-supplier | mrp generates purchase proposals, pur creates POs |
| bc:procurement | bc:inventory | customer-supplier | pur creates POs, im receives goods against them |
| bc:inventory | bc:warehousing | shared-kernel | im manages stock quantities, wm manages bin-level placement |
| bc:inventory | bc:quality | customer-supplier | im flags goods receipt, qm inspects |
| bc:warehousing | bc:inventory | conformist | wm confirms movements, im updates stock |

---

## Service Landscape

| Service ID | Name | Bounded Context | Status | Responsibility |
|-----------|------|----------------|--------|----------------|
| pps-pd-svc | Product Definition | bc:product-definition | active | Product master data: materials, BOMs, routings, production versions |
| pps-mrp-svc | Material Planning | bc:material-planning | planned | MRP runs, purchase proposals, production proposals |
| pps-pur-svc | Procurement | bc:procurement | planned | Purchase orders, supplier delivery tracking, invoice matching |
| pps-mes-svc | Manufacturing Execution | bc:manufacturing | planned | Work orders, shop floor operations, confirmations |
| pps-im-svc | Inventory Management | bc:inventory | active | Stock movements, goods receipts, goods issues, valuations |
| pps-wm-svc | Warehouse Management | bc:warehousing | active | Bin locations, put-away, picking, packing, shipping |
| pps-qm-svc | Quality Management | bc:quality | planned | Inspection lots, quality checks, defect recording |
| pps-eam-svc | Asset Management | bc:asset-management | planned | Equipment, maintenance, downtime |

---

## Integration Patterns

### Pattern Decision

| Field | Value |
|-------|-------|
| pattern | event_driven |
| rationale | PPS domains are loosely coupled — pd releases products, mrp reacts; pur creates POs, im reacts. Events enable each domain to evolve independently. Sync API used only for data lookups (material details, stock queries). |

### Key Event Flows

**Product Release Flow:**
```
pps-pd-svc --[pps.pd.product.released]--> pps-mrp-svc, pps-pur-svc, pps-mes-svc, pps-im-svc
```

**Procurement-to-Inventory Flow:**
```
pps-pur-svc --[pps.pur.po.created]--> pps-im-svc (set up goods receipt expectation)
pps-im-svc --[pps.im.goodsreceipt.posted]--> pps-pur-svc (confirm receipt against PO)
                                          --> pps-qm-svc (trigger quality inspection)
```

**Warehouse-to-Delivery Flow:**
```
pps-wm-svc --[pps.wm.pickwave.released]--> (worker picks)
pps-wm-svc --[pps.wm.shipment.dispatched]--> sd (delivery confirmation)
```

---

## Event Conventions

### Routing Key Pattern

| Segment | Description | Example |
|---------|-------------|---------|
| {suite} | Always `pps` | pps |
| {domain} | Domain short code | im, wm, pd, pur, mes, qm |
| {aggregate} | Aggregate root name | goodsreceipt, pickwave, product |
| {action} | Past tense verb | posted, released, created, dispatched |

Pattern: `pps.{domain}.{aggregate}.{action}`

Examples: `pps.im.goodsreceipt.posted`, `pps.wm.pickwave.released`, `pps.pd.product.released`

### Payload Envelope

```json
{
  "eventId": "uuid",
  "eventType": "pps.im.goodsreceipt.posted",
  "timestamp": "ISO-8601",
  "tenantId": "string",
  "correlationId": "uuid",
  "producer": "pps-im-svc",
  "schemaVersion": "1.0.0",
  "payload": { }
}
```

---

## Cross-Cutting Concerns

### Multi-Tenancy

| Field | Value |
|-------|-------|
| model | shared_schema |
| isolation | Row-Level Security via `tenant_id` on all tables |
| propagation | JWT claim `tenant_id` → propagated in event envelope |

### Compliance

| Regulation | Requirement | Implementation |
|-----------|-------------|----------------|
| ISO 9001 | Material movements must be traceable | Every stock movement has timestamp, actor, source/destination, lot reference |
| GDPR | Minimal personal data in warehouse operations | Only IAM user IDs stored, no personal data in PPS |

---

## External Interfaces

### Outbound (PPS → other suites)

| Target Suite | Interface Type | Description |
|-------------|---------------|-------------|
| fi | event | Goods receipt posted → triggers FI material valuation posting |
| sd | event | Shipment dispatched → confirms delivery for SD |
| T4 bi | event | All PPS events → analytics ingestion |

### Inbound (other suites → PPS)

| Source Suite | Interface Type | Description |
|-------------|---------------|-------------|
| sd | event | Delivery order created → triggers pick wave in wm |
| bp | API | Business Partner lookup for supplier data on goods receipt |
| T1 ref | API | Reference data lookup for codes/labels |
| T1 si | API | SI unit lookup for material units of measure |
