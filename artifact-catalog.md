# OpenLeap SPLE Platform — Artifact Catalog

> **Authority:** CONCEPTUAL_STACK.md
> **Scope:** Defines every artifact of the SPLE platform — its fields, relationships, constraints, and format. No processes, no phases, no "how to create" — only what each artifact IS.

---

## 1. Overview

The platform has two spaces. Each space has artifacts. The spaces connect through **Feature Mapping** — the Product Space describes what is needed (via Business Capabilities, Processes, Personas), and maps those needs against the Platform Space's Feature Catalog.

```
PRODUCT SPACE (Application Engineering)        PLATFORM SPACE (Domain Engineering)
════════════════════════════════════════        ═══════════════════════════════════

Product (= Elara Project)                      Platform Feature Catalog
  │                                              │
  ├── Project Scope                              ├── Suite Spec
  ├── Business Processes                         │    ├── Domain / Service Spec
  │    ├── Process Steps                         │    │    ├── API Contract (OpenAPI)
  │    └── Gateways                              │    │    └── Event Contract (AsyncAPI)
  ├── Actors + Stakeholders                      │    ├── Platform-Features
  ├── Business Objects                           │    │    ├── Feature Model (UVL)
  ├── Business Events                            │    │    ├── Screen Contract (AUI)
  ├── Business Rules                             │    │    └── BFF Contract
  ├── Business Capabilities                      │    ├── Feature Compositions
  ├── Glossary Terms                             │    ├── Suite Feature Catalog + UVL
  ├── Quality Requirements                       │    └── Workflow Specs
  ├── External Interfaces                        │
  ├── KPIs                                       └── Platform Catalog (UVL root)
  ├── Functional Areas
  ├── Decisions
  ├── Workflow Candidates
  │
  ├── Personas
  │
  └── Feature Mapping ◄════matches against════► Platform Feature Catalog
       ├── Capability → Platform-Feature matches
       ├── Gap Analysis (unmatched capabilities)
       ├── Feature Selection (mode, attributes, extensions)
       └── BFF Configuration
```

---

## 2. Product Space Artifacts

A Product in the Product Space is equivalent to an Elara Project. It contains all artifacts that describe **what this customer needs**. The goal is to develop a precise picture of who wants what, and then map that against the Platform Catalog.

### 2.1 Product

The top-level container. Equivalent to an Elara Project.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Unique product identifier |
| name | string | ✓ | Human-readable product name |
| description | string | ✓ | What this product does, for whom |
| scope | ProjectScope | ✓ | Business problem, goals, in/out scope, constraints, assumptions |
| personas | Persona[] | ✓ | Who uses this product (min. 1) |
| processes | BusinessProcess[] | ✓ | How the business works (min. 1) |
| actors | Actor[] | ✓ | Who is involved |
| businessObjects | BusinessObject[] | | Things the business works with |
| businessEvents | BusinessEvent[] | | What happens in the business |
| businessRules | BusinessRule[] | | Constraints and policies |
| businessCapabilities | BusinessCapability[] | ✓ | What the business can do |
| glossaryTerms | GlossaryTerm[] | ✓ | Business vocabulary |
| qualityRequirements | QualityRequirement[] | | How well must it work |
| externalInterfaces | ExternalInterface[] | | What's around us |
| kpis | KPI[] | | How do we measure success |
| functionalAreas | FunctionalArea[] | | How does it cluster |
| decisions | Decision[] | | What did we decide and why |
| workflowCandidates | WorkflowCandidate[] | | Patterns flagged for Platform workflows |
| requiredCapabilities | ref[] | ✓ | BusinessCapability IDs this product requires → sent to Platform for resolution |
| featureResolution | FeatureResolution | | Returned by Platform (Telos Decompose) — see §3.x |
| bffConfiguration | BFFConfig | ✓ | How the BFF is configured (derived from resolution) |
| navigationArchitecture | NavigationMap | | How features are arranged in the UI |
| customServices | CustomServiceRef[] | | Customer-specific services outside the platform |

**Constraint:** A product MUST have at least one persona, one process, one capability, and a feature mapping.

