# Business Rule: BR-003 Bin Capacity Must Not Be Exceeded

> **Template:** [Business Rule](../../../../artifacts/product/business-rule.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | br-003-bin-capacity |
| name | Bin Capacity Must Not Be Exceeded |
| description | A put-away MUST NOT exceed the bin's remaining capacity. The system must validate that (currentUtilization + incoming quantity) <= capacity before suggesting or confirming a bin assignment. |
| type | validation |
| rationale | Prevents physical overloading of storage racks, which is a safety hazard and leads to material damage. |
| source | Workplace safety regulations, warehouse operational standards |
| appliesToProcesses | put-away |
| governsCapabilities | cap-002-store-materials |

---

## Examples

- Bin A-03-2-1 has capacity 500 kg and currentUtilization 420 kg. A put-away of 100 kg is rejected because 420 + 100 = 520 > 500. The system suggests an alternative bin.
- Bin B-01-1-3 has capacity 200 pcs and currentUtilization 50 pcs. A put-away of 100 pcs is accepted because 50 + 100 = 150 <= 200.

---

## Exceptions

- None. This rule has no exceptions — bin capacity must never be exceeded.
