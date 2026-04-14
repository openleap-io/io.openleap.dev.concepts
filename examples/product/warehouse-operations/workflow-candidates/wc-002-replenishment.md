# Workflow Candidate: WC-002 Automatic Replenishment Trigger

> **Template:** [Workflow Candidate](../../../../artifacts/product/workflow-candidate.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | wc-002-replenishment |
| name | Automatic Replenishment Trigger |
| discoveredFromProcess | pick-and-pack |
| reason | Event-driven automation — when a pick bin drops below a configurable threshold, a replenishment task is automatically created to move stock from bulk storage to the pick bin. This is a reactive, automated pattern that does not fit a manual BPMN process. |
| estimatedTrigger | on event: storage-unit quantity drops below bin replenishment threshold |

---

## Notes

Discovered during analysis of the Pick & Pack process. Workers reported frequently encountering empty pick bins, causing them to walk to bulk storage and perform ad-hoc replenishment during picks. An automated replenishment workflow monitors pick bin levels and triggers a replenishment task when stock drops below a threshold (e.g., 20% of bin capacity). The task is assigned to a Warehouse Worker and involves moving material from a bulk storage bin to the pick-face bin. This keeps pick bins stocked and avoids disrupting the picking flow.
