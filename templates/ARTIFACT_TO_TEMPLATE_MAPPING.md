# Artifact-to-Template Mapping

> Maps each artifact from the Artifact Catalog to its chapter in a consolidated, human-readable template.

---

## Platform Templates

### Template: Domain/Service Specification (`platform/domain-service-spec.md`)

One consolidated template for specifying a microservice. Integrates content from multiple artifacts into numbered chapters.

| Chapter | Title | Artifact Source(s) | Notes |
|---------|-------|-------------------|-------|
| §0 | Document Purpose & Scope | domain-spec (scope) | Purpose, audience, in/out scope, related documents |
| §1 | Business Context | domain-spec (businessContext) | Problems solved, business value, stakeholders, strategic positioning |
| §2 | Service Identity | domain-spec (metadata) | Service ID, suite, domain, base package, API path, team, repo |
| §3 | Domain Model | domain-spec (domainModel) | Aggregates (root + entities + VOs), enumerations, shared types, lifecycle states, invariants. Each aggregate fully detailed. |
| §4 | Business Rules | domain-spec (businessRules) | Catalog with ID, name, type, enforcement, error code. Detailed definitions. Validation rules. Reference data deps. |
| §5 | Use Cases | *(new — from DOMAIN_SPEC_TEMPLATE §3)* | Canonical format: id, type, trigger, aggregate, operation, inputs, outputs, events, REST endpoint, idempotency, errors. Process flow diagrams. |
| §6 | REST API | domain-spec (contracts.restApi) + api-contract | Base path, auth, endpoints with full request/response examples. Reference to OpenAPI file. |
| §7 | Events & Integration | domain-spec (contracts.events + integration) | EDA pattern, published events (routing key, payload, consumers), consumed events, event flow diagrams, integration points. |
| §8 | Data Model | domain-spec (dataModel) | Storage tech, tables with columns/types/constraints/indexes, reference data, ERD diagram. |
| §9 | Security & Compliance | domain-spec (security) | Data classification, access control (roles → permissions), compliance requirements (GDPR, ISO, SOX). §9.4 DORA ICT Risk References *(v1.1.0, optional)*. §9.5 SBOM Requirements *(v1.1.0, optional)*. |
| §10 | Quality Attributes | domain-spec (from Elara QualityRequirements) | Performance targets, availability SLAs, scalability, maintainability. §10.5 SLI/SLO Reference *(v1.1.0, optional)*. §10.6 Resilience Requirements *(v1.1.0, optional)*. |
| §11 | Feature Dependencies | *(new — from Conceptual Stack)* | Which platform-features (F-{SUITE}-{NNN}) call this service. Endpoints used per feature. BFF aggregation hints. |
| §12 | Extension Points | *(new — from Conceptual Stack)* | Defined hooks: extension events, aggregate hooks, extension API endpoints. Open-Closed Principle. |
| §13 | Migration & Evolution | domain-spec (implicit) | Data migration strategy, deprecation path, versioning policy. |
| §14 | Decisions & Open Questions | domain-spec (adrs, suiteAdrRefs) | ADRs, consistency checks, conflicts, open questions with severity. |
| §15 | Appendix | domain-spec (glossary, changelog) | Service-local glossary, references, change log, review & approval. |

**Companion files (referenced, not embedded):**
- `openapi.yaml` → OpenAPI contract (referenced in §6)
- `*.schema.json` → Event contracts (referenced in §7)

---

### Template: Suite Specification (`platform/suite-spec.md`)

One consolidated template for specifying a suite — the UBL-bounded domain cluster.

