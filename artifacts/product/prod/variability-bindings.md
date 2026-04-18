# Artifact: Variability Bindings (`variability/bindings.yaml`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/variability/bindings.yaml`
> **Template:** [TPL-PROD-BIND](../../../templates/product/prod/variability/bindings.yaml)
> **Example:** [acme-sales/variability/bindings.yaml](../../../examples/product/acme-sales/variability/bindings.yaml)
> **Convention:** [dev.hub product-repo-layout.md §3](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md)

---

## Definition

Variability Bindings resolve every UVL attribute declared by the platform
features selected in `features/selection.uvl`. The platform feature spec §9
declares an attribute's name, type, default, and binding time; this file
decides the concrete value for this product.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` | string | ✓ | `openleap.io/product-spec/v1` |
| `kind` | string | ✓ | `VariabilityBindings` |
| `metadata.product` | string | ✓ | Product id (matches `product.yaml`) |
| `metadata.catalogRef` | string | ✓ | MUST equal `product.yaml#platformCatalog.ref` |
| `bindings[]` | FeatureBinding[] | ✓ | One entry per feature with attributes |
| `bindings[].featureId` | string | ✓ | Must appear in `features/selection.uvl` |
| `bindings[].attributes[]` | AttributeBinding[] | ✓ | At least one per entry |
| `bindings[].attributes[].path` | string | ✓ | Dotted attribute path from feature spec §9 |
| `bindings[].attributes[].value` | any | ✓ | Concrete value matching declared type |
| `bindings[].attributes[].bindingTime` | enum | ✓ | `compile` \| `deploy` \| `runtime` |
| `bindings[].attributes[].rationale` | string | ✓ | WHY this value for this product |
| `globals[]` | AttributeBinding[] | | Cross-cutting defaults (locale, currency, timezone) |

---

## Constraints

1. Binding-time values MUST be `compile`, `deploy`, or `runtime` (see [sple-platform-concept.md §2.3](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/sple-platform-concept.md) / conceptual-stack.md §4.2).
2. Every attribute declared with `bindingTime=compile` or `deploy` in the platform spec MUST be resolved here (or inherit a default); `runtime` attributes MAY be left unresolved.
3. `featureId` MUST match a selection in `features/selection.uvl`.
4. A value's type MUST match the attribute's type declared by the platform feature.
5. Feature-level bindings override `globals`.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| binds | Platform Feature attributes | `bindings[].featureId` + `attributes[].path` |
| input to | BFF productconfig | packaging step |
| validated against | dev.spec catalog at `catalogRef` | Decompose |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| UVL attribute model | Sundermann et al. (2021) | ✅ basis of the format |
| Binding-time semantics | `sple-platform-concept.md` / `conceptual-stack.md §4.2` | ✅ authoritative |
