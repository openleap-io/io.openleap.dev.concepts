# Business Process: Pick & Pack

> **Template:** [Business Process](../../../../concepts/artifacts/product/business-process.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | pick-and-pack |
| name | Pick & Pack |
| description | Process for picking materials from bin locations based on delivery orders and packing them for outbound shipment — from pick wave release through picking, packing, and labeling. |
| level | L0 |
| functionalAreaRef | outbound |
| triggerType | user_action |
| triggerDescription | Warehouse Manager releases a pick wave |
| frequency | ~350/week (~70/day) |
| sla | < 4 hours from pick wave release to pick completion |
| outcome | Materials picked, packed, and labeled, ready for goods issue |

---

## Happy Path

The Warehouse Manager reviews pending delivery orders and releases a pick wave. The assigned Warehouse Worker receives the pick list on their handheld device. The worker navigates to each bin location and picks the required materials, scanning each item to confirm. After all items are picked, the worker packs them into shipping containers, applies labels, and confirms pick completion in the system.

---

## Exceptions

- **Insufficient stock:** Bin does not contain enough material — worker flags short pick, system suggests alternative bin or partial fulfillment.
- **Wrong material scanned:** Scanned material does not match pick list — system rejects scan and alerts worker.
- **FIFO violation:** Worker attempts to pick a newer lot when older lots exist — system warns per BR-002 for perishable goods.

---

## Steps

### Step 1: Release Pick Wave

| Field | Value |
|-------|-------|
| id | pp-step-1 |
| name | Release pick wave |
| description | Warehouse Manager reviews pending delivery orders, groups them into a pick wave, assigns it to a worker, and releases it. System emits "Pick Wave Released" event. |
| type | userTask |
| actorRef | warehouse-manager |
| order | 1 |
| inputs | Pending delivery orders |
| outputs | Released pick wave with assigned worker |
| businessObjectsRead | pick-wave |
| businessObjectsWritten | pick-wave |
| linkedRules | — |
| linkedTerms | pick-wave |
| linkedInterfaces | erp-sales |

### Step 2: Receive Pick List

| Field | Value |
|-------|-------|
| id | pp-step-2 |
| name | Receive pick list on device |
| description | Assigned Warehouse Worker receives the pick list on their handheld scanner device, showing materials, quantities, and bin locations in optimized walking order. |
| type | receiveTask |
| actorRef | warehouse-worker |
| order | 2 |
| inputs | Pick wave data (materials, quantities, bin locations) |
| outputs | Pick list displayed on device |
| businessObjectsRead | pick-wave, bin-location, material |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | pick-wave, bin-location |
| linkedInterfaces | scanner |

### Step 3: Pick Materials

| Field | Value |
|-------|-------|
| id | pp-step-3 |
| name | Pick materials |
| description | Worker navigates to each bin location and picks the required materials. Each material is scanned to confirm correct pick. For perishable goods, FIFO order is enforced based on lot receipt date. |
| type | userTask |
| actorRef | warehouse-worker |
| order | 3 |
| inputs | Pick list line items with bin locations |
| outputs | Scanned/confirmed picks |
| businessObjectsRead | storage-unit, bin-location, material |
| businessObjectsWritten | storage-unit |
| linkedRules | br-002-fifo |
| linkedTerms | material, fifo, lot-batch, storage-unit |
| linkedInterfaces | scanner |

### Step 4: Pack and Label

| Field | Value |
|-------|-------|
| id | pp-step-4 |
| name | Pack and label |
| description | Worker packs picked materials into shipping containers, applies shipping labels with delivery order reference and destination details. |
| type | manualTask |
| actorRef | warehouse-worker |
| order | 4 |
| inputs | Picked materials, delivery order details |
| outputs | Packed and labeled shipment containers |
| businessObjectsRead | pick-wave |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | — |
| linkedInterfaces | scanner |

### Step 5: Confirm Pick Complete

| Field | Value |
|-------|-------|
| id | pp-step-5 |
| name | Confirm pick complete |
| description | Worker confirms that all items in the pick wave have been picked and packed. System updates pick wave status to "completed" and updates storage unit quantities. |
| type | userTask |
| actorRef | warehouse-worker |
| order | 5 |
| inputs | Pick confirmation scans |
| outputs | Pick wave marked as completed |
| businessObjectsRead | pick-wave |
| businessObjectsWritten | pick-wave, storage-unit |
| linkedRules | — |
| linkedTerms | pick-wave |
| linkedInterfaces | scanner |
