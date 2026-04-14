# Artifact: Business Capability

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.5, §9.1 MongoDB Document Model, Freeze schema `business_capabilities`

---

## Definition

A Business Capability answers **"what can the business do?"** — independent of how it is done. Capabilities are stable even when processes change. A business "can enroll students" regardless of whether enrollment is paper-based or digital.

**KEY ROLE:** Business Capabilities are the **primary input for Feature Resolution**. The Product declares `requiredCapabilities` (a list of BusinessCapability IDs) which the Platform resolves into concrete platform-features.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Capability identifier |
| name | string | ✓ | Capability name (e.g., "Enroll Students") |
| description | string | ✓ | What this capability represents |
| type | enum | ✓ | core, supporting, generic |
| priority | enum | ✓ | high, medium, low |
| functionalAreaRef | ref | | Which Functional Area this belongs to |
| fulfilledByProcesses | ref[] | | Process IDs that fulfill this capability |
| requiresCapabilities | ref[] | | Other BusinessCapability IDs this one depends on |
| governedByRules | ref[] | | BusinessRule IDs that govern this capability |

---

## Constraints

1. A BusinessCapability MUST have a type (core, supporting, or generic)
2. A BusinessCapability MUST have a priority
3. requiresCapabilities MUST NOT contain circular references

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | BusinessProcess | fulfilledByProcesses |
| references | BusinessCapability | requiresCapabilities |
| references | BusinessRule | governedByRules |
| references | FunctionalArea | functionalAreaRef |
| referenced by | Product | product.requiredCapabilities (input for Feature Resolution) |
| referenced by | BusinessRule | rule.governsCapabilities |
| referenced by | KPI | kpi.linkedCapabilities |
| referenced by | Decision | decision.affectedAreas (indirect) |

---

## Existing Sources

- Elara Spec §4.2 Cat.5 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema `business_capabilities` ✅
- Status: **Fully defined, no gaps**