| Chapter | Title | Artifact Source(s) | Notes |
|---------|-------|-------------------|-------|
| §0 | Suite Identity & Purpose | suite-spec (metadata + purpose) | ID, name, version, status, owner. Business purpose, in/out scope, target users, business value. |
| §1 | Ubiquitous Language | suite-spec (ubiquitousLanguage) | THE central chapter. Glossary entries with ID, term, aliases, definition, examples, related terms. This defines the suite boundary. |
| §2 | Domain Model | suite-spec (domainModel) | Conceptual overview (Mermaid), core concepts, shared kernel, bounded context map (intra-suite). |
| §3 | Service Landscape | suite-spec (serviceLandscape) | Service catalog (ID, name, BC, status, responsibility), responsibility matrix, dependency diagram. |
| §4 | Integration Patterns | suite-spec (integrationPatterns) | Pattern decision (event-driven/orchestration/hybrid) + rationale. Event flows. Sync vs async decisions. Error handling. |
| §5 | Event Conventions | suite-spec (eventConventions) | Routing key pattern, payload envelope, versioning strategy, event catalog. |
| §6 | Feature Catalog | suite-feature-catalog + suite-catalog-uvl | Feature tree (ASCII + Mermaid), mandatory features, cross-suite dependencies, feature register, variability summary. |
| §7 | Cross-Cutting Concerns | suite-spec (crossCuttingConcerns) | Compliance, security, multi-tenancy (model, isolation, propagation), audit (requirements, retention policies). |
| §8 | External Interfaces | suite-spec (externalInterfaces) | Outbound (→ other suites), inbound (← other suites), external context mapping (DDD patterns). |
| §9 | Architecture Decisions | suite-spec (adrs) | Suite-level ADRs (ADR-{SUITE}-NNN), with scope, rationale, consequences. |
| §10 | Roadmap | suite-spec (roadmap) | Planned phases, timeframes, deprecations. |
| §11 | Appendix | — | Change log, review & approval. |

**Companion files (referenced, not embedded):**
- `{suite}.catalog.uvl` → Suite UVL catalog root (referenced in §6)
- Per-feature files in the feature catalog directory

---

### Template: Platform-Feature Specification (`platform/feature-spec.md`)

One consolidated template for a platform-feature — the central artifact bridging both spaces.

| Chapter | Title | Artifact Source(s) | Notes |
|---------|-------|-------------------|-------|
| §0 | Feature Identity & Orientation | platform-feature-spec (id, orientation) | ID (F-{SUITE}-{NNN}), suite, name, status, summary, non-goals, entry/exit points. |
| §1 | User Goal & Scenarios | platform-feature-spec (§1) | Goal statement, 2–5 scenarios (happy path, edge cases, error cases). |
| §2 | User Journey & Screen Layout | platform-feature-spec (§2) | Mermaid sequence diagram, ASCII screen layouts. Derivation notes: this section feeds the AUI task model. |
| §3 | Interaction Requirements | platform-feature-spec (§3) | Form fields table, actions table, cross-field rules, confirmation dialogs. Derivation notes: this section feeds AUI zones. |
| §4 | Edge Cases & Screen States | platform-feature-spec (§4) | Component states (loading, empty, error, populated), specific edge cases. Derivation notes: this section feeds AUI absent-rules. |
| §5 | Backend Dependencies & BFF Contract | platform-feature-spec (§5) + bff-contract | Service calls table (service, tier, endpoint, method, isMutation, failure mode). View-model shape (JSON-C). Feature-gating rules per mode. Failure modes. Caching hints. |
| §6 | Screen Contract (AUI) | screen-contract-aui | Task model (operators), zones (typed, prioritized), absent-rules, business rules, extension zones. Inline YAML or reference to companion file. |
| §7 | i18n, Permissions & Accessibility | platform-feature-spec (§6) | Translation keys, role-based visibility/enable rules. |
| §8 | Acceptance Criteria | platform-feature-spec (§7) | Given/When/Then statements covering all scenarios, edge cases, permissions. |
| §9 | Dependencies, Variability & Extension Points | platform-feature-spec (§8) + feature-model | Required features (UVL `requires`), variability points (attributes with binding times), extension points (hooks for product customization). |
| §10 | Change Log & Review | — | Version history, approval. |

**Companion files (referenced from within the template):**
- `F-{SUITE}-{NNN}.uvl` → Feature variability model (referenced in §9) — template: `platform/uvl/feature-leaf.uvl`
- `F-{SUITE}-{NNN}.aui.yaml` → Screen contract (referenced in §6, can also be inline) — template: `platform/yaml/screen-contract-aui.yaml`
- `F-{SUITE}-{NNN}.attrs.md` → Attribute documentation (optional, referenced in §9 if complex) — template: `platform/feature-attrs.md`

---

### Template: Feature Composition (`platform/feature-composition-spec.md`)

