# Artifact: Feature Composition

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Platform-Feature (hierarchy dimension)
> **Source:** COMPOSITION_NODE_SPEC_TEMPLATE.md

---

## Definition

A Feature Composition is a composition node in the feature hierarchy that groups child features into capability structures. It follows the FODA hierarchy: Capability → Use-Case → Screen. Composition nodes define the variability structure of their children — whether children are mandatory, optional, alternative (exactly one), or or-groups (at least one). The composition provides the business rationale for the grouping and documents valid/invalid configuration examples.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: `F-{SUITE}-{NNN}` (composition node — no leaf suffix) |
| name | string | ✓ | Capability or use-case name (e.g., "Inventory Management", "Goods Receipt Processing") |
| children | ChildEntry[] | ✓ | Child features or sub-composition nodes |
| variabilityStructure | string (prose) | ✓ | Business rationale for the grouping and variability choices |
| constraints | Constraint[] | | Intra-node constraints (e.g., "if child A is selected, child B must also be selected") |
| validConfigurations | ConfigExample[] | | Example valid feature selections with rationale |
| invalidConfigurations | ConfigExample[] | | Example invalid selections with explanation of why they fail |

### children (per child)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| childId | string | ✓ | Feature ID of the child (F-{SUITE}-{NNN} or F-{SUITE}-{NNN}-{NN}) |
| groupType | enum | ✓ | mandatory (always included), optional (may be absent), alternative (XOR — exactly one of the group), or (at least one of the group) |

### constraints (per constraint)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| type | enum | ✓ | requires, excludes |
| source | string | ✓ | Child feature ID |
| target | string | ✓ | Target feature ID |
| rationale | string | | Why this constraint exists |

### validConfigurations / invalidConfigurations (per example)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| name | string | ✓ | Configuration name (e.g., "Minimal Inventory", "Full Warehouse") |
| selectedChildren | string[] | ✓ | Which children are selected |
| rationale | string | ✓ | Why this is valid / why this is invalid |

---

## Constraints

1. A composition node uses the `F-{SUITE}-{NNN}` pattern (no leaf suffix `-{NN}`).
2. Children with groupType `alternative` MUST belong to the same alternative group — exactly one of the group is selected.
3. Children with groupType `or` MUST belong to the same or-group — at least one must be selected.
4. Children with groupType `mandatory` are always included when the composition node is selected.
5. The variabilityStructure MUST explain the business rationale — why these features are grouped and why the variability types were chosen.
6. Every composition node MUST have at least one child.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| contains | [Platform-Feature Spec](platform-feature-spec.md) | children[].childId → leaf features |
| contains | Feature Composition (nested) | children[].childId → sub-composition nodes |
| produces | [Composition Model UVL](composition-model.md) | Children and group types → UVL tree structure |
| listed in | [Suite Feature Catalog](suite-feature-catalog.md) | Catalog registers all compositions |
| belongs to | [Suite Spec](suite-spec.md) | Via suite prefix in feature ID |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Composition Node Spec Template | `templates/COMPOSITION_NODE_SPEC_TEMPLATE.md` | ✅ Covers structure, children, variability |
| Composition Node UVL Template | `templates/COMPOSITION_NODE_TEMPLATE.uvl` | ✅ UVL companion |
| Conceptual Stack §3.5 | `conceptual-stack.md` | ✅ Defines FODA hierarchy: Capability → Use-Case → Screen |
| Artifact Catalog §3.9 | `artifact-catalog.md` | ✅ Field summary |

---

## Gaps

- ⚠️ Minor alignment needed with Conceptual Stack terminology
