# Artifact: Extension Rules Fill (`extensions/{F-ID}/rules.yaml`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/extensions/{F-ID}/rules.yaml`
> **Template:** [TPL-PROD-EXT-RULES](../../../templates/product/prod/extensions/rules.yaml)
> **Example:** [acme-sales/extensions/F-SD-001/rules.yaml](../../../examples/product/acme-sales/extensions/F-SD-001/rules.yaml)
> **Convention:** [dev.hub product-repo-layout.md §3](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md)

---

## Definition

Extension Rules fill feature-level `extension-rule` hooks declared by a
platform feature in Feature Spec §9. Rules layer on top of platform-provided
business rules — they cannot remove or weaken the platform's own enforcement
([sple-platform-concept.md §6](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/sple-platform-concept.md), open-closed principle).

Scope: FEATURE level (screen / form rules). Domain-level and service-level
rules live in custom services and attach via `extension-event` hooks, not here.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` | string | ✓ | `openleap.io/product-spec/v1` |
| `kind` | string | ✓ | `ExtensionRules` |
| `metadata.featureId` | string | ✓ | Directory name MUST equal this value |
| `metadata.product` | string | ✓ | Product id |
| `extensions[].extensionPointId` | string | ✓ | Must match a declared hook in the feature spec |
| `extensions[].rules[]` | Rule[] | ✓ | |
| `rules[].id` | string | ✓ | Stable rule id (e.g. `RX-NNN`) |
| `rules[].name` | string | ✓ | Short name |
| `rules[].type` | enum | ✓ | `validation` \| `authorization` \| `derivation` \| `calculation` \| `trigger` |
| `rules[].enforcement` | enum | ✓ | `hard` \| `soft` |
| `rules[].severity` | enum | | `error` \| `warning` \| `info` |
| `rules[].when` | expression | ✓ | Predicate against the view-model |
| `rules[].then` | object | ✓ | Consequence (block, requireRole, set, …) |
| `rules[].rationale` | string | ✓ | WHY this rule exists for this product |

---

## Constraints

1. Rules MUST NOT weaken platform-declared rules (only add or tighten).
2. `when` expressions MUST reference fields declared in the platform view-model schema (Feature Spec §5.2) or in the matching `fields.yaml`.
3. `type` values MUST be one of the five allowed.
4. `hard` rules block the UI action; `soft` rules allow override with audit log.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| fills | Platform Feature extension point | `extensionPointId` |
| may trigger | Custom Service | `then` consequence |
| referenced by | BFF productconfig | `extensionRoutes[]` |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Extension-point taxonomy | `sple-platform-concept.md §6` | ✅ feature-level row |
| Rule types | `artifacts/product/business-rule.md` | ✅ same taxonomy reused |