| Chapter | Title | Artifact Source(s) |
|---------|-------|-------------------|
| §0 | Composition Identity | feature-composition (id, name, position in tree) |
| §1 | Children & Variability Structure | feature-composition (children, group types, rationale) |
| §2 | Constraints | feature-composition + composition-model (intra-node, cross-tree) |
| §3 | Valid & Invalid Configurations | feature-composition (examples) |
| §4 | Change Log | — |

**Companion:** `F-{SUITE}-{NNN}.uvl` (referenced)

---

### Template: Workflow Specification (`platform/workflow-spec.md`)

| Chapter | Title | Artifact Source(s) |
|---------|-------|-------------------|
| §0 | Workflow Identity | workflow-spec (id, type, suite, trigger) |
| §1 | Steps | workflow-spec (ordered steps with service ref, compensation, retry, timeout) |
| §2 | Retry & Compensation | workflow-spec (retryPolicy, compensationStrategy) |
| §3 | Referenced Services | workflow-spec (service IDs, roles, endpoints, events) |
| §4 | Observability | workflow-spec (metrics, alerts) |
| §5 | Elara Cross-Reference | workflow-spec (link to originating business process) |
| §6 | Decisions & Change Log | — |

---

## Product Templates

### Template: Product Specification (`product/product-spec.md`)

One consolidated template for a product — the application engineering deliverable.

| Chapter | Title | Artifact Source(s) | Notes |
|---------|-------|-------------------|-------|
| §0 | Product Identity | product (id, name, description) | What this product is, for whom. |
| §1 | Project Scope | project-scope | Business problem, goals, in/out scope, constraints, assumptions. |
| §2 | Actors & Stakeholders | actor[] | All actors with responsibilities, decision authority, stakeholder interests. |
| §3 | Personas | persona[] | Enriched actors with IAM roles, job-to-be-done, frequency, comfort, pain points. |
| §4 | Business Processes | business-process[] | All processes (L0/L1/L2) with steps, gateways, data flows, actors. BPMN references. |
| §5 | Business Objects | business-object[] | Data entities with attributes, lifecycle states, ownership. |
| §6 | Business Events | business-event[] | Events chaining processes, with trigger types and carried data. |
| §7 | Business Rules | business-rule[] | Typed rules with rationale, source, examples, exceptions. |
| §8 | Business Capabilities | business-capability[] | What the business can do (core/supporting/generic), fulfilled by which processes. |
| §9 | Domain Vocabulary | glossary-term[] | Business terms, aliases, definitions, disambiguation. |
| §10 | Quality Requirements | quality-requirement[] | NFRs by category with measurable targets. |
| §11 | External Interfaces | external-interface[] | Systems, agencies, channels with direction. |
| §12 | KPIs | kpi[] | Success metrics with measurement method and targets. |
| §13 | Functional Areas | functional-area[] | Logical clustering with boundaries, rationale, dependencies. |
| §14 | Decisions | decision[] | Structural choices with context, rationale, alternatives. |
| §15 | Workflow Candidates | workflow-candidate[] | Patterns flagged for Platform (batch, saga, scheduled). |
| §16 | Required Capabilities & Feature Resolution | product (requiredCapabilities) + feature-resolution | THE bridge: capabilities sent to Platform, resolution returned (matched/partial/gap). |
| §17 | Feature Selection & Configuration | product (featureSelections) | Selected features with mode, attribute overrides, extension points filled. |
| §18 | BFF Configuration | product (bffConfiguration) | Deployment model, feature-gating, extension routing, view-model overrides. |
| §19 | Navigation Architecture | product (navigationArchitecture) | How features are arranged in the UI. Entry points, navigation map. |
| §20 | Custom Services | product (customServices) | Customer-specific services outside the platform. |
| §21 | Compliance & Operational Requirements | *(new — DORA, v1.1.0)* | Applicable governance, product compliance posture, product-specific requirements. Optional. |
| §22 | Appendix | — | Change log, review & approval. |

---

## DORA Governance Templates

### Template: ICT Risk Register (`platform/governance/risk-register.md`)

| Chapter | Title | Artifact Source(s) |
|---------|-------|-------------------|
| §0 | Risk Context | risk-register (scope, regulatory drivers) |
| §1 | Risk Register | risk-register (entries with ID, category, probability, impact, treatment, controls) |
| §2 | Risk Treatment Plans | risk-register (detailed mitigation actions per high/critical risk) |
| §3 | Risk Heatmap | risk-register (probability × impact visualization) |
| §4 | Residual Risk Assessment | risk-register (residual scores after controls) |
| §5 | Change Log | — |

