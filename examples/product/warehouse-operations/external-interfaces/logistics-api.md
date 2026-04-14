# External Interface: Logistics Partner API

> **Template:** [External Interface](../../../../artifacts/product/external-interface.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | logistics-api |
| name | Logistics Partner API |
| organization | External carrier(s) (e.g., DHL, DB Schenker) |
| type | service |
| direction | outbound |
| description | REST API used to send shipment dispatch notifications to logistics partners. When a shipment is confirmed as dispatched, the system sends shipment details (weight, dimensions, destination, tracking number) to the carrier's API. The carrier returns a tracking URL for shipment monitoring. |
