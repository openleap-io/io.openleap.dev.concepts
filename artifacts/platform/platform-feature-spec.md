# Artifact: Platform-Feature Spec

> **Space:** Platform (Domain Engineering) — bridges to Product Space
> **Conceptual Stack Layer:** Platform-Feature
> **Source:** Conceptual Stack §3.5, LEAF_FEATURE_SPEC_TEMPLATE.md

---

## Definition

A Platform-Feature Spec is the central artifact bridging the Platform and Product spaces. It is a complete specification of a reusable, variability-bearing capability in the platform catalog. A platform-feature defines what the user does, what they see, how the feature varies across products, what the backend provides, and where products can extend it.

Features are hierarchical following FODA principles (Kang et al., 1990): Capability (composition node) → Use-Case (composition node or leaf) → Screen (leaf). A Platform-Feature Spec is not one document but the anchor of **four integrated artifacts** managed together: Feature Spec (.md), Feature Model (.uvl), Screen Contract AUI (.yaml), and BFF Contract (.md).

---

## Fields

### Identity

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: `F-{SUITE}-{NNN}` (composition node) or `F-{SUITE}-{NNN}-{NN}` (leaf). Suite prefix indicates ownership. |
| suite | string | ✓ | Owning suite ID — the suite whose UBL this feature primarily uses |
| name | string | ✓ | Feature name (e.g., "Post Goods Receipt") |
| status | enum | ✓ | planned, draft, review, approved, live, deprecated |

### Specification Sections

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| §0 orientation | Orientation | ✓ | Summary, non-goals, entry/exit points. Establishes context for the reader. |
| §1 userGoal | UserGoal | ✓ | Goal statement + 2-5 scenarios. What the user wants to accomplish. |
| §2 userJourney | Journey | ✓ | Mermaid sequence diagram (happy path), screen layouts. **Feeds AUI task model** (Screen Contract AUI). |
| §3 interactionRequirements | Interaction | ✓ | Form fields, cross-field validation rules, actions, field groupings. **Feeds AUI zones** (Screen Contract AUI). |
| §4 edgeCases | EdgeCases | ✓ | Screen states: loading, empty, error, populated. **Feeds AUI absent-rules** (Screen Contract AUI). |
| §5 backendDependencies | BackendDep[] | ✓ | Service calls: serviceId, endpoint, method, tier, isMutation flag, failureMode. **Feeds BFF Contract** service call list. |
| §5.2 viewModelShape | JSON-C | ✓ | JSON with comments showing the BFF response structure. **IS the BFF output contract**. |
| §6 i18nAndPermissions | I18nPerm | | Translation keys (i18n key namespace), role-based visibility rules |
| §7 acceptanceCriteria | AC[] | ✓ | Given/When/Then testable statements |
| §8 dependencies | Dependencies | ✓ | Required features (UVL `requires` constraints), variability points (attributes with binding times), extension points (UI zones, rule slots, action slots, event hooks) |

### Section Derivation Flow

| Feature Spec Section | Produces / Feeds | Target Artifact |
|---------------------|-----------------|-----------------|
| §2 User Journey | Task Model (operators: sequential, concurrent, alternative, optional, enabling) | [Screen Contract AUI](screen-contract-aui.md) |
| §3 Interaction Requirements | Zones (fixed, variable, feature-gated) | [Screen Contract AUI](screen-contract-aui.md) |
| §4 Edge Cases | Absent-rules (collapse-up, end-page, tab-hidden, panel-section-hidden) | [Screen Contract AUI](screen-contract-aui.md) |
| §5 Backend Dependencies | Aggregated service calls with tier and mutation flag | [BFF Contract](bff-contract.md) |
| §5.2 View-Model Shape | BFF response JSON-C | [BFF Contract](bff-contract.md) |
| §8 Dependencies (variability) | Feature tree position, group type, attributes, constraints | [Feature Model UVL](feature-model.md) |

---

## Companion Artifacts

A Platform-Feature Spec is the anchor of four artifacts maintained in sync:

| Artifact | Format | Derivation |
|----------|--------|------------|
| Feature Spec (.md) | Markdown | Primary — this artifact |
| [Feature Model (.uvl)](feature-model.md) | UVL | From §8 dependencies + variability |
| [Screen Contract AUI](screen-contract-aui.md) | YAML | From §2 journey + §3 interaction + §4 edge cases |
| [BFF Contract](bff-contract.md) | Markdown | From §5 backend deps + §5.2 view-model shape |

---

## Constraints

1. A feature MUST belong to exactly one suite — the suite whose UBL it primarily uses (Conceptual Stack §5.2).
2. Cross-suite reads create `requires` constraints in the Feature Model UVL (Conceptual Stack §6.2).
3. Cross-suite writes are NOT allowed — mutations only within own suite. Cross-suite mutations via domain events (eventual consistency).
4. Every cross-suite backend dependency in §5 MUST have a corresponding `requires` constraint in the Feature Model.
5. The view-model shape in §5.2 IS the BFF output contract — frontend depends on this structure.
6. Features exist at T2 and T3 tiers only. T1 (infrastructure) and T4 (analytics) do not own features.
7. Extension points are declared in the platform, not invented by products (Open-Closed Principle).

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| belongs to | [Suite Spec](suite-spec.md) | suite field → Suite.metadata.id |
| produces | [Feature Model UVL](feature-model.md) | §8 variability → UVL declaration |
| produces | [Screen Contract AUI](screen-contract-aui.md) | §2-§4 → task model, zones, absent-rules |
| produces | [BFF Contract](bff-contract.md) | §5 + §5.2 → service calls, view-model shape |
| grouped by | [Feature Composition](feature-composition.md) | Leaf features are children of composition nodes |
| listed in | [Suite Feature Catalog](suite-feature-catalog.md) | Catalog registers all features |
| references | [Domain/Service Spec](domain-spec.md) | §5 backend dependencies reference service endpoints |
| selected by | [Product](../product/product.md) | Via Feature Resolution → featureSelection |
| configured as | Product-Feature | Product resolves variability, fills extension points |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Conceptual Stack §3.5 | `concepts/CONCEPTUAL_STACK.md` | ✅ Defines feature hierarchy, four parts, derivation rules |
| Leaf Feature Spec Template | `concepts/ui-sple/LEAF_FEATURE_SPEC_TEMPLATE.md` | ✅ Good (90%) — covers §0-§8 |
| Artifact Catalog §3.5 | `concepts/ARTIFACT_CATALOG.md` | ✅ Field summary |
| Agora Spec §5.3.3 | `io.openleap.spec/concepts/agora/` | ✅ Feature concept origin |

---

## Gaps

- ⚠️ Template needs AUI derivation rules integration (§2→task model, §3→zones, §4→absent-rules)
- ⚠️ Template needs BFF contract integration (§5→service calls, §5.2→view-model shape)
- ⚠️ Extension point section (§8) needs formalization of zone/rule/action/event extension types