**Governance:** GOV-DORA-002 (ICT Risk Management Policy)

---

### Template: SLI/SLO Specification (`platform/domain/sli-slo-spec.md`)

| Chapter | Title | Artifact Source(s) |
|---------|-------|-------------------|
| §0 | Service Identity | sli-slo-spec (service reference, tier) |
| §1 | Service Level Indicators | sli-slo-spec (metric definitions, measurement methods) |
| §2 | Service Level Objectives | sli-slo-spec (targets, windows, breach consequences) |
| §3 | Error Budget | sli-slo-spec (budget calculation, burn rate alerting) |
| §4 | Alerting Configuration | sli-slo-spec (alert conditions, severity mapping) |
| §5 | Dashboard Requirements | sli-slo-spec (visibility requirements) |
| §6 | Review Cadence | sli-slo-spec (review frequency, adjustment criteria) |
| §7 | Change Log | — |

**Companion to:** Domain/Service Spec (TPL-SVC) §10.5

---

### Template: Incident Response Specification (`platform/governance/incident-response-spec.md`)

| Chapter | Title | Artifact Source(s) |
|---------|-------|-------------------|
| §0 | Scope and Applicability | incident-response-spec (covered services/suites) |
| §1 | Classification Matrix | incident-response-spec (severity to criteria mapping) |
| §2 | Response Playbooks | incident-response-spec (per-severity: detection through recovery) |
| §3 | Escalation Matrix | incident-response-spec (severity × time → action) |
| §4 | Communication Plan | incident-response-spec (internal/external notification) |
| §5 | DORA Regulatory Notification | incident-response-spec (trigger, timeline, format) |
| §6 | Post-Mortem Template | incident-response-spec (structured post-mortem format) |
| §7 | Testing and Exercises | incident-response-spec (tabletop, simulations, schedule) |
| §8 | Change Log | — |

**Governance:** GOV-DORA-003 (Incident Management Policy)

---

### Template: DevSecOps Pipeline Specification (`platform/governance/devsecops-pipeline-spec.md`)

| Chapter | Title | Artifact Source(s) |
|---------|-------|-------------------|
| §0 | Pipeline Identity | pipeline-spec (scope, repositories, environments) |
| §1 | Pipeline Stages | pipeline-spec (ordered stages with gates) |
| §2 | Security Gates | pipeline-spec (SAST, SCA, secrets, container, IaC) |
| §3 | SBOM Generation | pipeline-spec (format, tool, storage, retention) |
| §4 | Policy as Code | pipeline-spec (OPA/Conftest rules) |
| §5 | Approval Gates | pipeline-spec (change type × tier → approvals) |
| §6 | Audit Artifacts | pipeline-spec (per-stage evidence) |
| §7 | Compliance Thresholds | pipeline-spec (metric thresholds) |
| §8 | Change Log | — |

**Governance:** GOV-DORA-004 (Change Management & Deployment Governance)

---

### Template: Resilience Testing Specification (`platform/governance/resilience-testing-spec.md`)

| Chapter | Title | Artifact Source(s) |
|---------|-------|-------------------|
| §0 | Scope | resilience-testing-spec (services, tiers, objectives) |
| §1 | Recovery Objectives | resilience-testing-spec (RTO/RPO per tier) |
| §2 | Failover Test Scenarios | resilience-testing-spec (scenarios with acceptance criteria) |
| §3 | Chaos Engineering | resilience-testing-spec (experiments, blast radius, abort conditions) |
| §4 | Load and Stress Testing | resilience-testing-spec (load profiles, success criteria) |
| §5 | Backup and Recovery Testing | resilience-testing-spec (strategy, frequency, restore tests) |
| §6 | DORA TLPT | resilience-testing-spec (threat-led penetration testing scope) |
| §7 | Annual Test Schedule | resilience-testing-spec (quarterly test plan) |
| §8 | Reporting and Evidence | resilience-testing-spec (documentation for audit) |
| §9 | Change Log | — |

**Governance:** GOV-DORA-001 (DORA Compliance Framework)

---

### Template: Third-Party Risk Assessment (`platform/governance/third-party-risk-assessment.md`)

