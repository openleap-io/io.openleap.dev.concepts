# Artifact: Quality Requirement

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.4, §9.1 MongoDB Document Model, Freeze schema `quality_requirements`

---

## Definition

A Quality Requirement captures a **non-functional expectation** — it answers "how well must it work?" rather than "how do we build it?" Quality Requirements are categorized by type and prioritized using MoSCoW.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Requirement identifier |
| description | string | ✓ | What the requirement is (e.g., "Booking system must respond < 2s") |
| category | enum | ✓ | performance, availability, security, compliance, scalability, usability |
| measurableTarget | string | | Quantitative target (e.g., "< 2s response time", "99.5% uptime") |
| scope | string or ref | | What this applies to — free text or ref to Process/FunctionalArea/Project |
| priority | enum | ✓ | must, should, could |
| source | string | | Origin of the requirement (e.g., "GDPR Art. 32", "SLA agreement") |

---

## Constraints

1. A QualityRequirement MUST have a category
2. A QualityRequirement MUST have a priority (MoSCoW: must, should, could)
3. A QualityRequirement SHOULD have a measurableTarget (quantitative over qualitative)

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | BusinessProcess, FunctionalArea, or Product | scope (when ref) |
| referenced by | Product | product.qualityRequirements |

---

## Existing Sources

- Elara Spec §4.2 Cat.4 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema `quality_requirements` ✅
- Status: **Fully defined, no gaps**
