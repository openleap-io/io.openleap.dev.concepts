# Artifact: Product Manifest (`product.yaml`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/product.yaml`
> **Template:** [TPL-PROD-MANIFEST](../../../templates/product/prod/product.yaml)
> **Example:** [acme-sales/product.yaml](../../../examples/product/acme-sales/product.yaml)
> **Convention:** [dev.hub product-repo-layout.md §3](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md)

---

## Definition

The Product Manifest is the entry point of a `prod.*` repository. It identifies
the product, names its owner and target customer, pins the platform catalog
version to validate against, and lists the satellite artifacts that make up
the product specification.

The manifest does NOT contain feature selections, variability resolutions, or
extension fills — those live in dedicated files referenced from here.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` | string | ✓ | `openleap.io/product-spec/v1` |
| `kind` | string | ✓ | `ProductManifest` |
| `metadata.id` | string | ✓ | Kebab-case product id; matches `{product}` in the repo name |
| `metadata.name` | string | ✓ | Human-readable product name |
| `metadata.description` | string | ✓ | One or two sentences; who it's for, what it does |
| `metadata.version` | semver | ✓ | Product spec version (bumped per release) |
| `metadata.status` | enum | ✓ | `draft` \| `review` \| `approved` \| `released` \| `deprecated` |
| `metadata.owner` | Owner | ✓ | Team, email, slack channel |
| `metadata.targetCustomer` | TargetCustomer | ✓ | Name, industry, deployment model |
| `references.*` | map | ✓ | Relative paths to satellite artifacts |
| `personas[]` | PersonaRef[] | ✓ | id + file path under `personas/` |
| `processes[]` | ProcessRef[] | ✓ | id, name, level, file path under `processes/` |
| `platformCatalog.repo` | string | ✓ | Always `io.openleap.dev.spec` |
| `platformCatalog.ref` | string | ✓ | Tag or SHA of dev.spec to validate against |
| `release` | Release | | Packaging hints for CI (artifact name, registry, channel) |

---

## Constraints

1. `metadata.id` MUST match the last segment of the repo name.
2. Every `references.*` path MUST resolve to an existing file in the repo.
3. `personas[].file` paths MUST exist under `personas/`.
4. `processes[].file` paths MUST exist under `processes/` and end in `.bpmn`.
5. `platformCatalog.ref` MUST be resolvable at packaging time (CI fails otherwise).

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| contains (by reference) | Feature Selection | `references.featureSelection` |
| contains (by reference) | Variability Bindings | `references.variabilityBindings` |
| contains (by reference) | Extension fills | `references.extensionsRoot` |
| contains (by reference) | Product Personas | `personas[]` |
| contains (by reference) | Customer BPMN Processes | `processes[]` |
| pins | dev.spec catalog version | `platformCatalog.ref` |
| input to | BFF productconfig | packaging step |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| dev.hub convention | `architecture/product-repo-layout.md §3` | ✅ authoritative |
| In-Elara counterpart | [`artifacts/product/product.md`](../product.md) | ✅ unchanged |
