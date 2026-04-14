# KPI: KPI-003 Warehouse Utilization

> **Template:** [KPI](../../../../concepts/artifacts/product/kpi.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | kpi-003-utilization |
| name | Warehouse Utilization |
| description | Measures the overall percentage of bin capacity in use across the entire warehouse. Utilization that is too low indicates wasted space; too high indicates risk of overflow and inability to receive new goods. |
| measurement | (Sum of all bins' currentUtilization / Sum of all bins' capacity) x 100, measured daily |
| target | 75-90% bin utilization |
| linkedProcesses | goods-receipt, put-away, pick-and-pack, goods-issue |
| linkedCapabilities | cap-005-track-inventory, cap-006-manage-layout |
