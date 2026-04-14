# Artifact: Screen Contract (AUI)

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Platform-Feature (UI dimension)
> **Source:** Conceptual Stack §3.5.1, ui-sple.md §7-§9

---

## Definition

A Screen Contract AUI (Abstract UI) is the platform-free UI specification derived from the Platform-Feature Spec. It applies the Cameleon Reference Framework (Calvary et al., 2003) to separate what a screen IS from how it RENDERS. The AUI defines the task model (what the user does and in what order), zones (named layout regions with priorities), absent-rules (what happens when optional zones are not rendered), business rules (role gates, state conditions), and extension zones (hooks for product-level UI customization). The AUI is the stable contract between platform specification and platform-specific CUI implementations.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| featureId | string | ✓ | Parent Platform-Feature ID (e.g., F-PPS-012-01) |
| taskModel | TaskModel | ✓ | User tasks and their temporal relationships |
| zones | Zone[] | ✓ | Named layout regions with type and priority |
| absentRules | AbsentRule[] | | What happens when optional zones are not rendered |
| businessRules | ScreenRule[] | | Role gates, state conditions, action consequences |
| extensionZones | ExtensionZone[] | | Defined hooks for product-level UI customization |

### taskModel

The task model uses five operators from the Cameleon Reference Framework to describe temporal relationships between user tasks:

| Operator | Syntax | Meaning |
|----------|--------|---------|
| sequential | `sequential(A, B)` | A must complete before B starts |
| concurrent | `concurrent(A, B)` | A and B are co-accessible (side-by-side or tabs) |
| alternative | `alternative(A, B)` | Mutual exclusion — one or the other, never both |
| optional | `optional(A)` | A may be absent — triggers an absent-rule |
| enabling | `enabling(B <- A)` | B is disabled until A provides data |

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| taskModel.rootTask | string | ✓ | Top-level task name |
| taskModel.tasks | Task[] | ✓ | All tasks with id, name, description |
| taskModel.operators | OperatorDeclaration[] | ✓ | Temporal relationships between tasks using the five operators |

### zones (per zone)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Zone identifier (e.g., zone:header, zone:form-fields, zone:actions) |
| name | string | ✓ | Human-readable zone name |
| type | enum | ✓ | fixed (platform shell, cannot be overridden), variable (product integration slots), feature-gated (visible only when a feature flag is active) |
| priority | integer | ✓ | Layout priority (lower = more important, rendered first) |
| dataBinding | string | | Reference to view-model field this zone binds to |
| taskRef | string | | Which task this zone belongs to |
| containedFields | FieldSpec[] | | Form fields within this zone: fieldId, label, inputType, required, validationRules[] |

### absentRules (per rule)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| zoneId | string | ✓ | Which zone this rule applies to |
| rule | enum | ✓ | collapse-up (predecessor expands to fill space), end-page (page ends at previous zone), tab-hidden (tab removed from navigation), panel-section-hidden (panel section collapses) |
| condition | string | | When this absent-rule activates (e.g., "feature F-PPS-012-02 is EXCLUDED") |

### businessRules (per rule)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Rule identifier |
| type | enum | ✓ | roleGate, stateCondition, actionConsequence |
| description | string | ✓ | What this rule does |
| affectedZones | string[] | | Which zones are affected |
| condition | string | ✓ | When the rule applies (role, state, or action trigger) |
| effect | string | ✓ | What happens (hide, disable, show, enable, redirect) |

### extensionZones (per zone)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Extension zone identifier |
| name | string | ✓ | Human-readable name (e.g., "Custom Fields Section") |
| insertionPoint | string | ✓ | Where in the layout this zone is inserted (after zoneId) |
| maxFields | integer | | Maximum number of fields a product can add |
| allowedFieldTypes | string[] | | Permitted input types for product-added fields |

---

## Derivation from Feature Spec

| Feature Spec Section | AUI Element | Derivation Rule |
|---------------------|-------------|-----------------|
| §2 User Journey | taskModel | Journey sequence steps → task operators. Sequential steps → `sequential()`. Parallel activities → `concurrent()`. Decision branches → `alternative()`. Optional screens → `optional()`. Data-dependent screens → `enabling()`. |
| §3 Interaction Requirements | zones | Form fields → zones with type, priority, dataBinding. Field groups → zone boundaries. Cross-field rules → zone-level businessRules. Actions → action zones. |
| §4 Edge Cases | absentRules | Screen states (empty, error) → absent-rules for conditional zones. Optional UI sections → `optional()` task + absent-rule. |
| §8 Variability (extension points) | extensionZones | Declared extension zones → product-customizable UI regions. |

---

## Constraints

1. Every form field in Feature Spec §3 MUST map to a zone.
2. Every edge case in Feature Spec §4 MUST map to an absent-rule or screen state definition.
3. Zone priorities determine rendering order — CUI implementations MUST respect priority ordering.
4. Extension zones are declared in the platform; products MUST NOT add zones outside defined extension points.
5. The AUI is platform-agnostic — it MUST NOT contain platform-specific rendering instructions (that belongs in the CUI).
6. One AUI can have multiple CUIs (web, mobile, desktop). The AUI is the stable contract; CUIs are derived.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derived from | [Platform-Feature Spec](platform-feature-spec.md) | §2 journey, §3 interaction, §4 edge cases, §8 extension points |
| drives | CUI (Concrete UI) | AUI operators → platform-specific rendering (web, mobile, desktop) |
| references | [BFF Contract](bff-contract.md) | zones[].dataBinding → BFF viewModelShape fields |
| referenced by | [Feature Model UVL](feature-model.md) | Feature-gated zones correspond to UVL optional features |
| extended by | Product-Feature | Products fill extensionZones with customer-specific fields |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Conceptual Stack §3.5.1 | `concepts/CONCEPTUAL_STACK.md` | ✅ Defines AUI concepts: task model operators, zones, absent-rules |
| UI-SPLE document §7-§9 | `concepts/ui-sple/ui-sple.md` | ✅ Conceptual foundation for AUI/CUI split |
| Cameleon Reference Framework | Calvary et al. (2003) | ✅ Academic foundation |
| Artifact Catalog §3.7 | `concepts/ARTIFACT_CATALOG.md` | ✅ Field summary |

---

## Gaps

- ❌ No template exists — must be created as a YAML template
- ❌ No schema exists — must be formalized
- ⚠️ CUI derivation rules (AUI operator → platform-specific widget) are documented conceptually but not as formal mapping tables
