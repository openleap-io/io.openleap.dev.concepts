# Business Process: Put-Away

> **Template:** [Business Process](../../../../artifacts/product/business-process.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | put-away |
| name | Put-Away |
| description | Process for storing received materials in their assigned bin locations — from system-suggested bin assignment through physical transport to confirmed storage. |
| level | L0 |
| functionalAreaRef | inbound |
| triggerType | business_event |
| triggerDescription | Triggered by "Goods Received" event after goods receipt is posted |
| frequency | ~200/week (1:1 with goods receipts) |
| sla | < 2 hours from goods receipt posting to put-away confirmation |
| outcome | Materials stored in designated bin locations, storage units created, inventory location updated |

---

## Happy Path

After a goods receipt is posted, the system suggests an optimal bin location based on material type, storage requirements, and available capacity. The Warehouse Worker transports the materials to the suggested bin. The worker scans the bin location barcode and the material barcode to confirm placement. The system creates a storage unit record and updates bin utilization.

---

## Exceptions

- **Bin full:** Suggested bin has insufficient capacity — system suggests next-best alternative bin.
- **Wrong storage type:** Material requires cold/hazmat storage but suggested bin is standard — worker rejects suggestion and requests re-assignment.
- **Scanner failure:** Worker cannot scan — manual entry of bin and material IDs as fallback.

---

## Steps

### Step 1: System Suggests Bin Location

| Field | Value |
|-------|-------|
| id | pa-step-1 |
| name | System suggests bin location |
| description | System calculates optimal bin location based on material storage requirements, bin capacity, and proximity to picking zones. |
| type | serviceTask |
| actorRef | receiving-clerk |
| order | 1 |
| inputs | Material details (storageRequirements, weight), current bin utilization |
| outputs | Suggested bin location |
| businessObjectsRead | material, bin-location |
| businessObjectsWritten | — |
| linkedRules | br-003-bin-capacity |
| linkedTerms | bin-location, storage-unit |
| linkedInterfaces | — |

### Step 2: Transport to Bin

| Field | Value |
|-------|-------|
| id | pa-step-2 |
| name | Transport materials to bin |
| description | Warehouse Worker physically transports the materials from the receiving dock to the assigned bin location. Worker's handheld device displays the target bin with aisle/rack/level directions. |
| type | manualTask |
| actorRef | warehouse-worker |
| order | 2 |
| inputs | Suggested bin location (aisle, rack, level, position) |
| outputs | Materials at target bin |
| businessObjectsRead | bin-location |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | bin-location |
| linkedInterfaces | scanner |

### Step 3: Scan Bin and Material

| Field | Value |
|-------|-------|
| id | pa-step-3 |
| name | Scan bin and material |
| description | Worker scans the bin location barcode and then scans each material barcode to confirm the correct materials are placed in the correct bin. |
| type | userTask |
| actorRef | warehouse-worker |
| order | 3 |
| inputs | Physical bin barcode, material barcodes |
| outputs | Scan confirmation (bin + material match) |
| businessObjectsRead | bin-location, material |
| businessObjectsWritten | — |
| linkedRules | — |
| linkedTerms | storage-unit, bin-location, material |
| linkedInterfaces | scanner |

### Step 4: Confirm Put-Away

| Field | Value |
|-------|-------|
| id | pa-step-4 |
| name | Confirm put-away |
| description | System creates storage unit records, updates bin utilization, and confirms put-away. Emits "Material Put Away" event. |
| type | userTask |
| actorRef | warehouse-worker |
| order | 4 |
| inputs | Scan confirmation data |
| outputs | Storage unit created, bin utilization updated |
| businessObjectsRead | bin-location |
| businessObjectsWritten | storage-unit, bin-location |
| linkedRules | br-003-bin-capacity |
| linkedTerms | storage-unit |
| linkedInterfaces | — |
