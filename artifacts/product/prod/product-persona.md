# Artifact: Product Persona (exported, `personas/{id}.yaml`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/personas/{id}.yaml`
> **Template:** [TPL-PROD-PERSONA](../../../templates/product/prod/personas/persona.yaml)
> **Example:** [acme-sales/personas/sales-rep.yaml](../../../examples/product/acme-sales/personas/sales-rep.yaml)
> **In-Elara counterpart:** [`artifacts/product/persona.md`](../persona.md)

---

## Definition

A Product Persona is the Git-persisted export of the Elara Persona artifact —
an Actor enriched with UX attributes (IAM roles, usage frequency, technical
comfort, pain points, workarounds, device profile). Platform feature specs
reference role TYPES generically; products map those types to specific
personas via this file.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` | string | ✓ | `openleap.io/product-spec/v1` |
| `kind` | string | ✓ | `Persona` |
| `metadata.id` | string | ✓ | Kebab-case; matches the file name |
| `metadata.name` | string | ✓ | Human-readable persona name |
| `actorRefs[]` | ref[] | ✓ | Actor ids this persona derives from |
| `iamRoles[]` | string[] | ✓ | IAM role codes from dev.spec IAM suite |
| `jobToBeDone` | string | ✓ | Primary task, one sentence |
| `usageFrequency` | enum | ✓ | `DAILY` \| `WEEKLY` \| `OCCASIONAL` \| `RARE` |
| `technicalComfort` | enum | ✓ | `LOW` \| `MEDIUM` \| `HIGH` |
| `painPoints[]` | string[] | | Frustrations |
| `workarounds[]` | string[] | | Current coping strategies |
| `scenarios[]` | Scenario[] | | Process participation (processId, role, frequency) |
| `device` | Device | | Primary/secondary devices, screen size, input methods |
| `accessibility` | Accessibility | | Color vision, font scale, keyboardOnly |

---

## Constraints

1. `actorRefs[]` MUST have at least one entry (personas always derive from actors).
2. `usageFrequency` and `technicalComfort` MUST be present.
3. `iamRoles[]` entries MUST exist in the dev.spec IAM suite catalog.
4. `scenarios[].processId` MUST reference a process declared in `product.yaml#processes[]`.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derives from | Actor (discovered in Elara) | `actorRefs[]` |
| referenced by | Product Manifest | `personas[].file` |
| referenced by | Extension fills | `persona.visibleFor/editableFor` |
| referenced by | Screen Overrides | `zones[].personaGates` |
| referenced by | BFF productconfig | `personas[]` with iamRoles |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| In-Elara Persona artifact | `artifacts/product/persona.md` | ✅ unchanged (authored in Elara) |
| Elara Enhancement §3.2 | upstream Elara spec | ✅ authoritative |
