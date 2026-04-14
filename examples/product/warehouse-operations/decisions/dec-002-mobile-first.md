# Decision: DEC-002 Mobile-First UI for Warehouse Floor

> **Template:** [Decision](../../../../artifacts/product/decision.md)

---

## Fields

| Field | Value |
|-------|-------|
| id | dec-002-mobile-first |
| title | Mobile-First UI for Warehouse Floor |
| context | Warehouse workers and receiving clerks spend their entire shift on the warehouse floor, moving between docks, aisles, and packing stations. We needed to decide the primary UI platform for these users — desktop workstations at fixed locations, native mobile app, or mobile-first web UI on handheld scanners. |
| decision | Build a mobile-first web UI optimized for the Zebra TC52 handheld scanner's screen and Android WebView. The Warehouse Manager dashboard will be a responsive web UI usable on both tablet and desktop. |
| rationale | Workers are constantly in motion and cannot return to fixed workstations for data entry (this is the root cause of the current paper-based workaround). The existing Zebra TC52 scanners have Android WebView capability, so a mobile-first web UI avoids the cost and deployment complexity of a native app while reusing existing hardware. The manager's dashboard has different needs (larger screen, more data) and will be responsive rather than mobile-first. |
| decidedBy | Product Owner + IT Architecture |
| date | 2026-03-18 |
| affectedAreas | inbound, outbound |
| affectedProcesses | goods-receipt, put-away, pick-and-pack, goods-issue |

---

## Alternatives Considered

- **Desktop-only:** Rejected because workers would still need paper on the floor, defeating the purpose of the system.
- **Native Android app:** Higher fidelity but adds app store deployment, versioning, and maintenance overhead. WebView is sufficient for scan + confirm workflows.
- **Hybrid (desktop + paper for floor):** The status quo, which is the problem we are solving.