| Chapter | Title | Artifact Source(s) |
|---------|-------|-------------------|
| §0 | Provider Identity | third-party-assessment (provider, contract, criticality) |
| §1 | Service Description | third-party-assessment (SLAs, data, locations) |
| §2 | Risk Assessment | third-party-assessment (certifications, security posture) |
| §3 | Contractual Review | third-party-assessment (DORA Art. 30 clause checklist) |
| §4 | Concentration Risk | third-party-assessment (dependency analysis, alternatives) |
| §5 | Exit Strategy | third-party-assessment (migration plan, data portability) |
| §6 | Monitoring Plan | third-party-assessment (continuous monitoring, triggers) |
| §7 | SBOM Integration | third-party-assessment (dependency tracking) |
| §8 | Change Log | — |

**Governance:** GOV-DORA-005 (Third-Party ICT Provider & Supply Chain Governance)

---

## `prod.*` Repository Templates

Templates for the `io.openleap.prod.{product}` repository convention defined in
[dev.hub architecture/product-repo-layout.md](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md).
Unlike the consolidated `product-spec.md` above (one big in-Elara document),
these split the product specification across small file types that are
individually reviewable in pull requests and individually consumable by the
packaging step that produces `bff/productconfig.yaml`.

| Template ID | File | Purpose | Registry entry |
|-------------|------|---------|----------------|
| TPL-PROD-MANIFEST | `product/prod/product.yaml` | Product manifest (identity, owner, target customer, catalog pin, satellite refs) | ✅ |
| TPL-PROD-SEL | `product/prod/features/selection.uvl` | Feature selection — references `F-{SUITE}-{NNN}`; declares inclusion mode | ✅ |
| TPL-PROD-BIND | `product/prod/variability/bindings.yaml` | Resolved UVL attributes with compile/deploy/runtime binding times | ✅ |
| TPL-PROD-EXT-FIELDS | `product/prod/extensions/fields.yaml` | Feature-level `extension-field` fill | ✅ |
| TPL-PROD-EXT-RULES | `product/prod/extensions/rules.yaml` | Feature-level `extension-rule` fill | ✅ |
| TPL-PROD-EXT-ACTIONS | `product/prod/extensions/actions.yaml` | Feature-level `extension-action` fill | ✅ |
| TPL-PROD-PERSONA | `product/prod/personas/persona.yaml` | Exported persona with IAM roles and device profile | ✅ |
| TPL-PROD-PROCESS | `product/prod/processes/process.bpmn` | BPMN 2.0 stub with `ol:*` OpenLeap extensions | ✅ |
| TPL-PROD-AUI-OVR | `product/prod/screen-overrides/feature.aui.yaml` | Persona-gated zones, CUI picks, copy overrides | ✅ |
| TPL-PROD-BFF-PCFG | `product/prod/bff/productconfig.yaml` | Release artifact consumed by the BFF at runtime | ✅ |

**Example repo:** [`examples/product/acme-sales/`](../examples/product/acme-sales/)
shows a complete product drawing from SD, BP, FI, and PPS with the cross-suite
read-only rule applied to PPS and FI.

**Relationship to the consolidated `product-spec.md`:** the in-Elara
`product-spec.md` documents discovery (SS0–SS15, the bridge, and product
configuration narratives). The `prod.*` templates are its export shape —
machine-readable, reviewable, and validated by Decompose against the dev.spec
catalog.

---

## UVL Companion File Templates

These are referenced from within the human-readable templates above, not standalone.

| Template | Referenced By | Location |
|----------|-------------|----------|
| `platform/uvl/feature-leaf.uvl` | Feature Spec §9 | Feature variability declaration |
| `platform/uvl/feature-composition.uvl` | Feature Composition §2 | Composition node variability |
| `platform/uvl/suite-catalog.uvl` | Suite Spec §6 | Suite root UVL |
| `platform/uvl/platform-catalog.uvl` | Platform-wide | Platform root UVL |
| `product/uvl/product-config.uvl` | Product Spec §17 | Resolved product variability |

---

## YAML Companion File Templates

| Template | Referenced By | Location |
|----------|-------------|----------|
| `platform/yaml/screen-contract-aui.yaml` | Feature Spec §6 | AUI screen contract |
