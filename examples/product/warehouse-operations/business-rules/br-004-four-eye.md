# Business Rule: BR-004 Four-Eye Principle for High-Value Goods

> **Template:** [Business Rule](../../../../concepts/artifacts/product/business-rule.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | br-004-four-eye |
| name | Four-Eye Principle for High-Value Goods |
| description | Goods receipts for materials with a total line value exceeding 10,000 EUR require confirmation by a Warehouse Manager in addition to the Receiving Clerk. The manager must review and approve the receipt before it can be posted. |
| type | authorization |
| rationale | Financial controls require dual approval for high-value transactions to prevent errors and fraud. |
| source | Internal audit policy, financial controls framework |
| appliesToProcesses | goods-receipt |
| governsCapabilities | cap-001-receive-goods |

---

## Examples

- A delivery of precision instruments worth 25,000 EUR arrives. The Receiving Clerk confirms quantities, but the goods receipt cannot be posted until the Warehouse Manager reviews and approves it.
- A delivery of standard screws worth 150 EUR arrives. No additional approval is needed; the Receiving Clerk can post the goods receipt directly.

---

## Exceptions

- If the Warehouse Manager is unavailable and the goods receipt is time-critical (e.g., production stoppage), a deputy with WH_MANAGER IAM role may approve.
