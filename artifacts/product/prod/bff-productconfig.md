# Artifact: BFF productconfig (`bff/productconfig.yaml`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/bff/productconfig.yaml`
> **Template:** [TPL-PROD-BFF-PCFG](../../../templates/product/prod/bff/productconfig.yaml)
> **Example:** [acme-sales/bff/productconfig.yaml](../../../examples/product/acme-sales/bff/productconfig.yaml)
> **Convention:** [dev.hub product-repo-layout.md §3.2](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md)

---

## Definition

The BFF productconfig is the **release artifact** of a `prod.*` repository.
Everything else in the repo exists to produce this file reproducibly. The BFF
reads it at startup (per-product deployment) or per request (SaaS shared
deployment) to:

1. enforce feature-gating per inclusion mode;
2. apply resolved variability attribute values;
3. route extension-point calls to the correct services;
4. know which platform service endpoints to aggregate per feature (BFF
   aggregation hints from Feature Spec §5 / §5.2).

See [sple-platform-concept.md §4.2](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/sple-platform-concept.md) and `conceptual-stack.md §4.6`.

---

## Fields (summary — see template for the full shape)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` / `kind` | string | ✓ | `openleap.io/product-spec/v1` / `BffProductConfig` |
| `metadata.product` | string | ✓ | Product id |
| `metadata.productVersion` | semver | ✓ | From `product.yaml` |
| `metadata.catalogRef` | string | ✓ | dev.spec ref used for validation |
| `metadata.deploymentModel` | enum | ✓ | `per-product` \| `shared` \| `feature-module` |
| `features[]` | ActiveFeature[] | ✓ | Each with `mode`, `aggregatedServices`, `viewModelKey`, `bindings` |
| `features[].mode` | enum | ✓ | `full` \| `read-only` \| `embedded` \| `excluded` |
| `excluded[]` | Excluded[] | | Audit of explicitly-excluded features |
| `extensionRoutes[]` | ExtensionRoute[] | | One per filled extension point |
| `personas[]` | PersonaMap[] | ✓ | Persona → IAM roles mapping |
| `globals` | Globals | | locale, currency, timezone |
| `validation.decomposeVersion` | semver | ✓ | Tool version that produced the file |
| `validation.inputDigest` | string | ✓ | SHA-256 digest of all input files |
| `validation.status` | enum | ✓ | `valid` \| `invalid` \| `partial` |

---

## Constraints

1. Inclusion mode values MUST come from [integrated-overview.md §8.2](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/integrated-overview.md): `full`, `read-only`, `embedded`, `excluded`.
2. `features[].bindings` MUST be consistent with `variability/bindings.yaml` at packaging time.
3. `extensionRoutes[]` MUST cover every filled extension point found in `extensions/*`.
4. `personas[].iamRoles` MUST match `personas/{id}.yaml#iamRoles`.
5. The BFF SHOULD refuse to start if `validation.inputDigest` does not match a freshly-computed digest of its inputs.
6. Cross-suite entries MUST respect read-across, mutate-local: other-suite T3 features MUST appear in `read-only` mode unless the feature spec permits `full` (sple-platform-concept.md §5).

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derived from | Product Manifest | `metadata.product`, `catalogRef` |
| derived from | Feature Selection | `features[]`, `excluded[]` |
| derived from | Variability Bindings | `features[].bindings`, `globals` |
| derived from | Extension fills | `extensionRoutes[]` |
| derived from | Product Personas | `personas[]` |
| aggregates | Feature Spec §5 / §5.2 hints | `features[].aggregatedServices`, `viewModelKey` |
| consumed by | `prod.{product}.bff` | runtime |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| dev.hub §3.2 | `product-repo-layout.md` | ✅ authoritative |
| BFF runtime model | `conceptual-stack.md §4.6` | ✅ authoritative |
| Aggregation hints | Feature Spec §5 / §5.2 | ✅ source of BFF input |