**Existing source:** Elara Business Spec §4 (15 artifact types) + Elara Enhancement doc (Product, Persona) + `PRODUCT_CONFIG_SPEC_TEMPLATE.md`

### 2.2 Project Scope (embedded in Product)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| businessProblem | string | ✓ | Why does this product exist? What pain is being solved? |
| goals | Goal[] | ✓ | Measurable objectives with success criteria and priority |
| inScope | string[] | ✓ | What is included |
| outOfScope | string[] | | What is explicitly excluded |
| constraints | Constraint[] | | Budget, technology, regulatory, organizational limits |
| assumptions | string[] | | What we're taking for granted |

**Existing source:** Elara Spec §4.1, Freeze schema `project_scope`

### 2.3 Business Process (embedded in Product)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Process identifier |
| name | string | ✓ | Process name |
| description | string | ✓ | What this process does |
| level | enum | ✓ | L0 (overview), L1 (sub-process), L2 (detailed) |
| parentProcessId | ref | | For L1/L2: parent process |
| functionalAreaRef | ref | | Which functional area |
| triggerType | enum | ✓ | user_action, business_event, timer, external_signal |
| triggerDescription | string | | What specifically triggers it |
| frequency | string | | How often (e.g., "~20/month") |
| sla | string | | Expected turnaround time |
| happyPath | string | ✓ | Narrative of the main success scenario |
| exceptions | string | | Exception scenarios |
| outcome | string | ✓ | What is the business outcome |
| steps | ProcessStep[] | ✓ | Ordered activities with actor, inputs/outputs, linked rules |
| gateways | Gateway[] | | Decision points with conditions and branches |

**Existing source:** Elara Spec §4.2 Cat.1, Freeze schema `business_processes`

### 2.4 Actor (embedded in Product)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Actor identifier |
| name | string | ✓ | Role or organization name |
| type | enum | ✓ | internal, external, system |
| description | string | | What this actor does |
| responsibilities | string[] | ✓ | What they are responsible for |
| decisionAuthority | string | | What they can decide |
| stakeholder.role | string | | Stakeholder role |
| stakeholder.influence | enum | | high, medium, low |
| stakeholder.interests | string[] | | What they care about |
| stakeholder.informationNeeds | string[] | | What information they need |

**Existing source:** Elara Spec §4.2 Cat.2, Freeze schema `actors`

### 2.5 Business Object (embedded in Product)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Object identifier |
| name | string | ✓ | Business name (e.g., "Ausbildungsvertrag") |
| description | string | ✓ | What this thing is |
| attributes | Attribute[] | ✓ | Name, type, description |
| lifecycleStates | string[] | | Ordered states (e.g., draft → active → completed → archived) |
| ownerActorRef | ref | | Which actor owns this data |
| functionalAreaRef | ref | | Which area it belongs to |
| relatedObjects | ref[] | | References to other business objects |
| usedInProcesses | ref[] | | Which processes read/write this object |

**Existing source:** Elara Spec §4.2 Cat.3. ⚠️ NOT in Freeze schema — gap.

### 2.6 Business Capability (embedded in Product)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Capability identifier |
| name | string | ✓ | What the business can do (e.g., "Enroll Students") |
| description | string | ✓ | Independent of how it's done |
| type | enum | ✓ | core, supporting, generic |
| priority | enum | ✓ | high, medium, low |
| functionalAreaRef | ref | | Which area |
| fulfilledByProcesses | ref[] | | Which processes exercise this capability |
| requiresCapabilities | ref[] | | Other capabilities this depends on |
| governedByRules | ref[] | | Which rules constrain this capability |

**Existing source:** Elara Spec §4.2 Cat.5, Freeze schema `capabilities`

**KEY ROLE:** Business Capabilities are the primary input for Feature Mapping (§2.8). Each capability is matched against the Platform Feature Catalog.

### 2.7 Persona (embedded in Product)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Persona identifier |
| name | string | ✓ | Persona name (e.g., "Warehouse Worker") |
| actorRefs | ref[] | ✓ | Which actors this persona represents |
| iamRoles | string[] | ✓ | IAM role codes for authorization |
| jobToBeDone | string | ✓ | Primary job this persona performs |
| usageFrequency | enum | ✓ | DAILY, WEEKLY, OCCASIONAL, RARE |
| technicalComfort | enum | ✓ | LOW, MEDIUM, HIGH |
| painPoints | string[] | | Current frustrations |
| workarounds | string[] | | How they cope today |

