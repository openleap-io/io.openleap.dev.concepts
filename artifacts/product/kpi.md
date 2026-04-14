# Artifact: KPI

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.4, §9.1 MongoDB Document Model

---

## Definition

A KPI (Key Performance Indicator) is a **measurable indicator** tied to business processes and capabilities, with a measurement method and target. KPIs answer "how do we measure success?" for the business.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | KPI identifier |
| name | string | ✓ | KPI name (e.g., "Durchfallquote Theorieprüfung") |
| description | string | ✓ | What this KPI measures |
| measurement | string | ✓ | How it is measured (e.g., "failed/total per quarter") |
| target | string | ✓ | Target value (e.g., "< 15%") |
| linkedProcesses | ref[] | | Process IDs this KPI measures |
| linkedCapabilities | ref[] | | BusinessCapability IDs this KPI measures |

---

## Constraints

1. A KPI MUST have a measurement method
2. A KPI MUST have a target value

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | BusinessProcess | linkedProcesses |
| references | BusinessCapability | linkedCapabilities |
| referenced by | Product | product.kpis |

---

## Existing Sources

- Elara Spec §4.2 Cat.4 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema: ⚠️ **NOT in Freeze schema — gap**
