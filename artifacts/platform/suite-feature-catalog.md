# Artifact: Suite Feature Catalog

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Suite (feature inventory)
> **Source:** SUITE_UI_CATALOG_TEMPLATE.md

---

## Definition

A Suite Feature Catalog is the complete inventory of all platform-features belonging to a suite. It is the "shelf" that products select from during Application Engineering. The catalog lists every feature (leaf and composition) with its status, parent, and spec file reference. It includes the feature tree visualization, mandatory features, cross-suite dependencies, and evolution roadmap. The catalog is the human-readable companion to the Suite Catalog UVL.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| suiteId | string | ✓ | Suite ID (e.g., pps, fi, sd) |
| suiteName | string | ✓ | Full suite name (e.g., "Production Planning & Scheduling") |
| tier | enum | ✓ | T2 (Shared Enterprise Business) or T3 (Core Business Suites) — only tiers that own features |
| featureTree | string | ✓ | ASCII tree + Mermaid diagram showing the full feature hierarchy |
| mandatoryFeatures | string[] | ✓ | Feature IDs that are always included when the suite is selected (mandatory group members) |
| crossSuiteDependencies | CrossSuiteDep[] | | Features that require features from other suites (derived from UVL `requires` constraints) |
| featureRegister | FeatureEntry[] | ✓ | Complete register of all features in this suite |
| variabilitySummary | string | ✓ | Prose summary of key variability decisions and rationale |
| evolution | Evolution | | Planned features, deprecations, breaking change policy |

### crossSuiteDependencies (per dependency)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| featureId | string | ✓ | Feature in this suite (e.g., F-SD-001) |
| requiresFeatureId | string | ✓ | Feature in another suite (e.g., F-PPS-010) |
| targetSuite | string | ✓ | Suite ID of the required feature |
| reason | string | | Why this cross-suite dependency exists |

### featureRegister (per entry)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Feature ID (F-{SUITE}-{NNN} or F-{SUITE}-{NNN}-{NN}) |
| name | string | ✓ | Feature name |
| parent | string | | Parent composition node ID (null for root composition nodes) |
| status | enum | ✓ | planned, draft, review, approved, live, deprecated |
| specFile | string | ✓ | Relative path to the feature spec file |

### evolution

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| planned | PlannedFeature[] | | Features planned for future releases: id, name, targetRelease, description |
| deprecated | DeprecatedFeature[] | | Features being phased out: id, deprecationDate, removalDate, migrationPath |
| breakingChangePolicy | string | | How breaking changes to features are handled (versioning strategy, deprecation period) |

---

## Constraints

1. Every feature in the suite MUST be listed in the featureRegister.
2. The featureTree MUST be kept in sync with the Suite Catalog UVL.
3. mandatoryFeatures MUST correspond to features with groupType `mandatory` in their parent composition's UVL.
4. crossSuiteDependencies MUST match the `requires` constraints in the individual Feature Model UVL files.
5. Only T2 and T3 suites have feature catalogs (T1 and T4 do not own features).

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| lists | [Platform-Feature Spec](platform-feature-spec.md) | featureRegister[].id → feature specs |
| lists | [Feature Composition](feature-composition.md) | featureRegister[].id → composition nodes |
| produces | [Suite Catalog UVL](suite-catalog-uvl.md) | Feature tree → UVL root for this suite |
| belongs to | [Suite Spec](suite-spec.md) | suiteId → Suite.metadata.id |
| matched against by | [Feature Resolution](feature-resolution.md) | Resolution matches capabilities against catalog entries |
| selected from by | [Product](../product/product.md) | Products select features from this catalog |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Suite UI Catalog Template | `templates/SUITE_UI_CATALOG_TEMPLATE.md` | ✅ Covers feature tree, register, variability summary |
| Suite Catalog UVL Template | `templates/SUITE_CATALOG_TEMPLATE.uvl` | ✅ Machine-readable companion |
| Conceptual Stack §3.5 | `conceptual-stack.md` | ✅ Feature hierarchy and ownership rules |
| Artifact Catalog §3.10 | `artifact-catalog.md` | ✅ Summary |

---

## Gaps

- ⚠️ Minor alignment needed: evolution section (planned/deprecated/breakingChangePolicy) may not be in current template
