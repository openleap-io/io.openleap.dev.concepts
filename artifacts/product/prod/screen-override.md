# Artifact: Product AUI Override (`screen-overrides/{feature-id}.aui.yaml`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/screen-overrides/{feature-id}.aui.yaml`
> **Template:** [TPL-PROD-AUI-OVR](../../../templates/product/prod/screen-overrides/feature.aui.yaml)
> **Example:** [acme-sales/screen-overrides/F-SD-001-02.aui.yaml](../../../examples/product/acme-sales/screen-overrides/F-SD-001-02.aui.yaml)
> **Companion of:** the platform Screen Contract (AUI) in dev.spec

---

## Definition

A Product AUI Override customises a platform Screen Contract (AUI) for one
product along three narrow axes: pick a CUI target per persona, reorder or
persona-gate variable zones, and rewrite copy. Anything broader (adding
actions, adding fields, changing rules) belongs in `extensions/*.yaml`.

The platform AUI is platform-free and suite-agnostic. The override is the
product's view into how it should be materialised for a specific customer's
persona and device mix.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` | string | ✓ | `openleap.io/product-spec/v1` |
| `kind` | string | ✓ | `ScreenOverride` |
| `metadata.featureId` | string | ✓ | File stem MUST equal this value |
| `metadata.product` | string | ✓ | Product id |
| `cuiTargets.default` | enum | ✓ | `web-desktop` \| `web-tablet` \| `handheld` \| `voice` |
| `cuiTargets.perPersona[]` | PersonaCUI[] | | Persona-specific CUI selection |
| `zones[]` | ZoneOverride[] | | |
| `zones[].id` | string | ✓ | Must be a zone declared by the platform AUI |
| `zones[].order` | int | | Relative rendering order (variable zones only) |
| `zones[].personaGates.visibleFor[]` | string[] | | Persona ids |
| `zones[].personaGates.editableFor[]` | string[] | | Persona ids |
| `zones[].hidden` | bool | | Allowed only for `removableByProduct: true` zones |
| `actionLabels[]` | ActionLabel[] | | Change label/icon of platform actions |
| `copy[]` | Copy[] | | Override i18n keys |
| `absentRules[]` | AbsentRule[] | | For hidden zones: pick `collapse-up` \| `end-page` \| `tab-hidden` \| `panel-section-hidden` |

---

## Constraints

1. Every `zones[].id`, `actionLabels[].id`, and `copy[].key` MUST be declared by the platform Screen Contract (AUI). Decompose rejects unknown ids.
2. `zones[].hidden: true` is permitted only on zones the platform AUI marks `removableByProduct: true`.
3. FIXED zones MUST NOT be altered (no `hidden`, no `order`).
4. The task model of the platform AUI MUST NOT be modified here.
5. `absentRules[].rule` values MUST come from the four platform absent-rules.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| overrides | Platform Screen Contract (AUI) | `metadata.featureId` |
| references | Product Personas | `personaGates` |
| input to | BFF productconfig | `features[].viewModelKey` resolution |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Platform AUI template | `templates/platform/feature/screen-contract-aui.yaml` | ✅ source of declared ids |
| Cameleon Reference Framework | Calvary et al. (2003) | ✅ AUI/CUI split |
