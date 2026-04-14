# External Interface: ERP Procurement (Purchase Orders)

> **Template:** [External Interface](../../../../concepts/artifacts/product/external-interface.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | erp-procurement |
| name | ERP Procurement (Purchase Orders) |
| organization | PPS-PUR (Procurement module) |
| type | system |
| direction | inbound |
| description | Provides purchase order data consumed read-only by warehouse operations. When a delivery note is scanned during goods receipt, the system retrieves the corresponding purchase order from PPS-PUR to verify expected materials and quantities. Data includes PO number, line items, material references, expected quantities, and supplier reference. |
