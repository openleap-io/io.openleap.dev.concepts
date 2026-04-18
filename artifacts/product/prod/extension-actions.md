# Artifact: Extension Actions Fill (`extensions/{F-ID}/actions.yaml`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/extensions/{F-ID}/actions.yaml`
> **Template:** [TPL-PROD-EXT-ACTIONS](../../../templates/product/prod/extensions/actions.yaml)
> **Example:** [acme-sales/extensions/F-SD-001/actions.yaml](../../../examples/product/acme-sales/extensions/F-SD-001/actions.yaml)
> **Convention:** [dev.hub product-repo-layout.md §3](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md)

---

## Definition

Extension Actions fill feature-level `extension-action` hooks declared by a
platform feature in Feature Spec §9. Actions appear in the feature's Screen
Contract (AUI) at the designated extension zone as buttons, menu items, or
row actions, routed by the BFF to either a custom service or a platform
endpoint.

See [sple-platform-concept.md §6](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/sple-platform-concept.md) — feature-level extension points.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` | string | ✓ | `openleap.io/product-spec/v1` |
| `kind` | string | ✓ | `ExtensionActions` |
| `metadata.featureId` | string | ✓ | Directory name MUST equal this value |
| `extensions[].extensionPointId` | string | ✓ | Declared hook in the feature spec |
| `extensions[].zone` | string | ✓ | AUI extension zone id |
| `extensions[].actions[]` | Action[] | ✓ | |
| `actions[].id` | string | ✓ | Kebab-case action id |
| `actions[].label` | string | ✓ | User-visible label (localisable) |
| `actions[].kind` | enum | ✓ | `button` \| `menu-item` \| `row-action` |
| `actions[].confirm` | Confirm | | Confirmation dialog spec |
| `actions[].routing` | Routing | ✓ | target, service, endpoint, timeoutMs, failureMode |
| `actions[].input` | map | | Map action inputs to view-model paths or prompts |
| `actions[].persona.visibleFor[]` | string[] | | Persona ids that see the action |
| `actions[].persona.enabledFor[]` | string[] | | Persona ids that may invoke |
| `actions[].enabledWhen` | expression | | Precondition against the view-model |

---

## Constraints

1. `zone` MUST be an extension zone declared by the feature's Screen Contract (AUI).
2. `kind` MUST be one of the three allowed values.
3. Actions MUST NOT bypass platform rules: they call the platform or a custom service, never modify local view state.
4. Every persona id referenced MUST exist in `personas/{id}.yaml`.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| fills | Platform Feature extension point | `extensionPointId` |
| renders into | AUI extension zone | `zone` |
| invokes | Custom Service or platform endpoint | `routing.target` |
| referenced by | BFF productconfig | `extensionRoutes[]` |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Extension-point taxonomy | `sple-platform-concept.md §6` | ✅ feature-level row |
| AUI zone model | `templates/platform/feature/screen-contract-aui.yaml` | ✅ zone types defined |
