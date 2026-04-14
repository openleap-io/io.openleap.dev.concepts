# Actor: Logistics Partner

> **Template:** [Actor](../../../../concepts/artifacts/product/actor.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | logistics-partner |
| name | Logistics Partner |
| type | external |
| description | External carrier responsible for picking up and transporting outbound shipments to customers. Receives dispatch notifications via the Logistics Partner API. |
| decisionAuthority | None within warehouse operations. Responsible for transportation planning and route optimization (out of scope). |

---

## Responsibilities

- Collect outbound shipments from the warehouse
- Provide tracking numbers for dispatched shipments
- Confirm delivery to end customer

---

## Stakeholder

| Field | Value |
|-------|-------|
| role | External transport provider |
| influence | low |
| interests | Timely shipment readiness, accurate shipment documentation, tracking data |
| informationNeeds | Shipment details, weight, dimensions, pickup schedule |