**Existing source:** Elara Enhancement doc. ⚠️ NOT in Freeze schema — gap.

### 2.8 Required Capabilities — The Product's Input to the Platform

The product does NOT select platform-features directly. Instead, it declares **which Business Capabilities it requires**. The Platform (Telos Decompose) resolves these into concrete platform-features.

This separation is fundamental:
- **Product Space knows:** "We need the capability to enroll students, schedule lessons, and manage exams."
- **Platform Space knows:** "Enrolling students is served by F-SD-001 + F-BP-003. Scheduling lessons is served by F-PPS-007. Managing exams is a GAP — no platform-feature exists yet."

The product's `requiredCapabilities` field is a list of BusinessCapability IDs. The resolution happens on the Platform side (see §3.x Feature Resolution).

### 2.9 BFF Configuration (embedded in Product)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| deploymentModel | enum | ✓ | PER_PRODUCT, SHARED, FEATURE_MODULE |
| activeFeatures | string[] | ✓ | Feature IDs with mode ≠ EXCLUDED |
| featureGating | FeatureGate[] | | Per-feature access rules (derived from mode) |
| extensionRouting | ExtensionRoute[] | | Where extension point calls are routed |
| viewModelOverrides | map | | Per-feature view-model customizations |

**Derived from:** Feature Selection + Platform-Feature BFF Contracts

### 2.10 Remaining Product Artifacts

The following artifacts are fully defined in the Elara Business Specification and belong to the Product. Their field definitions are authoritative in that source.

| Artifact | Elara Spec Reference | Freeze Schema Status | Summary |
|----------|---------------------|---------------------|---------|
| Business Event | §4.2 Cat.3 | ⚠️ missing | What happens in the business — triggers and outcomes |
| Business Rule | §4.2 Cat.3 | ✅ `business_rules` | Constraints and policies with type, rationale, source |
| Glossary Term | §4.2 Cat.3 | ✅ `ubiquitous_language` | Business vocabulary — feeds into Suite UBL |
| Quality Requirement | §4.2 Cat.4 | ✅ `quality_requirements` | Non-functional expectations |
| External Interface | §4.2 Cat.4 | ⚠️ missing | System boundaries — other systems, agencies |
| KPI | §4.2 Cat.4 | ⚠️ missing | Measurable success indicators |
| Functional Area | §4.2 Cat.5 | ✅ `domains` (mapped) | Logical clustering — maps to Suites during decomposition |
| Decision | §4.2 Cat.5 | ✅ `decisions` | Important choices with rationale |
| Workflow Candidate | §4.2 Cat.5 | ⚠️ missing | Flagged for Platform Workflow Specs |

**Note:** These artifacts are not repeated in full here because their authoritative field definitions are in `elara_business_specification.md` §4 and §9.1 (MongoDB document structures). The Artifact Catalog references them; it does not duplicate them.

---

## 3. Platform Space Artifacts

### 3.0 Feature Resolution (Decompose Result)

Produced by the Platform (Telos Decompose) when a Product submits its Required Capabilities. The Platform resolves each capability against the Feature Catalog and returns a Feature Resolution.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| productRef | ref | ✓ | Which product this resolution is for |
| capabilityMappings | CapabilityMapping[] | ✓ | Per capability: matched, partial, or gap |
| featureSelection | FeatureSelection[] | ✓ | Resolved list of platform-features |
| gapAnalysis | Gap[] | | Capabilities with no or partial match |
| requiredConstraintsSatisfied | boolean | ✓ | All UVL `requires` constraints met? |
| validationErrors | string[] | | If constraints are violated |

