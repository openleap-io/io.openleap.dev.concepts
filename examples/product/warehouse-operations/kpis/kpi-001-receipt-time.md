# KPI: KPI-001 Goods Receipt Processing Time

> **Template:** [KPI](../../../../artifacts/product/kpi.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | kpi-001-receipt-time |
| name | Goods Receipt Processing Time |
| description | Measures the elapsed time from when a supplier delivery arrives at the warehouse dock to when the goods receipt is posted in the system. Currently averaging ~24 hours due to manual paper processes; target is under 30 minutes with digital scanning. |
| measurement | Time elapsed (minutes) from delivery arrival timestamp to goods receipt posting timestamp, averaged per day |
| target | < 30 minutes (p90) |
| linkedProcesses | goods-receipt |
| linkedCapabilities | cap-001-receive-goods |
