# Actor: Receiving Clerk

> **Template:** [Actor](../../../../concepts/artifacts/product/actor.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | receiving-clerk |
| name | Receiving Clerk |
| type | internal |
| description | Specialized warehouse employee responsible for the administrative side of goods receipt — verifying delivery notes against purchase orders, scanning documentation, coordinating quality checks, and posting receipts in the system. |
| decisionAuthority | Can accept or reject deliveries based on purchase order matching. Can flag materials for quality check. |

---

## Responsibilities

- Receive and verify delivery documentation from suppliers
- Scan delivery notes and match against purchase orders
- Coordinate quality checks for regulated materials
- Confirm received quantities in the system
- Post goods receipts to trigger downstream processes
- Handle discrepancies between expected and actual deliveries

---

## Stakeholder

| Field | Value |
|-------|-------|
| role | Inbound operations specialist |
| influence | medium |
| interests | Accurate receipt data, fast PO matching, clear escalation paths for discrepancies |
| informationNeeds | Purchase order details, expected delivery schedule, material specifications, quality requirements |