**CapabilityMapping:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| capabilityRef | ref | ✓ | Reference to product's BusinessCapability |
| status | enum | ✓ | MATCHED, PARTIAL, GAP |
| matchedFeatures | string[] | | Platform-Feature IDs that fulfill this capability |
| suggestedMode | enum | | FULL, READ_ONLY, EMBEDDED |
| gapDescription | string | | If PARTIAL or GAP: what's missing |
| resolution | enum | | EXTEND_PLATFORM, CUSTOM_SERVICE, DEFER |

**FeatureSelection:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| featureId | string | ✓ | Platform-Feature ID (F-{SUITE}-{NNN}) |
| mode | enum | ✓ | FULL, READ_ONLY, EMBEDDED, EXCLUDED |
| attributeDefaults | map | | Platform default attribute values |
| extensionPointsAvailable | string[] | | Which extension points can be filled |

**Constraint:** The resolution MUST satisfy all UVL `requires` constraints. If F-SD-001 requires F-BP-001, the resolution MUST include both.

**Relationship:** Input: Product.requiredCapabilities. Output: Product.featureResolution (returned to Product Space for confirmation/override by PO).

**Existing source:** Agora Spec mentions "Decompose" as Telos function. ⚠️ No formal artifact definition exists — must be created.

### 3.1 Suite Spec

The top-level grouping artifact. A suite clusters domains that share a Ubiquitous Language.

**Constraint:** Every domain within a suite MUST share core terms with consistent semantics. When a term means something different, it signals a suite boundary.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| metadata.id | string | ✓ | Suite ID, pattern: `^[a-z]{2,4}$` (e.g., fi, pps, sd) |
| metadata.name | string | ✓ | Full name |
| metadata.version | semver | ✓ | Spec version |
| metadata.status | enum | ✓ | draft, active, deprecated |
| metadata.owner | Owner | | Team, email, slack |
| metadata.boundedContexts | string[] | | Authoritative list of BCs in this suite |
| purpose.businessPurpose | string | ✓ | What business problem does this suite solve? |
| purpose.inScope | string[] | ✓ | What belongs to this suite |
| purpose.outOfScope | string[] | | What explicitly does NOT belong |
| ubiquitousLanguage | GlossaryEntry[] | ✓ | THE central section — shared vocabulary |
| domainModel.coreConcepts | CoreConcept[] | ✓ | Key concepts with owners |
| domainModel.sharedKernel | SharedConcept[] | | Concepts shared across services |
| domainModel.boundedContextMap | ContextRelationship[] | | Intra-suite context relationships |
| serviceLandscape.services | ServiceEntry[] | ✓ | Services in this suite |
| integrationPatterns | IntegrationConfig | ✓ | Event-driven / orchestration / hybrid |
| eventConventions | EventConventions | ✓ | Routing key pattern, payload envelope, catalog |
| adrs | ADR[] | | Suite-level architecture decisions |
| crossCuttingConcerns | CrossCutting | | Compliance, security, multi-tenancy, audit |
| externalInterfaces | ExternalInterfaces | | Outbound/inbound to other suites |

**Relationships:**
- Contains: Domain/Service Specs, Platform-Features, Feature Catalog
- Referenced by: Platform Catalog UVL

**Format:** Markdown → JSON (roundtrip via Telos)

**Schema:** `suite-layer.schema.json` (649 lines, comprehensive)

**Existing template:** `SUITE_SPEC_TEMPLATE.md` — ⚠️ sparse, needs major rework to match schema

### 3.2 Domain / Service Spec

One bounded context, one microservice. Owns aggregates, invariants, API, events.

**Constraint:** One domain = one service (1:1). If a domain is too large, split it — don't add a second service.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| metadata.id | string | ✓ | Pattern: `^[a-z]+-[a-z]+-svc$` (e.g., fi-gl-svc) |
| metadata.suite | string | ✓ | Parent suite ID |
| metadata.domain | string | ✓ | Domain within suite |
| metadata.version | semver | ✓ | Spec version |
| metadata.status | enum | | draft, review, active, deprecated, retired |
| metadata.boundedContextRef | string | | Reference to suite BC |
| metadata.apiBasePath | string | | e.g., /api/fi/gl/v1 |
| scope.purpose | string | ✓ | What this service does |
| businessContext | BusinessContext | | Problems solved, stakeholders |
| domainModel.aggregates | Aggregate[] | ✓ | Consistency boundaries with root, entities, VOs |
| domainModel.enumerations | Enum[] | | Named enumerations |
| domainModel.sharedTypes | SharedType[] | | Reusable VOs across aggregates |
| businessRules.catalog | BusinessRule[] | ✓ | Rules with ID, enforcement, error code |
| contracts.restApi | RestAPI | | Base path, auth, endpoints |
| contracts.events | Events | | Outbound + inbound events |
| integration | Integration | | Dependencies, pattern decision |
| dataModel | DataModel | | Tables, indexes, reference data |
| security | Security | | Data classification, access control, compliance |
| adrs | ADR[] | | Service-specific decisions |
| glossary | GlossaryEntry[] | | Service-local terms |

