# Artifact: Persona

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Enhancement `concepts/agora/elara-product-discovery-enhancement.md` §3.2

---

## Definition

A Persona enriches an Actor/Stakeholder with **UX-relevant attributes** — it is the product-level view of "who uses this." Personas are derived from business Actors discovered during elicitation and enriched with usage frequency, technical comfort, pain points, and IAM role mappings.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Persona identifier (e.g., "persona-sales-clerk") |
| name | string | ✓ | Persona name (e.g., "Sales Clerk") |
| actorRefs | ref[] | ✓ | Actor IDs this persona is derived from (min. 1) |
| iamRoles | string[] | | IAM role identifiers (e.g., ["SD_USER"]) |
| jobToBeDone | string | ✓ | Core job-to-be-done (e.g., "Enter and track customer orders") |
| usageFrequency | enum | ✓ | DAILY, WEEKLY, OCCASIONAL, RARE |
| technicalComfort | enum | ✓ | LOW, MEDIUM, HIGH |
| painPoints | string[] | | Pain points discovered during interviews |
| workarounds | string[] | | What they do today to work around problems |

---

## Constraints

1. A Persona MUST reference at least one Actor (actorRefs)
2. A Persona MUST have a jobToBeDone
3. A Persona MUST have usageFrequency and technicalComfort

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | Actor | actorRefs (DERIVED_FROM_ACTOR in Knowledge Graph) |
| referenced by | Product | product.personas |
| referenced by | Feature (Platform Space) | feature.primaryPersonaId |

---

## Existing Sources

- Elara Enhancement §3.2 ✅
- Neo4j Graph Extension (Enhancement §4.3) ✅
- Freeze schema: ⚠️ **NOT in Freeze schema — gap**
