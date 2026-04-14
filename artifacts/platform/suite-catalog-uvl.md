# Artifact: Suite Catalog UVL

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Suite (variability root per suite)
> **Source:** SUITE_CATALOG_TEMPLATE.uvl

---

## Definition

A Suite Catalog UVL is the suite-level root of the UVL variability tree. It imports all composition node models and standalone leaf features belonging to this suite, assembling them under a single suite root feature. The suite root uses an `optional` group so that products may select any subset of the suite's features (subject to constraints). Cross-tree constraints at this level enforce intra-suite rules that span multiple composition nodes. The Suite Catalog UVL is imported by the Platform Catalog UVL to form the platform-wide variability tree.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| namespace | string | ✓ | Suite namespace (e.g., `pps`, `fi`, `sd`). Convention: lowercase suite ID. |
| imports | string[] | ✓ | All composition node UVL models belonging to this suite (by namespace/feature ID) |
| features | FeatureDeclaration | ✓ | Suite root feature with optional group containing all top-level composition nodes |
| constraints | Constraint[] | | Cross-tree constraints that span multiple compositions within this suite, plus cross-suite `requires` constraints |

### features (FeatureDeclaration)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| rootFeature | string | ✓ | Suite root feature name. Convention: `{SUITE}_UI` (e.g., `PPS_UI`, `FI_UI`). |
| group | GroupDeclaration | ✓ | An `optional` group containing all top-level composition nodes as members |

### constraints (per constraint)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| expression | string | ✓ | UVL constraint expression. May be intra-suite (e.g., `F-PPS-012 requires F-PPS-010`) or cross-suite (e.g., `F-SD-001 requires F-PPS-010`) |
| scope | enum | | intra-suite, cross-suite |
| rationale | string | | Business reason for this constraint |

---

## Constraints

1. The namespace MUST be the lowercase suite ID.
2. The root feature MUST follow the convention `{SUITE}_UI` (uppercase suite ID + `_UI`).
3. Every composition node and standalone leaf in the Suite Feature Catalog MUST be imported.
4. Cross-suite `requires` constraints from individual Feature Models are surfaced here for suite-level visibility.
5. The suite root group is `optional` — products choose which top-level compositions to include.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derived from | [Suite Feature Catalog](suite-feature-catalog.md) | Catalog's feature tree → UVL structure |
| imports | [Composition Model UVL](composition-model.md) | All composition nodes in this suite |
| imports | [Feature Model UVL](feature-model.md) | Standalone leaf features (not nested in a composition) |
| imported by | [Platform Catalog UVL](platform-catalog-uvl.md) | Platform root imports all suite catalogs |
| validated by | UVL Solver | Constraint satisfaction checking during product configuration |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Suite Catalog UVL Template | `concepts/ui-sple/SUITE_CATALOG_TEMPLATE.uvl` | ✅ Covers namespace, imports, features, constraints |
| UVL Standard | Sundermann et al. (2021), MODEVAR community | ✅ Language specification |
| Artifact Catalog §3.10 | `concepts/ARTIFACT_CATALOG.md` | ✅ Referenced alongside Suite Feature Catalog |

---

## Gaps

- ⚠️ Minor alignment: cross-suite constraint surfacing convention needs documentation
