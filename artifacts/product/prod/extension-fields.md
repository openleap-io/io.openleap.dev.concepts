# Artifact: Extension Fields Fill (`extensions/{F-ID}/fields.yaml`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/extensions/{F-ID}/fields.yaml`
> **Template:** [TPL-PROD-EXT-FIELDS](../../../templates/product/prod/extensions/fields.yaml)
> **Example:** [acme-sales/extensions/F-SD-001/fields.yaml](../../../examples/product/acme-sales/extensions/F-SD-001/fields.yaml)
> **Convention:** [dev.hub product-repo-layout.md §3](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md)

---

## Definition

Extension Fields fill the **feature-level** `extension-field` hooks declared by
a platform feature in its Feature Spec §9. A feature declares WHERE custom
fields may be added and under what rules; this file declares WHICH fields a
product actually adds and how the BFF should persist them.

See [sple-platform-concept.md §6](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/sple-platform-concept.md) — Feature-level extension points (Open-Closed Principle).

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` | string | ✓ | `openleap.io/product-spec/v1` |
| `kind` | string | ✓ | `ExtensionFields` |
| `metadata.featureId` | string | ✓ | Directory name MUST equal this value |
| `metadata.product` | string | ✓ | Product id |
| `extensions[]` | ExtensionGroup[] | ✓ | One per declared extension-field point |
| `extensions[].extensionPointId` | string | ✓ | Must match a declared hook in the feature spec |
| `extensions[].routing` | enum | ✓ | `custom-service` \| `inline` |
| `extensions[].target` | Target | ✓ if `custom-service` | service id, endpoint, timeout, failure mode |
| `extensions[].fields[]` | Field[] | ✓ | The custom fields |
| `extensions[].fields[].id` | string | ✓ | Stable field id |
| `extensions[].fields[].label` | string | ✓ | User-visible label (localisable) |
| `extensions[].fields[].type` | enum | ✓ | `string` \| `number` \| `enum` \| `date` \| `bool` |
| `extensions[].fields[].required` | bool | ✓ | Is this field mandatory? |
| `extensions[].fields[].validation` | map | | `pattern`, `min`, `max`, etc. |
| `extensions[].fields[].persona.visibleFor[]` | string[] | | Persona ids that see this field |
| `extensions[].fields[].persona.editableFor[]` | string[] | | Persona ids that may edit |

---

## Constraints

1. Only **feature-level** extension points (sple-platform-concept.md §6, row 1). Domain- and service-level hooks belong elsewhere.
2. Every `extensionPointId` MUST be declared by the feature spec §9; Decompose rejects unknown ids.
3. Every persona id referenced MUST exist in `personas/{id}.yaml`.
4. A field's `validation` section MUST satisfy the field's declared `type`.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| fills | Platform Feature extension point | `extensionPointId` |
| references | Custom Service | `target.service` |
| referenced by | BFF productconfig | `extensionRoutes[]` |
| referenced by | Screen Override (AUI) | zones display these fields |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Extension-point taxonomy | `sple-platform-concept.md §6` | ✅ feature-level row |
| Open-Closed Principle | Meyer (1988) | ✅ theoretical basis |
