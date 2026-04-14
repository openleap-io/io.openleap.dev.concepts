# Artifact: Feature Model (UVL)

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Platform-Feature (variability dimension)
> **Source:** LEAF_FEATURE_TEMPLATE.uvl, UVL standard (Sundermann et al., 2021)

---

## Definition

A Feature Model is a machine-readable variability declaration in the Universal Variability Language (UVL). It is the companion artifact to the Platform-Feature Spec that formalizes how a feature varies across products. The Feature Model declares the feature's position in the feature tree, its group type, typed attributes with binding times, and cross-tree constraints (requires/excludes). UVL enables automated validation of product configurations — incomplete or contradictory feature selections are rejected by the UVL solver.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| namespace | string | ✓ | Feature ID (e.g., F-PPS-012). Identifies this model in the UVL namespace hierarchy. |
| imports | string[] | | Suite catalog imports and sibling feature references needed for cross-tree constraints |
| features | FeatureTree | ✓ | Position in the tree (parent node, siblings), group type declaration |
| attributes | Attribute[] | | Typed attributes with defaults and binding times |
| constraints | Constraint[] | ✓ | Cross-tree constraints: `requires` and `excludes` declarations |

### features (FeatureTree)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| featureId | string | ✓ | This feature's ID in the tree |
| parentGroup | string | | Parent composition node ID |
| groupType | enum | ✓ | mandatory, optional, alternative (XOR — exactly one), or (at least one) |
| children | string[] | | Child features (for composition nodes only) |

### attributes (per attribute)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| name | string | ✓ | Attribute name (e.g., pagination.pageSize, currency.default) |
| type | enum | ✓ | String, Integer, Real, Boolean |
| default | any | | Default value |
| bindingTime | enum | ✓ | compile (build-time, baked into artifact), deploy (deployment configuration), runtime (dynamic per request/session) |

### constraints (per constraint)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| type | enum | ✓ | requires, excludes |
| source | string | ✓ | This feature ID |
| target | string | ✓ | Target feature ID (e.g., F-PPS-010, F-BP-001) |
| rationale | string | | Why this constraint exists (e.g., "needs stock data for availability check") |

---

## Constraints

1. Every cross-suite backend dependency in Feature Spec §5 MUST have a corresponding `requires` constraint in this model (Conceptual Stack §6.2). This is the critical traceability rule.
2. The namespace MUST match the feature's ID.
3. Attributes with bindingTime `compile` are resolved at build time and cannot change per product deployment.
4. Attributes with bindingTime `deploy` are resolved at deployment and set in ProductConfig.
5. Attributes with bindingTime `runtime` are resolved per request/session (e.g., user preferences, tenant settings).
6. `requires` constraints are transitive for product validation: if A requires B and B requires C, a product including A MUST also include B and C.
7. `excludes` constraints are symmetric: if A excludes B, then B also excludes A.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derived from | [Platform-Feature Spec](platform-feature-spec.md) | §8 dependencies and variability points |
| imported by | [Composition Model UVL](composition-model.md) | Composition node imports leaf feature models |
| imported by | [Suite Catalog UVL](suite-catalog-uvl.md) | Suite root imports all feature models |
| validated against | [Platform Catalog UVL](platform-catalog-uvl.md) | Platform-wide constraint checking |
| resolved by | Product-Feature | Product resolves attributes and selects optional children |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Leaf Feature UVL Template | `templates/LEAF_FEATURE_TEMPLATE.uvl` | ✅ Covers tree position, group types, attributes |
| UVL Standard | Sundermann et al. (2021), MODEVAR community | ✅ Language specification |
| Conceptual Stack §6.2 | `conceptual-stack.md` | ✅ Cross-suite `requires` constraint rule |
| Artifact Catalog §3.6 | `artifact-catalog.md` | ✅ Field summary |

---

## Gaps

- ⚠️ Template needs explicit `requires` derivation rules: how to trace Feature Spec §5 cross-suite deps to UVL constraints
- ⚠️ Binding time semantics need runtime examples (e.g., tenant-specific attribute resolution)
