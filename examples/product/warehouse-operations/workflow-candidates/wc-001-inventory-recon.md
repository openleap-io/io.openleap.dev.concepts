# Workflow Candidate: WC-001 Nightly Inventory Reconciliation

> **Template:** [Workflow Candidate](../../../../artifacts/product/workflow-candidate.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | wc-001-inventory-recon |
| name | Nightly Inventory Reconciliation |
| discoveredFromProcess | goods-receipt |
| reason | Recurring batch pattern — runs on a fixed schedule (nightly), compares two data sources (system inventory vs. expected stock based on movements), and produces a discrepancy report. This does not fit a BPMN user-task model. |
| estimatedTrigger | cron 23:00 daily (after warehouse closes at 22:00) |

---

## Notes

Discovered during analysis of inventory accuracy requirements (QR-003). Even with scan-based tracking, small errors can accumulate (e.g., scanner failures, manual overrides). A nightly batch job compares system stock quantities against expected values computed from the day's movements. Any discrepancy above a configurable threshold (e.g., > 1%) is flagged for the Warehouse Manager to investigate the next morning. Output is a discrepancy report available on the manager dashboard.
