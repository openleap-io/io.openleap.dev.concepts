# Operating Model

The binding operating model for all spec-driven work on the OpenLeap platform. Defines **who does what, when, with which artifact, at which gate** — tool-neutral, dual-mode (solo + team), without prescribing specific agent frameworks or tracker tools.

## Contents

| File | Document Type | Authority |
|---|---|---|
| [L0-operating-doctrine.md](L0-operating-doctrine.md) | GOV-OM-001 — Operating Doctrine | **Binding rules** for all roles, phases, gates, artifact flow, and enforcement |
| [custody.md](custody.md) | GOV-OM-002 — Artifact Custody Catalog | Canonical location and stewardship per artifact class |
| [agent-roles.md](agent-roles.md) | GOV-OM-003 — Agent Roles Catalog | Accumulating catalog of agent roles introduced by L1 specs |
| [L1-roadmap.md](L1-roadmap.md) | GOV-OM-004 — L1 Index | Overview and sequencing of the nine L1 detail specs |
| [L1/*.md](L1/) | GOV-OM-L1-01 … 09 — L1 Skeletons | Detail specs that operationalize L0 |

## Templates

Operating-model-specific artifact templates live in [`../templates/operating-model/`](../templates/operating-model/):
- `intent-template.md` — Intent artifacts (Conceive phase)
- `cve-record-template.md` — CVE-Record ledger entries
- `deployment-record-template.md` — immutable deploy records
- `improvement-proposal-template.md` — Learn-phase outputs

## How to read

1. Start with **L0-operating-doctrine.md** — the binding frame.
2. Consult **custody.md** when you need to know where an artifact lives or who curates a class.
3. Consult **agent-roles.md** to see which agent roles are currently defined (starts empty; grows with L1).
4. For operational details on a specific process (spec authoring, deployment, CVE response, etc.), consult the matching **L1** skeleton and the L1-roadmap for dependencies.
5. For the RACI of any action: L0 §8 (aggregated) or the specific L1 (refined).

## Status

- L0: PROPOSED
- L1 skeletons: SKELETON — awaiting content per the roadmap
- Templates: PROPOSED

All documents are amendable via the Meta-Gate (L0 §13 and §6.4).
