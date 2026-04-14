# Artifact: Feature Resolution

> **Space:** Platform (serves Product Space)
> **Conceptual Stack Layer:** Bridge (Product → Platform)
> **Source:** Agora Spec "Decompose" concept

---

## Definition

A Feature Resolution is the result produced by the Platform (Telos Decompose) when a Product submits its Required Capabilities. The Platform resolves each Business Capability against the Feature Catalog and returns a structured mapping: which capabilities are matched by existing platform-features, which are partially covered, and which are gaps. The Feature Resolution is the formal bridge artifact between Product Space needs and Platform Space offerings.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| productRef | ref | ✓ | Reference to the Product this resolution is for |
| capabilityMappings | CapabilityMapping[] | ✓ | Per-capability resolution: matched, partial, or gap |
| featureSelection | FeatureSelection[] | ✓ | Resolved list of platform-features with mode and defaults |
| gapAnalysis | Gap[] | | Capabilities with no or partial platform match |
| requiredConstraintsSatisfied | boolean | ✓ | Whether all UVL `requires` constraints are satisfied by the selection |
| validationErrors | string[] | | Constraint violations, if any |

### CapabilityMapping (embedded)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| capabilityRef | ref | ✓ | Reference to the Product's BusinessCapability ID |
| status | enum | ✓ | MATCHED, PARTIAL, GAP |
| matchedFeatures | string[] | | Platform-Feature IDs that fulfill this capability (e.g., F-SD-001) |
| suggestedMode | enum | | FULL, READ_ONLY, EMBEDDED |
| gapDescription | string | | If PARTIAL or GAP: what is missing from the platform |
| resolution | enum | | EXTEND_PLATFORM, CUSTOM_SERVICE, DEFER |

### FeatureSelection (embedded)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| featureId | string | ✓ | Platform-Feature ID (pattern: F-{SUITE}-{NNN} or F-{SUITE}-{NNN}-{NN}) |
| mode | enum | ✓ | FULL, READ_ONLY, EMBEDDED, EXCLUDED |
| attributeDefaults | map | | Platform default attribute values for this feature |
| extensionPointsAvailable | string[] | | Extension point IDs that the Product may fill |

---

## Constraints

1. The resolution MUST satisfy all UVL `requires` constraints. If F-SD-001 requires F-BP-001, the resolution MUST include both.
2. Every capability in the Product's requiredCapabilities MUST appear in capabilityMappings.
3. A feature with mode EXCLUDED MUST NOT appear in the BFF's activeFeatures.
4. If requiredConstraintsSatisfied is false, validationErrors MUST be non-empty.
5. The resolution is a proposal — the Product PO confirms or overrides before it becomes the Product's featureResolution.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| input from | [Product](../product/product.md) | Product.requiredCapabilities (BusinessCapability IDs) |
| matches against | [Suite Feature Catalog](suite-feature-catalog.md) | capabilityMappings[].matchedFeatures → catalog entries |
| output to | [Product](../product/product.md) | Returned as Product.featureResolution |
| references | [Platform-Feature Spec](platform-feature-spec.md) | featureSelection[].featureId |
| feeds | [BFF Configuration](../product/product.md) | featureSelection drives BFF activeFeatures and gating |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Agora Spec "Decompose" concept | `io.openleap.dev.spec/artifacts/` | ⚠️ Mentions Decompose as a Telos function but does not define the artifact |
| Artifact Catalog §3.0 | `artifact-catalog.md` | ✅ Field definitions authoritative |
| Conceptual Stack §2.8 | `conceptual-stack.md` | ✅ Describes the capability→feature resolution flow |

---

## Gaps

- ❌ No formal template exists — must be created
- ❌ No schema exists — must be defined in Telos
- ⚠️ The Decompose algorithm (how matching works) is not yet specified — only the input/output artifact is defined here
