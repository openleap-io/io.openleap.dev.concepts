# Artifact: `prod.*` Repository Artifacts

> **Space:** Product (Application Engineering)
> **Convention:** [dev.hub architecture/product-repo-layout.md](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md)
> **Repo shape:** `io.openleap.prod.{product}`
> **Template root:** [`templates/product/prod/`](../../../templates/product/prod/)
> **Example:** [`examples/product/acme-sales/`](../../../examples/product/acme-sales/)

---

This subdirectory defines the **Git-persisted, reviewable** artifacts of a
product-spec repository. These are the counterpart to the discovery artifacts
in [`artifacts/product/`](..) (Product, Persona, BusinessProcess, …) that live
inside Elara's working area: once Elara authoring is frozen, the following
artifacts are exported to a `prod.*` repo for review, versioning, and release.

---

## Artifact index

| Artifact | File | Template | Repo path |
|---|---|---|---|
| [Product Manifest](product-manifest.md) | `product.yaml` | TPL-PROD-MANIFEST | `/product.yaml` |
| [Feature Selection (UVL)](feature-selection.md) | `selection.uvl` | TPL-PROD-SEL | `/features/selection.uvl` |
| [Variability Bindings](variability-bindings.md) | `bindings.yaml` | TPL-PROD-BIND | `/variability/bindings.yaml` |
| [Extension Fields Fill](extension-fields.md) | `fields.yaml` | TPL-PROD-EXT-FIELDS | `/extensions/{F-ID}/fields.yaml` |
| [Extension Rules Fill](extension-rules.md) | `rules.yaml` | TPL-PROD-EXT-RULES | `/extensions/{F-ID}/rules.yaml` |
| [Extension Actions Fill](extension-actions.md) | `actions.yaml` | TPL-PROD-EXT-ACTIONS | `/extensions/{F-ID}/actions.yaml` |
| [Product Persona (exported)](product-persona.md) | `{persona}.yaml` | TPL-PROD-PERSONA | `/personas/{id}.yaml` |
| [Customer BPMN Process](customer-process.md) | `{process}.bpmn` | TPL-PROD-PROCESS | `/processes/{id}.bpmn` |
| [Product AUI Override](screen-override.md) | `{feature}.aui.yaml` | TPL-PROD-AUI-OVR | `/screen-overrides/{feature-id}.aui.yaml` |
| [BFF productconfig](bff-productconfig.md) | `productconfig.yaml` | TPL-PROD-BFF-PCFG | `/bff/productconfig.yaml` |

---

## Relationship to the in-Elara Product artifacts

| In-Elara artifact (`artifacts/product/`) | `prod.*` repo artifact (this dir) |
|---|---|
| Product | Product Manifest (`product.yaml`) |
| Persona | Product Persona (`personas/*.yaml`) |
| Business Process | Customer BPMN Process (`processes/*.bpmn`) |
| Feature Resolution (Platform → Product) | Feature Selection (`features/selection.uvl`) |
| — (new) | Variability Bindings (`variability/bindings.yaml`) |
| — (new) | Extension fills (`extensions/{F-ID}/*.yaml`) |
| — (new) | Screen Override (`screen-overrides/*.aui.yaml`) |
| — (new) | BFF productconfig (`bff/productconfig.yaml`) |

Elara authors the in-Elara artifacts during discovery. At export time, Elara
produces the `prod.*` repo artifacts and opens a pull request against
`io.openleap.prod.{product}`.
