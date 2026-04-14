# Actor: Supplier

> **Template:** [Actor](../../../../concepts/artifacts/product/actor.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | supplier |
| name | Supplier |
| type | external |
| description | External organization that delivers raw materials and goods to the warehouse. Suppliers are managed in the Business Partner (BP) suite and referenced read-only. |
| decisionAuthority | None within warehouse operations. |

---

## Responsibilities

- Deliver goods to the warehouse according to purchase order schedule
- Provide delivery notes with material quantities and lot/batch numbers
- Comply with packaging and labeling requirements

---

## Stakeholder

| Field | Value |
|-------|-------|
| role | External goods provider |
| influence | low |
| interests | Timely unloading, confirmation of delivery acceptance |
| informationNeeds | Delivery time slots, warehouse dock assignments |