**Relationships:**
- Belongs to: Suite Spec
- Produces: API Contracts (OpenAPI), Event Contracts (AsyncAPI)
- Referenced by: Platform-Feature Spec §5 (backend dependencies)

**Format:** Markdown → JSON (roundtrip via Telos)

**Schema:** `service-layer.schema.json` (775 lines, comprehensive)

**Existing template:** `DOMAIN_SPEC_TEMPLATE.md` — ✅ solid, needs minor Stack alignment

### 3.3 API Contract

**Format:** OpenAPI YAML. **Derived from:** Domain/Service Spec contracts.restApi section.

**Convention:** `contracts/http/<suite>/<domain>/openapi.yaml`

**Constraint:** MUST include vendor extensions: `x-owner`, `x-suite`, `x-domain`, `x-service`

### 3.4 Event Contract

**Format:** AsyncAPI / JSON Schema. **Derived from:** Domain/Service Spec contracts.events section.

**Convention:** `contracts/events/<suite>/<domain>/<aggregate>.<event>.schema.json`

### 3.5 Platform-Feature Spec

The central artifact bridging Platform and Product spaces. A complete specification of a user-facing capability with four integrated parts.

**Constraint:** A feature MUST belong to exactly one suite (the suite whose UBL it primarily uses). Cross-suite reads create `requires` constraints.

A platform-feature consists of **four companion artifacts** (3.5, 3.6, 3.7, 3.8) that are maintained together:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: F-{SUITE}-{NNN} or F-{SUITE}-{NNN}-{NN} |
| suite | string | ✓ | Owning suite ID |
| name | string | ✓ | Feature name |
| status | enum | ✓ | planned, draft, review, approved, live, deprecated |
| §0 orientation | Orientation | ✓ | Summary, non-goals, entry/exit points |
| §1 userGoal | UserGoal | ✓ | Goal statement, 2–5 scenarios |
| §2 userJourney | Journey | ✓ | Mermaid sequence, screen layouts → **feeds AUI (3.7)** |
| §3 interactionRequirements | Interaction | ✓ | Form fields, rules, actions → **feeds AUI zones (3.7)** |
| §4 edgeCases | EdgeCases | ✓ | Screen states (loading, empty, error, populated) → **feeds AUI absent-rules (3.7)** |
| §5 backendDependencies | BackendDep[] | ✓ | Service calls with tier, isMutation flag → **feeds BFF contract (3.8)** |
| §5.2 viewModelShape | JSON-C | ✓ | BFF response shape → **IS the BFF output contract (3.8)** |
| §6 i18nAndPermissions | I18nPerm | | Translation keys, role-based visibility |
| §7 acceptanceCriteria | AC[] | ✓ | Given/When/Then statements |
| §8 dependencies | Dependencies | ✓ | Required features (UVL `requires`), variability points, extension points |

**Relationships:**
- Belongs to: Suite (via suite field)
- References: Domain/Service Specs (via §5 backendDependencies)
- Produces: Feature Model UVL (3.6), Screen Contract AUI (3.7), BFF Contract (3.8)
- Referenced by: Product (via featureSelections)
- Listed in: Suite Feature Catalog

**Format:** Markdown

**Existing template:** `LEAF_FEATURE_SPEC_TEMPLATE.md` — ✅ good (90%), needs AUI/BFF/extension point alignment

### 3.6 Feature Model (UVL)

