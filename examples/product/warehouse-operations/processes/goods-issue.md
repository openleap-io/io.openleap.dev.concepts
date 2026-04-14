# Business Process: Goods Issue

> **Template:** [Business Process](../../../../concepts/artifacts/product/business-process.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | goods-issue |
| name | Goods Issue |
| description | Process for dispatching outbound shipments — from shipment creation through loading, scanning, dispatch confirmation, and logistics partner notification. |
| level | L0 |
| functionalAreaRef | outbound |
| triggerType | business_event |
| triggerDescription | Pick wave completed, packed goods ready for dispatch |
| frequency | ~350/week (~70/day) |
| sla | < 2 hours from pick completion to dispatch |
| outcome | Shipment dispatched, logistics partner notified, inventory updated |

---

## Happy Path

After pick and pack is complete, a shipment is created from the delivery order. The Warehouse Worker loads the packed goods onto the logistics partner's vehicle. The worker scans the shipment barcode to confirm all items are loaded. Dispatch is confirmed in the system, and a "Shipment Dispatched" event is emitted, triggering a notification to the logistics partner with tracking details.

---

## Exceptions

- **Incomplete shipment:** Not all items from the pick wave are available — partial shipment is created, remaining items back-ordered.
- **Logistics partner no-show:** Carrier does not arrive within scheduled window — Warehouse Manager escalates and reschedules.
- **Scan mismatch:** Scanned shipment contents do not match expected — worker re-checks contents before dispatch.

---

## Steps

### Step 1: Create Shipment

| Field | Value |
|-------|-------|
| id | gi-step-1 |
| name | Create shipment from delivery order |
| description | System creates a shipment record from the completed pick wave and associated delivery order, linking it to the assigned logistics partner. |
| type | serviceTask |
| actorRef | warehouse-manager |
| order | 1 |
| inputs | Completed pick wave, delivery order details |
| outputs | Shipment record created |
| businessObjectsRead | pick-wave |
| businessObjectsWritten | shipment |
| linkedRules | — |
| linkedTerms | goods-issue |
| linkedInterfaces | erp-sales |

### Step 2: Load Goods

| Field | Value |
|-------|-------|
| id | gi-step-2 |
| name | Load goods onto vehicle |
| description | Warehouse Worker loads the packed and labeled containers onto the logistics partner's vehicle at the dispatch dock. |
| type | manualTask |
| actorRef | warehouse-worker |
| order | 2 |
| inputs | Packed shipment containers, vehicle assignment |
| outputs | Goods loaded on vehicle |
| businessObjectsRead | shipment |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | goods-issue |
| linkedInterfaces | — |

### Step 3: Scan Shipment

| Field | Value |
|-------|-------|
| id | gi-step-3 |
| name | Scan shipment |
| description | Worker scans the shipment barcode and each container barcode to confirm all items are loaded and match the shipment record. |
| type | userTask |
| actorRef | warehouse-worker |
| order | 3 |
| inputs | Shipment barcode, container barcodes |
| outputs | Scan confirmation (all items verified) |
| businessObjectsRead | shipment |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | goods-issue |
| linkedInterfaces | scanner |

### Step 4: Confirm Dispatch

| Field | Value |
|-------|-------|
| id | gi-step-4 |
| name | Confirm dispatch |
| description | Worker confirms dispatch in the system. Shipment status is updated to "dispatched" and inventory is decremented. |
| type | userTask |
| actorRef | warehouse-worker |
| order | 4 |
| inputs | Scan confirmation |
| outputs | Shipment dispatched, inventory updated |
| businessObjectsRead | shipment |
| businessObjectsWritten | shipment |
| linkedRules | — |
| linkedTerms | goods-issue |
| linkedInterfaces | — |

### Step 5: Notify Logistics Partner

| Field | Value |
|-------|-------|
| id | gi-step-5 |
| name | Notify logistics partner |
| description | System sends dispatch notification to the logistics partner via the Logistics Partner API, including shipment details and tracking number. Emits "Shipment Dispatched" event. |
| type | sendTask |
| actorRef | logistics-partner |
| order | 5 |
| inputs | Shipment details, tracking number |
| outputs | Dispatch notification sent |
| businessObjectsRead | shipment |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | goods-issue |
| linkedInterfaces | logistics-api |
