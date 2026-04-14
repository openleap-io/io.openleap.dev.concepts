# Artifact: Composition Model (UVL)

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Platform-Feature (hierarchy + variability dimension)
> **Source:** COMPOSITION_NODE_TEMPLATE.uvl

---

## Definition

A Composition Model is the UVL (Universal Variability Language) representation of a Feature Composition node. It declares the feature tree structure for a composition: which children exist, how they are grouped (mandatory, optional, alternative, or), and what cross-tree constraints apply. The Composition Model imports child feature models (leaf or nested composition) and is itself imported by the Suite Catalog UVL. Together, these form the hierarchical variability tree that UVL solvers validate during product configuration.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| namespace | string | ✓ | Composition node feature ID (e.g., F-PPS-012) |
| imports | string[] | ✓ | Child feature model imports — references to leaf Feature Models or nested Composition Models |
| features | FeatureDeclaration | ✓ | Group declarations: the composition node as parent with children in typed groups |
| constraints | Constraint[] | | Cross-tree constraints within or across this composition's children |

### features (FeatureDeclaration)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| rootFeature | string | ✓ | This composition node's feature ID |
| groups | GroupDeclaration[] | ✓ | One or more groups of children |

### groups (per GroupDeclaration)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| type | enum | ✓ | mandatory, optional, alternative, or |
| members | string[] | ✓ | Child feature IDs belonging to this group |

### constraints (per constraint)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| expression | string | ✓ | UVL constraint expression (e.g., `F-PPS-012-01 requires F-PPS-012-03`) |
| rationale | string | | Business reason for this constraint |

---

## Constraints

1. The namespace MUST match the Feature Composition's ID.
2. Every child listed in the Feature Composition's children[] MUST appear in the imports and features groups.
3. Alternative groups enforce exactly-one selection (XOR semantics).
4. Or-groups enforce at-least-one selection.
5. Constraints here are intra-composition — cross-suite constraints belong in the leaf Feature Model.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derived from | [Feature Composition](feature-composition.md) | Children and group types → UVL tree |
| imports | [Feature Model UVL](feature-model.md) | Leaf child feature models |
| imports | Composition Model (nested) | Sub-composition node models |
| imported by | [Suite Catalog UVL](suite-catalog-uvl.md) | Suite root imports composition models |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Composition Node UVL Template | `concepts/ui-sple/COMPOSITION_NODE_TEMPLATE.uvl` | ✅ Covers imports, feature groups, constraints |
| UVL Standard | Sundermann et al. (2021), MODEVAR community | ✅ Language specification |
| Artifact Catalog §3.9 | `concepts/ARTIFACT_CATALOG.md` | ✅ Referenced alongside Feature Composition |

---

## Gaps

- ⚠️ Minor alignment with Conceptual Stack naming conventions