Machine-readable variability declaration. Companion to Feature Spec.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| namespace | string | ✓ | Feature ID |
| imports | string[] | | Suite catalog imports |
| features | FeatureTree | ✓ | Position, group type (mandatory/optional/alternative/or) |
| attributes | Attribute[] | | With type, default, bindingTime (compile/deploy/runtime) |
| constraints | Constraint[] | ✓ | `requires` (cross-suite deps), `excludes` |

**Constraint:** Every cross-suite backend dependency in Feature Spec §5 MUST have a corresponding `requires` constraint here.

**Format:** UVL

**Existing template:** `LEAF_FEATURE_TEMPLATE.uvl` — needs `requires` derivation rules

### 3.7 Screen Contract (AUI)

Platform-free UI specification. Derived from Feature Spec §2–§4.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| featureId | string | ✓ | Parent feature ID |
| taskModel | TaskModel | ✓ | Operators: sequential, concurrent, alternative, optional, enabling |
| zones | Zone[] | ✓ | Named regions, typed: fixed, variable, feature-gated. With priorities. |
| absentRules | AbsentRule[] | | What happens when optional zones are absent |
| dataBindings | DataBinding[] | ✓ | Which zones bind to which view-model fields |
| businessRules | ScreenRule[] | | Role gates, state conditions, action consequences |
| extensionZones | ExtensionZone[] | | Defined hooks for product-level UI customization |

**Constraint:** Every form field in Feature Spec §3 MUST map to a zone. Every edge case in §4 MUST map to an absent-rule or screen state.

**Format:** YAML

**Existing source:** UI-SPLE `ui-sple.md` §7–§9 defines the concepts. ⚠️ No template exists.

### 3.8 BFF Contract

Specifies how the BFF serves this feature. Derived from Feature Spec §5 + §5.2.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| featureId | string | ✓ | Parent feature ID |
| aggregatedServices | ServiceCall[] | ✓ | Which domain services are called (from §5) |
| viewModelShape | JSON-C | ✓ | BFF response structure (from §5.2) |
| featureGatingRules | GatingRule[] | ✓ | Behavior per mode: full → all calls, read-only → reads only, excluded → no calls |
| extensionRouting | ExtensionRoute[] | | Where extension calls are routed |
| failureModes | FailureMode[] | | What happens when a service is unavailable |
| cachingHints | CacheHint[] | | Which responses can be cached |

**Constraint:** Every service call in Feature Spec §5 MUST appear here. The viewModelShape MUST match §5.2.

**Format:** Markdown

**Existing source:** ⚠️ No template. Feature Spec §5 + §5.2 contain the data, but the BFF perspective is not formalized.

### 3.9 Feature Composition Spec

Groups features into capability hierarchies (composition nodes in the UVL tree).

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | F-{SUITE}-{NNN} (composition node) |
| name | string | ✓ | Capability name |
| children | ChildEntry[] | ✓ | Child features/nodes with group type |
| variabilityStructure | prose | ✓ | Why this grouping, business rationale |
| constraints | Constraint[] | | Intra-node constraints |
| validConfigurations | Example[] | | Example valid selections |
| invalidConfigurations | Example[] | | Example invalid selections with explanation |

**Format:** Markdown + UVL

**Existing template:** `COMPOSITION_NODE_SPEC_TEMPLATE.md` + `COMPOSITION_NODE_TEMPLATE.uvl`

### 3.10 Suite Feature Catalog

Lists all platform-features belonging to a suite. The "shelf" that products select from.

**Format:** Markdown + UVL

**Existing template:** `SUITE_UI_CATALOG_TEMPLATE.md` + `SUITE_CATALOG_TEMPLATE.uvl`

### 3.11 Workflow Spec

Non-actor processes: batch, saga, orchestration, ETL, scheduled jobs.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: wf-{name} |
| type | enum | ✓ | batch, saga, orchestration, etl, scheduled_job, integration |
| suite | string | ✓ | Owning suite |
| trigger | Trigger | ✓ | cron, event, API, manual |
| steps | Step[] | ✓ | Ordered: action, service ref, compensation, retry, timeout |
| retryPolicy | RetryPolicy | | Max attempts, backoff, max interval |
| compensationStrategy | enum | | backward_recovery, forward_recovery, none |
| referencedServices | ServiceRef[] | ✓ | Service IDs with role |
| observability | Observability | | Metrics, alerts |

