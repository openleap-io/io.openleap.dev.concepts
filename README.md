# OpenLeap Specification Framework

This repository contains the conceptual foundations and specification tooling for the OpenLeap platform. It defines the overarching SPLE platform model, normative artifact definitions, consolidated authoring templates, canonical examples, and governance rules.

This is the **meta-layer** that defines HOW to write specifications. The actual specifications live in [dev.spec](https://github.com/openleap-io/io.openleap.dev.spec).

---

## Structure

### Root Documents

| Document | Description | Status |
|----------|-------------|--------|
| [conceptual-stack.md](conceptual-stack.md) | Authoritative SPLE platform model: Platform/Product spaces, conceptual layers, scientific foundations. All other documents derive from this. | Authoritative |
| [artifact-catalog.md](artifact-catalog.md) | Normative definition of every artifact in the platform — fields, relationships, constraints, format. | Authoritative |
| [reconciliation.md](reconciliation.md) | Formal mapping of 4-Tier Service Model, 5-Layer Specification Model, and 3-Space Perspective. Unified glossary. | Authoritative |

### Templates (`templates/`)

Consolidated specification templates — the current standard for authoring specs.

| Template | Purpose |
|----------|---------|
| `platform/suite-spec.md` | Suite Specification (11 sections) |
| `platform/domain-service-spec.md` | Domain/Service Specification (15 sections) |
| `platform/feature-spec.md` | Platform-Feature Specification (10 sections) |
| `platform/feature-composition-spec.md` | Feature Composition Node Specification |
| `platform/feature-attrs.md` | Feature Attribute Documentation |
| `platform/workflow-spec.md` | Workflow Specification |
| `platform/uvl/` | UVL grammar templates |
| `platform/yaml/` | Screen Contract (AUI) templates |
| `product/product-spec.md` | Product Specification |
| `ARTIFACT_TO_TEMPLATE_MAPPING.md` | Maps artifacts to template chapters |
| `template-registry.json` | Machine-readable template metadata |

### Artifacts (`artifacts/`)

Artifact detail specifications — defines what each artifact IS (not how to fill in a template).

- `platform/` — 15 Platform Space artifact definitions
- `product/` — 25 Product Space artifact definitions

### Examples (`examples/`)

Canonical examples demonstrating how to use the templates.

| Example | Type | What It Shows |
|---------|------|---------------|
| `platform/pps-suite/` | Suite Spec | Complete PPS Suite specification |
| `platform/pps-im-domain/` | Domain Spec | PPS Inventory Management service |
| `platform/pps-im-feature-goods-receipt/` | Feature Spec | F-PPS-IM-001 with all companions (.uvl, .aui.yaml, .bff) |
| `platform/warehouse-ops-resolution/` | Feature Resolution | Decompose output example |
| `product/warehouse-operations/` | Product Spec | Complete product with 50+ Elara artifacts |

### Governance (`governance/`)

Rules and guidelines for maintaining the specification framework.

- `template-governance.md` — How to maintain and evolve templates
- `bff-guideline.md` — Backend-for-Frontend specifications
- `suite_package_blueprint.md` — Suite packaging guidance

---

## Relationship to Other Repos

```
dev.concepts (this repo)         — defines HOW to write specs
  │ templates, artifacts, examples
  ↓ used by
dev.spec                         — the actual domain specifications
  ↓ operated on by
dev.prompts                      — AI automation for spec operations
  ↓ documented in
dev.hub                          — central documentation & navigation
```

---

## Related Repositories

| Repository | Relationship |
|------------|-------------|
| [dev.hub](https://github.com/openleap-io/io.openleap.dev.hub) | Central documentation, architecture overview |
| [dev.spec](https://github.com/openleap-io/io.openleap.dev.spec) | Domain & service specifications that use these templates |
| [dev.prompts](https://github.com/openleap-io/io.openleap.dev.prompts) | AI prompts that reference the template registry |
