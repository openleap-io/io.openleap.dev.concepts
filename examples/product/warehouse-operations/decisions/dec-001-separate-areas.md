# Decision: DEC-001 Separate Inbound and Outbound as Functional Areas

> **Template:** [Decision](../../../../artifacts/product/decision.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | dec-001-separate-areas |
| title | Separate Inbound and Outbound as Functional Areas |
| context | During business process discovery, we identified four main processes: Goods Receipt, Put-Away, Pick & Pack, and Goods Issue. We needed to decide how to group these into functional areas — as a single "Warehouse Operations" area or as separate Inbound/Outbound areas with a shared Storage foundation. |
| decision | Separate Inbound (Goods Receipt, Put-Away) and Outbound (Pick & Pack, Goods Issue) into distinct functional areas, with a shared Storage area for bin management and inventory tracking. |
| rationale | Inbound and Outbound involve different primary actors (Receiving Clerk vs. Warehouse Manager for release), different physical zones (receiving dock vs. dispatch dock), different external interfaces (ERP Procurement vs. ERP Sales), and different KPIs (receipt time vs. pick accuracy). Separating them enables independent process optimization and clearer ownership. The shared Storage area avoids duplicating bin and inventory logic. |
| decidedBy | Product Owner + Warehouse Manager |
| date | 2026-03-15 |
| affectedAreas | inbound, outbound, storage |
| affectedProcesses | goods-receipt, put-away, pick-and-pack, goods-issue |

---

## Alternatives Considered

- **Single "Warehouse" area:** Simpler structure but loses the distinction between inbound and outbound optimization opportunities and ownership.
- **Four areas (one per process):** Too granular, creates unnecessary boundaries between tightly coupled processes (e.g., Pick & Pack and Goods Issue are sequential).
