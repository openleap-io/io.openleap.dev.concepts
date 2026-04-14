# Artifact: Functional Area

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.5, §9.1 MongoDB Document Model, Freeze schema `functional_areas`

---

## Definition

A Functional Area is a **logical grouping** of related processes, actors, and terms — how the business organizes its work. Aligned with TOGAF business architecture concepts. Functional Areas define cohesive boundaries within the business domain.

**KEY ROLE:** Functional Areas **map to Suites during decomposition** — they are the primary input for determining which Suite Spec owns which part of the domain.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Functional area identifier |
| name | string | ✓ | Area name (e.g., "Enrollment") |
| description | string | ✓ | What this area covers |
| includes | string[] | | Process/object names inside this area |
| excludes | string[] | | Names explicitly outside this area |
| rationale | string | | Why these boundaries were chosen (e.g., "cohesive around student onboarding") |
| subAreas | SubArea[] | | Nested sub-areas within this area |
| subAreas[].name | string | ✓ | Sub-area name |
| subAreas[].type | enum | ✓ | core, supporting, generic |
| dependsOn | ref[] | | Other FunctionalArea IDs this area depends on |

---

## Constraints

1. A FunctionalArea SHOULD have a rationale explaining the boundary choice
2. dependsOn MUST NOT contain circular references

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | FunctionalArea | dependsOn |
| referenced by | BusinessProcess | process.functionalAreaRef |
| referenced by | BusinessObject | object.functionalAreaRef |
| referenced by | BusinessEvent | event.functionalAreaRef |
| referenced by | BusinessCapability | capability.functionalAreaRef |
| referenced by | GlossaryTerm | term.functionalAreaRef |
| referenced by | QualityRequirement | requirement.scope (when ref to area) |
| referenced by | Decision | decision.affectedAreas |
| maps to | Suite Spec | Suite boundary derivation during decomposition |

---

## Existing Sources

- Elara Spec §4.2 Cat.5 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema `functional_areas` ✅
- Status: **Fully defined, no gaps**
