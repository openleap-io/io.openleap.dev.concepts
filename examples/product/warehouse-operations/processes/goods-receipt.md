# Business Process: Goods Receipt

> **Template:** [Business Process](../../../../concepts/artifacts/product/business-process.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | goods-receipt |
| name | Goods Receipt |
| description | End-to-end process for receiving inbound goods — from the moment a supplier delivery arrives at the dock until the goods receipt is posted in the system and downstream processes are triggered. |
| level | L0 |
| functionalAreaRef | inbound |
| triggerType | external_signal |
| triggerDescription | Supplier delivery arrives at warehouse dock |
| frequency | ~200/week (~40/day) |
| sla | < 30 minutes from arrival to posting |
| outcome | Goods receipt posted, inventory updated, put-away triggered |

---

## Happy Path

A supplier delivery arrives at the warehouse dock. The Receiving Clerk scans the delivery note barcode, and the system matches it against the corresponding Purchase Order. Warehouse Workers unload the goods and count the materials. If the material is regulated, a quality check is performed. The Receiving Clerk confirms the received quantities in the system. The goods receipt is posted, inventory is updated, and a "Goods Received" event triggers the Put-Away process.

---

## Exceptions

- **PO mismatch:** Delivery note does not match any open Purchase Order — Receiving Clerk rejects delivery or escalates to Warehouse Manager.
- **Quantity discrepancy:** Actual quantity differs from expected — Receiving Clerk records actual quantity and flags discrepancy for procurement.
- **Quality rejection:** Regulated material fails quality check — material is quarantined, supplier notified, goods receipt not posted for rejected items.
- **High-value approval:** Materials with value > 10,000 EUR require Warehouse Manager confirmation before posting (BR-004).

---

## Steps

### Step 1: Delivery Arrives

| Field | Value |
|-------|-------|
| id | gr-step-1 |
| name | Delivery arrives at dock |
| description | Supplier delivers goods to the warehouse receiving dock. Delivery note is handed to the Receiving Clerk. |
| type | receiveTask |
| actorRef | supplier |
| order | 1 |
| inputs | Delivery note (paper or electronic) |
| outputs | Delivery note available for scanning |
| businessObjectsRead | — |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | goods-receipt |
| linkedInterfaces | — |

### Step 2: Scan Delivery Note

| Field | Value |
|-------|-------|
| id | gr-step-2 |
| name | Scan delivery note |
| description | Receiving Clerk scans the delivery note barcode using a handheld scanner. System retrieves the corresponding Purchase Order for matching. |
| type | userTask |
| actorRef | receiving-clerk |
| order | 2 |
| inputs | Delivery note barcode |
| outputs | Purchase Order data displayed for verification |
| businessObjectsRead | — |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | goods-receipt |
| linkedInterfaces | scanner, erp-procurement |

### Gateway: PO Match Check

| Field | Value |
|-------|-------|
| id | gw-po-match |
| name | Does delivery note match a Purchase Order? |
| type | exclusive |
| afterStepId | gr-step-2 |

| Condition | Expression | Target |
|-----------|-----------|--------|
| PO matches | deliveryNote.poNumber matches openPurchaseOrder | gr-step-3 |
| No PO match | deliveryNote.poNumber not found in openPurchaseOrders | END (reject delivery) |

### Step 3: Unload and Count

| Field | Value |
|-------|-------|
| id | gr-step-3 |
| name | Unload and count materials |
| description | Warehouse Workers unload goods from the delivery vehicle and count materials per line item, comparing against the delivery note quantities. |
| type | manualTask |
| actorRef | warehouse-worker |
| order | 3 |
| inputs | Purchase Order line items with expected quantities |
| outputs | Actual quantities per material |
| businessObjectsRead | material |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | material |
| linkedInterfaces | scanner |

### Step 4: Quality Check

| Field | Value |
|-------|-------|
| id | gr-step-4 |
| name | Quality check |
| description | For regulated materials (isRegulated=true), a quality check is performed to verify material condition and compliance. Non-regulated materials skip this step. |
| type | userTask |
| actorRef | receiving-clerk |
| order | 4 |
| inputs | Material details, quality criteria |
| outputs | Quality check result (pass/fail) |
| businessObjectsRead | material |
| businessObjectsWritten | — |
| linkedRules | br-001-quality-check |
| linkedTerms | material |
| linkedInterfaces | — |

### Gateway: Quality Result

| Field | Value |
|-------|-------|
| id | gw-quality-result |
| name | Quality check passed? |
| type | exclusive |
| afterStepId | gr-step-4 |

| Condition | Expression | Target |
|-----------|-----------|--------|
| Quality OK | qualityCheck.result == PASS | gr-step-5 |
| Quality rejected | qualityCheck.result == FAIL | END (quarantine material) |

### Step 5: Confirm Quantities

| Field | Value |
|-------|-------|
| id | gr-step-5 |
| name | Confirm quantities in system |
| description | Receiving Clerk enters or confirms the actual received quantities in the system, noting any discrepancies against the Purchase Order. |
| type | userTask |
| actorRef | receiving-clerk |
| order | 5 |
| inputs | Actual counted quantities, Purchase Order expected quantities |
| outputs | Confirmed goods receipt lines |
| businessObjectsRead | material |
| businessObjectsWritten | goods-receipt |
| linkedRules | — |
| linkedTerms | goods-receipt |
| linkedInterfaces | scanner |

### Step 6: Post Goods Receipt

| Field | Value |
|-------|-------|
| id | gr-step-6 |
| name | Post goods receipt |
| description | Goods receipt is posted in the system. For high-value materials (> 10,000 EUR), Warehouse Manager confirmation is required (four-eye principle). Posting updates inventory and emits the "Goods Received" event. |
| type | userTask |
| actorRef | receiving-clerk |
| order | 6 |
| inputs | Confirmed goods receipt with all lines |
| outputs | Posted goods receipt, inventory updated |
| businessObjectsRead | goods-receipt |
| businessObjectsWritten | goods-receipt |
| linkedRules | br-004-four-eye |
| linkedTerms | goods-receipt |
| linkedInterfaces | — |
