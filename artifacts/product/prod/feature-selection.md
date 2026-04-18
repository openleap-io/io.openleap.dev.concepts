# Artifact: Feature Selection (`features/selection.uvl`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/features/selection.uvl`
> **Template:** [TPL-PROD-SEL](../../../templates/product/prod/features/selection.uvl)
> **Example:** [acme-sales/features/selection.uvl](../../../examples/product/acme-sales/features/selection.uvl)
> **Convention:** [dev.hub product-repo-layout.md §3.1](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md)

---

## Definition

The Feature Selection is the UVL file that declares WHICH platform features
this product ships and in WHAT inclusion mode. It references platform features
by their stable `F-{SUITE}-{NNN}` / `F-{SUITE}-{NNN}-{NN}` id; it MUST NOT
copy any platform spec content.

This artifact replaces the conceptual "Feature Selection" step that
previously lived only in Elara. It is the machine-readable source of truth
for a product's feature set.

---

## Fields (UVL structure)

| Element | Required | Description |
|---------|----------|-------------|
| `namespace {product-id}` | ✓ | MUST match `metadata.id` in `product.yaml` |
| `imports catalog from "io.openleap.dev.spec/catalog.uvl"` | ✓ | Only import |
| `features.{ProductRoot}` | ✓ | Single root feature per product |
| `mandatory` group | ✓ | Features shipped in this product |
| `optional` group |   | Optional features considered (explicit include or commented negative decision) |
| `inclusion "{mode}"` attribute | ✓ per selection | One of `full` \| `read-only` \| `embedded` \| `excluded` |
| `constraints` block |   | Product-specific `requires` / `excludes` not already in dev.spec |

---

## Constraints

1. Inclusion mode values MUST come from [integrated-overview.md §8.2](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/integrated-overview.md):
   `full`, `read-only`, `embedded`, `excluded`.
2. **Cross-suite rule (read-across, mutate-local, [sple-platform-concept.md §5](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/sple-platform-concept.md)):**
   - T2 features from OTHER suites: freely included (`full` allowed).
   - T3 features from OTHER suites: `read-only` only, unless the feature spec explicitly permits `full`.
   - Own-suite features: any mode allowed.
3. Referenced feature ids MUST exist in the imported `catalog.uvl`. Decompose rejects unknown ids.
4. All UVL `requires` constraints declared in dev.spec MUST be satisfied by the selection.
5. The root feature id MUST match `metadata.id` in `product.yaml`.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| imports | dev.spec catalog | `imports catalog from ...` |
| references | Platform Features | id references (never copies) |
| referenced by | Variability Bindings | `bindings[].featureId` must match |
| referenced by | Extension fills | `extensions/{F-ID}/` path matches |
| input to | BFF productconfig | packaging step |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| dev.hub convention | `product-repo-layout.md §3.1 "Reference, Do Not Copy"` | ✅ authoritative |
| Feature ID convention | `sple-platform-concept.md §2.3` | ✅ `F-{SUITE}-{NNN}` / `F-{SUITE}-{NNN}-{NN}` |
| Feature inclusion modes | `integrated-overview.md §8.2` | ✅ `full` / `read-only` / `embedded` / `excluded` |