**Format:** Markdown

**Existing source:** Agora Spec §5.3.4. ⚠️ No template exists.

### 3.12 Platform Catalog (UVL Root)

Root of the variability tree. Imports all suite catalogs.

**Format:** UVL

**Existing template:** `PLATFORM_CATALOG_TEMPLATE.uvl` — ✅ good

### 3.13 Microservice README

Standard README for implementation repositories. Covers operational and developer-facing concerns (build, run, configure, deploy, troubleshoot) — references the Domain/Service Spec for domain model and business rules rather than duplicating them.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| meta | Meta | ✓ | Suite, domain, service ID, tier, base package, API path, port, status |
| serviceOverview | Section | ✓ | Purpose, key capabilities, architecture context |
| prerequisites | Section | ✓ | Runtime dependencies, OpenLeap dependencies, environment setup |
| gettingStarted | Section | ✓ | Clone & build, run locally, verify, run tests |
| configuration | Section | ✓ | Spring profiles, key properties, env vars, feature flags |
| projectStructure | Section | ✓ | DDD package layout, key packages, important files |
| apiOverview | Section | ✓ | REST endpoints summary, command ingress, OpenAPI link |
| eventsMessaging | Section | ✓ | Published/consumed events, exchange config, outbox pattern |
| database | Section | | Schema overview (link to spec), migrations, seed data |
| operations | Section | ✓ | Health, metrics, logging, deployment |
| troubleshooting | Section | | Common issues, debugging tips |
| relatedResources | Section | ✓ | Spec link, guidelines link, related services, team |

**Format:** Markdown

**Lives in:** Implementation repository root (`README.md`)

**Relationship:** References Domain/Service Spec (§3.2); referenced by developers.

**Existing template:** `readme-microservice.md` — ✅ good

---

## 4. Gap Summary

| Artifact | Existing Definition | Template/Schema | Gap |
|----------|--------------------|-----------------|----|
| Product (incl. all Elara artifacts) | Elara Spec §4 + Enhancement | `PRODUCT_CONFIG_SPEC_TEMPLATE.md` | ⚠️ Missing: required capabilities as input to Platform, BFF config, extensions. Freeze schema missing: BusinessObject, BusinessEvent, ExternalInterface, KPI, WorkflowCandidate, Persona, Product |
| **Feature Resolution** | Agora Spec "Decompose" concept | **None** | ❌ Must create — the artifact produced when Platform resolves Product's capabilities into features |
| Suite Spec | Agora Spec §5.3.1 | `suite-layer.schema.json` ✅ + template ⚠️ sparse | Template needs major rework to match schema |
| Domain/Service Spec | Agora Spec §5.3.2 | `service-layer.schema.json` ✅ + template ✅ | Minor alignment |
| Platform-Feature Spec | Agora Spec §5.3.3 | `LEAF_FEATURE_SPEC_TEMPLATE.md` ✅ | Needs AUI/BFF/extension integration |
| Feature Model UVL | UI-SPLE | Template ✅ | Needs `requires` derivation rules |
| **Screen Contract AUI** | UI-SPLE concepts | **None** | ❌ Must create |
| **BFF Contract** | Conceptual Stack §4.6 | **None** | ❌ Must create |
| Feature Composition | UI-SPLE | Templates ✅ | Minor alignment |
| Suite Feature Catalog | UI-SPLE | Templates ✅ | Minor alignment |
| **Workflow Spec** | Agora Spec §5.3.4 | **None** | ❌ Must create |
| Platform Catalog UVL | UI-SPLE | Template ✅ | Minor |

**Summary:**
- **4 artifacts must be created:** Feature Resolution, Screen Contract AUI, BFF Contract, Workflow Spec
- **~5 need alignment** to Conceptual Stack
- **7 Freeze schema fields missing** (Product-side artifacts not yet in freeze format)
- **Rest is solid** — existing schemas and templates cover it
