# Artifact: Platform Catalog UVL

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Platform (variability root)
> **Source:** PLATFORM_CATALOG_TEMPLATE.uvl

---

## Definition

The Platform Catalog UVL is the root of the entire variability tree for the OpenLeap platform. It imports all Suite Catalog UVL files (organized by tier) and assembles them under a single platform root feature. This is the top-level model that UVL solvers use for platform-wide constraint validation during product configuration. Every product configuration is validated against this root — cross-suite `requires` and `excludes` constraints are checked here.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| namespace | string | ✓ | Platform root namespace. Convention: `openleap.ui` |
| imports | string[] | ✓ | All Suite Catalog UVL files, organized by tier (T2 shared business suites, T3 core business suites) |
| features | FeatureDeclaration | ✓ | Platform root feature with or-group containing all suite roots |
| constraints | Constraint[] | | Platform-level cross-suite rules that cannot be expressed at the suite level |

### features (FeatureDeclaration)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| rootFeature | string | ✓ | Platform root feature name. Convention: `OpenLeapUI` |
| group | GroupDeclaration | ✓ | An `or` group (at least one suite must be selected) containing all suite root features |

### imports (organized by tier)

| Tier | Imported Suites | Description |
|------|----------------|-------------|
| T2 — Shared Enterprise Business | bp, cal, ... | Cross-suite business data suites with features |
| T3 — Core Business Suites | pps, fi, sd, hr, ps, ... | Core business suite catalogs |

Note: T1 (Platform Foundations) and T4 (Data & Analytics) are NOT imported because they do not own features.

### constraints (per constraint)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| expression | string | ✓ | UVL constraint expression (e.g., `F-SD-001 requires F-BP-001`, platform-wide invariants) |
| scope | string | | Description of which suites are affected |
| rationale | string | | Business reason for this platform-level rule |

---

## Constraints

1. The namespace MUST be `openleap.ui`.
2. The root feature MUST be `OpenLeapUI`.
3. The root group is `or` — at least one suite must be selected in any valid product configuration.
4. Only T2 and T3 suite catalogs are imported (T1 and T4 do not own features).
5. Platform-level constraints are rules that span multiple suites and cannot be expressed within a single suite's catalog.
6. Every Suite Catalog UVL MUST be imported — no suite may be omitted from the platform root.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| imports | [Suite Catalog UVL](suite-catalog-uvl.md) | All suite catalogs by tier |
| validated against by | Product Configuration | Products are validated against this root model |
| constrains | [Feature Model UVL](feature-model.md) | Platform-level constraints apply to individual features |
| top of tree for | [Composition Model UVL](composition-model.md) | Transitively imports all composition models via suite catalogs |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Platform Catalog UVL Template | `templates/PLATFORM_CATALOG_TEMPLATE.uvl` | ✅ Covers namespace, imports, features, constraints |
| UVL Standard | Sundermann et al. (2021), MODEVAR community | ✅ Language specification |
| Conceptual Stack §7 | `conceptual-stack.md` | ✅ Tier governance — T2/T3 own features |
| Artifact Catalog §3.12 | `artifact-catalog.md` | ✅ Summary |

---

## Gaps

- ⚠️ Minor: template is solid, may need updates as new suites are added
