# Artifact: Product

> **Space:** Product (Application Engineering)
> **Conceptual Stack Layer:** Product
> **Owner:** Application Engineering Team
> **Tool:** Elara
> **Equivalent:** Elara Project

---

## Definition

A Product is the top-level container of the Product Space. It represents a composed UI application for a specific customer or market segment. A Product is equivalent to an Elara Project — it contains all artifacts that describe what this customer needs.

The Product's ultimate goal is to declare **required capabilities** that the Platform resolves into concrete platform-features via Feature Resolution.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Unique product identifier |
| name | string | ✓ | Human-readable product name |
| description | string | ✓ | What this product does, for whom |
| scope | [ProjectScope](project-scope.md) | ✓ | Business problem, goals, in/out scope, constraints, assumptions |
| personas | [Persona](persona.md)[] | ✓ | Who uses this product (min. 1) |
| processes | [BusinessProcess](business-process.md)[] | ✓ | How the business works (min. 1) |
| actors | [Actor](actor.md)[] | ✓ | Who is involved |
| businessObjects | [BusinessObject](business-object.md)[] | | Things the business works with |
| businessEvents | [BusinessEvent](business-event.md)[] | | What happens in the business |
| businessRules | [BusinessRule](business-rule.md)[] | | Constraints and policies |
| businessCapabilities | [BusinessCapability](business-capability.md)[] | ✓ | What the business can do (min. 1) |
| glossaryTerms | [GlossaryTerm](glossary-term.md)[] | ✓ | Business vocabulary (min. 1) |
| qualityRequirements | [QualityRequirement](quality-requirement.md)[] | | How well must it work |
| externalInterfaces | [ExternalInterface](external-interface.md)[] | | What's around us |
| kpis | [KPI](kpi.md)[] | | How do we measure success |
| functionalAreas | [FunctionalArea](functional-area.md)[] | | How does it cluster |
| decisions | [Decision](decision.md)[] | | What did we decide and why |
| workflowCandidates | [WorkflowCandidate](workflow-candidate.md)[] | | Patterns flagged for Platform workflows |
| requiredCapabilities | ref[] | ✓ | BusinessCapability IDs sent to Platform for Feature Resolution |
| featureResolution | [FeatureResolution](../platform/feature-resolution.md) | | Returned by Platform — capability→feature mapping + gaps |
| bffConfiguration | BFFConfig | | Derived from Feature Resolution |
| navigationArchitecture | NavigationMap | | How features are arranged in the UI |
| customServices | CustomServiceRef[] | | Customer-specific services outside the platform |

---

## Constraints

1. A product MUST have at least one Persona
2. A product MUST have at least one Business Process
3. A product MUST have at least one Business Capability
4. A product MUST have at least one Glossary Term
5. A product MUST declare requiredCapabilities (the input to Platform Feature Resolution)
6. After Feature Resolution: all UVL `requires` constraints must be satisfied in the feature selection

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| contains | All Product Space artifacts | Embedded |
| sends to Platform | Required Capabilities | requiredCapabilities → Feature Resolution |
| receives from Platform | Feature Resolution | featureResolution |
| references | Platform-Features | Via featureResolution.featureSelection[].featureId |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Elara Business Spec | `io.openleap.elara/spec/elara_business_specification.md` §4 | ✅ Defines 15 contained artifact types |
| Elara Enhancement | `artifacts/elara-product-discovery-enhancement.md` | ✅ Defines Product + Persona |
| Product Config Template | `templates/PRODUCT_CONFIG_SPEC_TEMPLATE.md` | ⚠️ Has feature selection but missing: requiredCapabilities, Feature Resolution, BFF config |
| Product Config UVL | `templates/PRODUCT_CONFIG_TEMPLATE.uvl` | ✅ Variability resolution format |
| Freeze Schema | `io.openleap.telos/schemas/conceptual-freeze.schema.json` | ⚠️ Missing: BusinessObject, BusinessEvent, ExternalInterface, KPI, WorkflowCandidate, Persona, Product |

---

## Gaps

- Product Config template needs requiredCapabilities field and Feature Resolution reference
- Freeze schema needs 7 additional artifact types
- BFF Configuration has no template (new concept from Conceptual Stack)
