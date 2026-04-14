# Business Rule: BR-002 FIFO Principle for Perishable Goods

> **Template:** [Business Rule](../../../../concepts/artifacts/product/business-rule.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | br-002-fifo |
| name | FIFO Principle for Perishable Goods |
| description | Perishable goods MUST be picked in FIFO (First In, First Out) order based on lot receipt date. The system must suggest the oldest lot first and warn the worker if a newer lot is selected. |
| type | invariant |
| rationale | Prevents material expiry and waste. Industry standard for perishable inventory management. |
| source | Good Distribution Practice (GDP) guidelines |
| appliesToProcesses | pick-and-pack |
| governsCapabilities | cap-003-pick-materials |

---

## Examples

- Lot A (received 2026-03-01) and Lot B (received 2026-03-15) of the same material exist. The pick list must suggest Lot A first. If the worker scans Lot B, the system displays a warning.
- Non-perishable materials (e.g., steel bolts) are not subject to FIFO enforcement, though the system may suggest FIFO as a default.

---

## Exceptions

- If the older lot is in a bin that is physically inaccessible (e.g., blocked by maintenance), the worker may override FIFO with a documented reason.
