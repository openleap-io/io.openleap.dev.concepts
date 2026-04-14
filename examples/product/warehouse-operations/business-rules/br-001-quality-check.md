# Business Rule: BR-001 Quality Check Required for Regulated Materials

> **Template:** [Business Rule](../../../../concepts/artifacts/product/business-rule.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | br-001-quality-check |
| name | Quality Check Required for Regulated Materials |
| description | Regulated materials (isRegulated=true) MUST undergo a quality check before they can be put away in the warehouse. The quality check must be performed during goods receipt and documented for audit purposes. |
| type | invariant |
| rationale | ISO 9001 compliance requires documented quality verification of regulated materials upon receipt. |
| source | ISO 9001:2015, Section 8.6 — Release of products and services |
| appliesToProcesses | goods-receipt |
| governsCapabilities | cap-001-receive-goods |

---

## Examples

- A shipment of chemical solvents (isRegulated=true) arrives. The Receiving Clerk must perform a quality check verifying material condition, labeling, and safety data sheet compliance before confirming receipt.
- A shipment of standard packaging materials (isRegulated=false) arrives. No quality check is required; the goods receipt proceeds directly to quantity confirmation.

---

## Exceptions

- Emergency receipt: In cases where production is halted waiting for regulated material, the Warehouse Manager may authorize a conditional receipt with quality check deferred to within 4 hours. This exception must be documented.
