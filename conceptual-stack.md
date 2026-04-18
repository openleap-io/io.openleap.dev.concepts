# OpenLeap SPLE Platform — Conceptual Stack

> **Status:** Draft — all open decisions resolved (see §9)
> **Purpose:** This is the authoritative top-down model for the OpenLeap platform. All other documents (specs, contracts, implementations) derive from this model.
> **Principle:** Specifications define what implementations follow. Never the reverse.

---

## 1. Foundations — SPLE and the Platform/Product Divide

### 1.1 Software Product Line Engineering (SPLE)

OpenLeap is a **Software Product Line** (SPL) as defined by Clements & Northrop (2001): *"a set of software-intensive systems sharing a common, managed set of features that satisfy the specific needs of a particular market segment and that are developed from a common set of core assets in a prescribed way."*

The platform follows the two-lifecycle model established in SPLE literature (Pohl, Böckle & van der Linden, 2005):

| SPLE Lifecycle | OpenLeap Mapping | Responsibility | Tool |
|---------------|------------------|---------------|------|
| **Domain Engineering** | Platform Space | Build reusable assets (services, features, components) | Telos |
| **Application Engineering** | Product Space | Derive specific products by selecting and configuring assets | Elara |

This is not an implementation detail — it is the **fundamental architectural principle** of the platform.

### 1.2 The Two Spaces

```
╔═══════════════════════════════════════════════════════════════════╗
║  PLATFORM SPACE (Domain Engineering / Telos / Solution Space)     ║
║                                                                   ║
║  "What can the platform do?"                                      ║
║                                                                   ║
║  Assets:                                                          ║
║  · Suites (vocabulary-bounded domain clusters)                    ║
║  · Domains (bounded contexts)                                     ║
║  · Services (deployable microservices)                            ║
║  · Platform-Features (reusable capabilities with variability)     ║
║  · Contracts (OpenAPI, AsyncAPI, JSON Schema)                     ║
║  · Screen Contracts (AUI/CUI — integral part of features)        ║
║  · Extension Points (defined hooks for product customization)     ║
║                                                                   ║
║  Principle: Build for reuse. Declare variability. Define          ║
║  extension points. Specify, don't implement for one customer.     ║
╠═══════════════════════════════════════════════════════════════════╣
║  PRODUCT SPACE (Application Engineering / Elara / Problem Space)  ║
║                                                                   ║
║  "What does this customer need?"                                  ║
║                                                                   ║
║  Assets:                                                          ║
║  · Products (composed UI applications)                            ║
║  · Product-Features (configured instances of platform-features)   ║
║  · Personas (customer-specific user profiles)                     ║
║  · Processes (customer-specific business workflows)               ║
║  · Custom Services (customer-specific, outside the platform)      ║
║  · Variability Resolutions (attribute values, feature selections) ║
║                                                                   ║
║  Principle: Select, configure, extend. Resolve variability.       ║
║  Don't rebuild what the platform provides.                        ║
╚═══════════════════════════════════════════════════════════════════╝
```

### 1.3 Scientific and Industry Foundations

The OpenLeap platform model draws from established research and standards:

| Concept | Source | How OpenLeap Uses It |
|---------|--------|---------------------|
| Software Product Lines | Pohl, Böckle & van der Linden: *Software Product Line Engineering* (2005) | Two-lifecycle model: Domain Engineering / Application Engineering |
| Feature-Oriented Domain Analysis (FODA) | Kang et al. (1990), SEI/CMU | Feature models as the central variability mechanism |
| Universal Variability Language (UVL) | Sundermann et al. (2021), MODEVAR community | Machine-readable feature models with attributes and constraints |
| Cameleon Reference Framework (CRF) | Calvary et al. (2003) | AUI/CUI split for multi-platform UI derivation |
| Domain-Driven Design (DDD) | Evans (2003): *Domain-Driven Design* | Bounded contexts, ubiquitous language, aggregates |
| Bounded Context as Suite Boundary | Vernon (2013): *Implementing DDD* | UBL-based clustering of related bounded contexts |
| CQRS + Event Sourcing patterns | Young (2010), Fowler (2011) | Command/Query separation, thin domain events, outbox pattern |
| Microservice architecture | Newman (2015): *Building Microservices* | One service per bounded context, independent deployability |
| 4-Tier service topology | OpenLeap-specific (aligned with layered architecture patterns) | Dependency governance: T1 → T2 → T3 → T4 |

---

## 2. The Conceptual Layers

The platform is specified through seven layers, organized in two spaces with a mediating layer between them.

```
PRODUCT SPACE (Application Engineering)
┌─────────────────────────────────────────────────────────┐
│  PRODUCT                                                 │
│  "What does the end user interact with?"                 │
│  A composed application serving personas.                │
│  Selects and configures platform-features.               │
├─────────────────────────────────────────────────────────┤
│  PRODUCT-FEATURE                                         │
│  "What does this customer get?"                          │
│  A configured instance of a platform-feature.            │
│  Variability resolved. Extension points filled.          │
├─────────────────────────────────────────────────────────┤
│  BFF (Backend for Frontend)                              │
│  "How does the product talk to the platform?"            │
│  Translates product-feature needs into service calls.    │
│  Aggregates view models. Enforces product configuration. │
│  THE bridge between product-specific and platform-agnostic│
└─────────────────────────────────────────────────────────┘

PLATFORM SPACE (Domain Engineering)
┌─────────────────────────────────────────────────────────┐
│  PLATFORM-FEATURE                                        │
│  "What can the platform do?"                             │
│  Hierarchical: Capability → Use-Case → Screen.           │
│  Declares variability. Defines extension points.         │
├─────────────────────────────────────────────────────────┤
│  SUITE                                                   │
│  "Which domains share a language?"                       │
│  A cluster of domains bound by a shared UBL.             │
│  Defines the vocabulary boundary.                        │
├─────────────────────────────────────────────────────────┤
│  DOMAIN                                                  │
│  "What is a single bounded context?"                     │
│  One coherent subdomain within a suite.                  │
│  Owns aggregates, invariants, and domain events.         │
├─────────────────────────────────────────────────────────┤
│  SERVICE                                                 │
│  "What is deployed and operated?"                        │
│  A deployable microservice implementing one domain.      │
│  Has its own API, data store, and event contracts.       │
├─────────────────────────────────────────────────────────┤
│  TIER                                                    │
│  "Where does this run and what can it depend on?"        │
│  Deployment governance: T1 → T2 → T3 → T4.              │
│  Dependency direction: higher tiers depend on lower.     │
└─────────────────────────────────────────────────────────┘
```

**Reading direction:** Top-down is the specification direction. Bottom-up is the dependency direction.

---

## 3. Platform Space — Domain Engineering

### 3.1 Suite — The Vocabulary Boundary

A **Suite** is a cluster of domains that share a common **Ubiquitous Language (UBL)** (Evans, 2003).

> **Suite boundary = UBL boundary.**

The Ubiquitous Language is the set of terms, definitions, and relationships that domain experts and developers use to communicate about a business area. When two domains use the same term with the same meaning, they belong to the same suite. When two domains use the same term with different meanings, they belong to different suites.

**Formal criteria:**
1. **Shared terms:** Domains within a suite share core terms (e.g., "Product", "BOM", "Routing" in PPS)
2. **Consistent semantics:** Those shared terms carry identical meaning across all domains in the suite
3. **Translation at boundaries:** When a term crosses a suite boundary, it must be explicitly translated (Anti-Corruption Layer)

**Consequences of shared UBL (same suite):**
- Shared event namespace: `<suite>.<domain>.events`
- Shared API path prefix: `/api/<suite>/<domain>/v1`
- Aggregate references by ID without translation
- Features spanning these domains are natural

**Consequences of different UBL (different suites):**
- Communication requires explicit translation
- Cross-suite references use foreign IDs with documented meaning
- Features spanning these domains require careful scoping

**Example — PPS as one suite:**

| Term | Shared Meaning Across All PPS Domains |
|------|---------------------------------------|
| Product | A manufactured or procured item with a BOM and routing |
| Material | A tangible item tracked by stock quantity and location |
| BOM | Hierarchical list of materials needed to produce a product |
| Work Order | An instruction to produce a quantity of a product |

All PPS domains (pd, mrp, im, wm, pur, mes, qm) understand these terms identically.

**Example — FI as a separate suite:**

| Term | Meaning in FI (DIFFERENT from PPS) |
|------|-----------------------------------|
| Account | A ledger position (not a user account) |
| Document | A financial document (not a DMS document) |
| Period | A fiscal period (not a production period) |

When PPS posts a goods receipt, FI sees a "material valuation posting" — same event, translated vocabulary.

### 3.2 Domain — The Bounded Context

A **Domain** is a bounded context (Evans, 2003) within a suite. It owns one coherent area of business logic.

- Uses the suite's UBL but focuses on a subset
- Owns its own aggregates (consistency boundaries)
- Defines its own invariants (always-true business rules)
- Publishes domain events (facts about what happened)
- Maps 1:1 to a service

### 3.3 Service — The Deployable Unit

A **Service** is a deployable microservice implementing exactly one domain.

- Own data store: PostgreSQL schema `<suite>_<domain>`
- Own API: REST at `/api/<suite>/<domain>/v1`
- Own event exchange: `<suite>.<domain>.events`
- Own deployment lifecycle: independent releases

Internal architecture follows a 4-layer pattern: API → Application → Domain → Infrastructure.

### 3.4 Tier — Deployment Governance

A **Tier** governs what a service is allowed to depend on.

```
T1 — Platform & Technical Foundations (IAM, ref, i18n, si, dms, rpt)
T2 — Shared Enterprise Business (bp, cal)
T3 — Core Business Suites (PPS, FI, SD, HR, PS domains)
T4 — Data, Analytics & Integration (BI, ETL)

Dependency direction: T4 → T3 → T2 → T1 (higher depends on lower, never reverse)
```

### 3.5 Platform-Feature — The Reusable Capability

A **Platform-Feature** is a reusable, variability-bearing capability in the platform catalog. It is the central artifact that bridges the Platform and Product spaces. A platform-feature is a **complete specification** — it defines what the user does, what they see, how it varies, what the backend provides, and where products can extend it.

**Hierarchy** (following FODA principles, Kang et al., 1990):

```
Capability (Composition Node in UVL tree)
│   "What can the system do in this area?"
│   e.g., "Inventory Management", "Order Processing"
│
├── Use-Case (Composition Node or Leaf)
│   "What can the user accomplish?"
│   e.g., "Post Goods Receipt", "Create Sales Order"
│
└── Screen (Leaf)
    "What does the user see and interact with?"
    e.g., "Goods Receipt Form", "Order Line Entry"
```

**Identity:** `F-{SUITE}-{NNN}` (composition node) or `F-{SUITE}-{NNN}-{NN}` (leaf). The suite prefix indicates ownership — the suite whose UBL the feature primarily uses.

#### 3.5.1 Feature Specification — The Four Parts

A platform-feature is not one document but **four integrated artifacts** managed together in Telos:

```
Platform-Feature F-{SUITE}-{NNN}
├── Feature Spec (.md)          — What the feature does (human-readable)
├── Feature Model (.uvl)        — How the feature varies (machine-readable)
├── Screen Contract — AUI       — What the user sees, platform-free (YAML)
└── Screen Contract — CUI(s)    — Platform-specific rendering (per target)
```

These are not independent deliverables. They are **four views of the same feature**, maintained in sync:

**1. Feature Spec (.md) — The Comprehensive Description**

| Section | Space | Content | Stability |
|---------|-------|---------|-----------|
| §0 Metadata | — | ID, suite, name, status | Stable |
| §1 User Goal & Scenarios | Problem | What the user wants to accomplish | High |
| §2 User Journey | Problem | Sequence diagram (happy path), screen layouts | High |
| §3 Interaction Requirements | Problem | Form fields, cross-field rules, actions | High |
| §4 Edge Cases | Problem | Screen states: loading, empty, error, populated | High |
| §5 Backend Dependencies | Solution | Service calls (tier, method, endpoint, failure mode) | Medium |
| §6 i18n & Permissions | Solution | Translation keys, role-based visibility | Medium |
| §7 Acceptance Criteria | Both | Given/When/Then testable statements | High |
| §8 Dependencies & Variability | Both | Required features, variability points, extension points | High |

**2. Feature Model (.uvl) — The Variability Declaration**

- Position in the feature tree (parent, siblings)
- Group type: `mandatory`, `optional`, `alternative` (XOR), `or` (≥1)
- Attributes with binding times: `compile`, `deploy`, `runtime`
- Cross-tree constraints: `requires`, `excludes`

**3. Screen Contract — AUI (Abstract UI)**

The AUI is the **platform-free UI specification** derived from the Feature Spec. It applies the Cameleon Reference Framework (Calvary et al., 2003) to separate what a screen IS from how it RENDERS.

The AUI defines:
- **Task Model:** User tasks and their temporal relationships, using five operators:
  - `sequential(A, B)` — A must complete before B starts
  - `concurrent(A, B)` — A and B are co-accessible (side-by-side or tabs)
  - `alternative(A | B)` — mutual exclusion (one or the other)
  - `optional(A)` — A may be absent (triggers absent-rule)
  - `enabling(B ← A)` — B is disabled until A provides data
- **Zones:** Named layout regions with priorities (not pixel sizes), typed as:
  - `fixed` — owned by the platform shell, cannot be overridden
  - `variable` — product team integration slots
  - `feature-gated` — visible only when a feature flag is active
- **Absent-Rules:** What happens when an optional zone is not rendered:
  - `collapse-up` — predecessor expands to fill the space
  - `end-page` — page ends at previous zone
  - `tab-hidden` — tab removed from navigation
  - `panel-section-hidden` — panel section collapses
- **Business Rules:** Role gates, state conditions, action consequences
- **Data Bindings:** Which zones bind to which view-model fields

**Derivation:** The AUI is derived from the Feature Spec:
- §2 (User Journey) → Task Model operators
- §3 (Form Fields + Actions) → Zones
- §4 (Edge Cases) → Absent-Rules + screen state definitions
- §8 (Variability) → Feature-gated zones

**4. Screen Contract — CUI(s) (Concrete UI)**

A CUI is a **platform-specific rendering** of the AUI. Each target platform (web, mobile, desktop) has its own CUI that interprets the AUI:

| AUI Concept | Web CUI | Mobile CUI | Desktop CUI |
|------------|---------|------------|-------------|
| `sequential(A, B)` | Vertical stack | Wizard steps | Top-to-bottom panels |
| `concurrent(A, B)` | Side-by-side columns | Tabs/swipe | Split pane |
| `alternative(A \| B)` | Radio/toggle switch | Segmented control | Tab bar |
| `optional(A)` | Collapsible section | Hidden by default | Collapsible panel |
| Zone priorities | Responsive breakpoints | Touch-optimized order | Keyboard-first layout |

One AUI, multiple CUIs. The AUI is the **stable contract** between platform specification and platform-specific implementation.

#### 3.5.2 Extension Points

Platform-features define explicit hooks where product-level customization is allowed:
- **Extension zones** in Screen Contracts (additional form sections, custom panels)
- **Extension rule slots** (custom validation rules beyond the platform's rules)
- **Extension action slots** (custom buttons, menu items)
- **Extension event hooks** (custom handlers for domain events)

Extension points are **declared in the platform**, not invented by products. This follows the Open-Closed Principle (Meyer, 1988): platform assets are open for extension but closed for modification.

### 3.6 Extension Points — Platform Hooks for Product Customization

Extension points are a first-class concept in the platform. They are defined at multiple levels:

| Level | Extension Point Type | Example |
|-------|---------------------|---------|
| Feature | Extension zones, fields, rules, actions | "Add custom fields to the Goods Receipt form" |
| Domain | Extension events, aggregate hooks | "React to custom events on the Product aggregate" |
| Service | Extension API endpoints, message handlers | "Add a custom endpoint to the PPS-PD service" |

Extension points follow the **Open-Closed Principle** (Meyer, 1988): Platform assets are open for extension but closed for modification. Products extend at defined points; they do not fork or modify platform code.

---

## 4. Product Space — Application Engineering

### 4.1 Product — The Composed Application

A **Product** is a composed UI application that serves one or more personas for a specific customer or market segment.

**Products are independent of suites.** A product typically includes features from multiple suites. "Sales Workbench" includes features from SD, BP, FI, and PPS.

**Product composition is customer-driven** (Application Engineering): The product owner or business analyst selects features from the platform catalog based on the customer's processes and personas. This selection happens in Elara.

**Persistence — the `prod.*` repo convention.** Elara is the authoring UI. The resulting product specification is persisted to a dedicated Git repository named `io.openleap.prod.{product}`, authored against the templates under [`templates/product/prod/`](templates/product/prod/) and documented in [dev.hub architecture/product-repo-layout.md](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md). The repo holds a product manifest (`product.yaml`), feature selection (`features/selection.uvl`), variability bindings (`variability/bindings.yaml`), extension fills (`extensions/{F-ID}/*`), exported personas and BPMN processes, screen overrides, and the release artifact `bff/productconfig.yaml` consumed by the BFF at runtime. A companion `prod.{product}.ui` repo holds the frontend, and an optional `prod.{product}.bff` holds a dedicated BFF.

### 4.2 Product-Feature — The Configured Instance

A **Product-Feature** is a configured instance of a platform-feature. It represents "what this specific customer gets" for a given platform capability.

A product-feature consists of:
1. **Base:** Reference to the platform-feature (`F-{SUITE}-{NNN}`)
2. **Configuration:** Resolved variability (attribute values set, optional features decided)
3. **Extensions:** Filled extension points (customer-specific fields, rules, actions)

A product-feature may NOT:
- Modify the platform-feature's core logic
- Add functionality outside defined extension points
- Change the platform-feature's backend dependency structure

### 4.3 Custom Services — Outside the Platform

Some customer requirements are so specific that they do not belong in the platform catalog. For these, **custom services** can be built:

- Custom services are NOT platform assets — they live outside the platform's suite/domain structure
- They may consume platform services (via REST/events) but are not consumed by platform services
- They follow the same technical patterns (Spring Boot, CQRS) but are not governed by platform specs
- They are owned by the customer/project, not by the platform team

**When to create a custom service vs. extending the platform:**
- If the capability is reusable across customers → platform-feature with extension points
- If the capability is unique to one customer and unlikely to be reused → custom service

### 4.4 Persona — The User Profile

A **Persona** is a UX-enriched user profile derived from business actors discovered in Elara. It adds:
- IAM roles and permissions
- Job-to-be-done (primary tasks)
- Usage frequency (daily, weekly, occasional)
- Technical comfort level
- Pain points and workarounds

Personas drive product composition: which features does this persona need?

### 4.5 Process — The Customer Workflow

A **Process** is a customer-specific business workflow discovered and modeled in Elara (BPMN). Processes inform feature selection: which features support the steps in this process?

### 4.6 BFF — The Product-Platform Bridge

The **Backend for Frontend (BFF)** is the runtime layer that connects product-specific frontends to platform-agnostic backend services. It sits at the boundary between Product Space and Platform Space.

#### 4.6.1 Why the BFF Exists

Backend services are platform assets — they speak suite/domain language, serve any product, and know nothing about specific product configurations. Frontends are products — they speak feature/persona language and need product-specific view models. Something must translate between these two worlds:

```
Frontend (Product)                 BFF                        Backend Services (Platform)
──────────────────                 ───                        ──────────────────────────
Speaks: features, screens,         Translates:                Speaks: aggregates, events,
personas, product config           product → platform          domains, suites

"Show me the goods receipt         "Call pps-im for stock,     GET /api/pps/im/v1/stock
 form for warehouse worker         call pps-pd for product     GET /api/pps/pd/v1/products
 persona with custom fields"       details, merge into         GET /api/shared/bp/v1/parties
                                   view model, include
                                   extension fields"
```

#### 4.6.2 What the BFF Does

| Responsibility | Description |
|---------------|-------------|
| **View Model Composition** | Aggregates data from multiple domain services into a single response shaped for a specific screen. A "Goods Receipt" screen may need data from `pps-im` (stock), `pps-pd` (product details), `bp` (supplier), and `ref` (units) — the BFF composes this into one view model. |
| **Product-Feature → Platform-Feature Mapping** | The BFF knows which platform-features are active for this product, which variability has been resolved, and which extension points are filled. It translates product-feature requests into the correct platform service calls. |
| **Feature-Gating** | If a product-feature is configured as `read-only`, the BFF strips write endpoints from the API. If an optional feature is `excluded`, the BFF doesn't call its backend services at all. The frontend doesn't need to know about platform-level feature configuration — the BFF enforces it. |
| **Extension Point Execution** | When a product has filled extension points (custom fields, custom rules), the BFF routes those to the appropriate handlers — whether platform extension APIs or custom services. |
| **Cross-Suite Aggregation** | A product-feature may need data from multiple suites. The BFF handles this aggregation, keeping the frontend ignorant of suite boundaries. The frontend asks for "the data I need for this screen" — the BFF knows which suites to call. |
| **Security Context Translation** | The BFF translates product-level persona/role information into platform-level IAM calls. The frontend says "I'm a warehouse worker" — the BFF translates this into the correct T1 IAM permissions check. |

#### 4.6.3 BFF Specification

Each platform-feature's **Feature Spec §5 (Backend Dependencies)** defines what the BFF must orchestrate. The §5.2 **View-Model Shape** is the BFF's output contract — it defines what the frontend receives.

```
Feature Spec §5 (Backend Dependencies)
├── Service calls: which domain services, which endpoints, which tier
├── Aggregation rules: how responses are merged into a view model
├── Failure modes: what happens if a service is unavailable
└── isMutation flag: read vs. write (affects feature-gating)

Feature Spec §5.2 (View-Model Shape)
└── JSON-C example of what the BFF returns to the frontend
    This IS the BFF response contract.
```

The BFF is therefore **not a free-form integration layer**. Its behavior is specified by the platform-features it serves. The Feature Spec §5 + §5.2 together define the BFF contract for each feature.

#### 4.6.4 BFF and Product Configuration

At runtime, a BFF instance is configured by the **ProductConfig**:

```
ProductConfig (from Elara)
├── Active features: [F-PPS-012 (full), F-BP-001 (full), F-FI-002 (read-only)]
├── Resolved attributes: { pagination.pageSize: 25, currency.default: "EUR" }
├── Extension point mappings: { F-PPS-012.extension.customFields: "customer-x-fields-service" }
│
▼ loaded by BFF at startup/deploy
│
BFF behavior for this product:
├── F-PPS-012 "Post Goods Receipt": full access → calls pps-im (write), pps-pd (read), bp (read)
├── F-BP-001 "Customer Search": full access → calls bp (read+write)
├── F-FI-002 "Invoice Overview": read-only → calls fi-ap (read ONLY, write endpoints blocked)
├── Attribute: pagination.pageSize = 25 → passed to all list queries
└── Extension: F-PPS-012 custom fields → additional call to customer-x-fields-service
```

This is where the **Product-Feature → Platform-Feature mapping materializes at runtime**. The BFF is the execution engine for product configuration.

#### 4.6.5 BFF Deployment Models

The BFF deployment model depends on the platform's operating context:

| Model | Description | When to Use |
|-------|-------------|-------------|
| **Per-Product BFF** | One BFF instance per product. Configured with that product's features and extensions. | **Default.** On-premise / dedicated customer deployments. Clean separation, independent scaling, simple configuration. |
| **Shared BFF (SaaS)** | One BFF serving multiple products/subscriptions. Product context resolved dynamically per request (subscription ID in token/header). | SaaS platform where multiple customers share infrastructure. BFF configuration is loaded per subscription. |
| **Feature-Module BFF (SaaS, advanced)** | BFF composed from feature-specific modules, dynamically assembled per subscription. | SaaS platform with high feature variability across subscriptions. Most dynamic, most complex. |

**Default recommendation: Per-Product BFF (A).** Start here. The BFF is statically configured from the ProductConfig at deployment time. When the platform evolves to SaaS, the BFF can be extended to resolve configuration dynamically per request — the conceptual model remains the same, only the configuration loading changes.

---

## 5. The Bridge — Feature and BFF as PLE Core Assets

The feature is the **central mediating artifact** between Platform and Product spaces (Czarnecki & Eisenecker, 2000). The BFF is the **runtime manifestation** of that mediation.

```
PRODUCT SPACE                          PLATFORM SPACE
─────────────                          ──────────────
Product                                Platform-Feature Catalog
  │ selects                              │ contains
  ▼                                      ▼
Product-Feature ◄── configures ───── Platform-Feature
  │ resolves variability                 │ declares variability (UVL)
  │ fills extension points               │ defines extension points
  │                                      │ owns screen contracts (AUI/CUI)
  ▼                                      │ specifies backend deps (§5)
BFF ◄─────── configured by ──────── Feature Spec §5 + §5.2
  │ composes view models                 │ defines service calls
  │ enforces feature-gating              │ defines view-model shape
  │ routes extension calls               │ defines failure modes
  ▼                                      ▼
Frontend (CUI) ──── via BFF ────────► Service APIs
```

### 5.1 Feature Lifecycle Across Spaces

| Phase | Space | Activity | Artifact |
|-------|-------|----------|----------|
| Discovery | Product | AI-guided interviews identify needs | Feature Candidate (Elara) |
| Cataloging | Platform | Feature specified and added to catalog | Platform-Feature (Telos): Spec + UVL + AUI + CUI(s) |
| Selection | Product | PO selects features for a product | ProductConfig (Elara) |
| Configuration | Product | Variability resolved, attributes set | Product-Feature (configured) |
| Extension | Product | Extension points filled with customer-specific logic | Extended Product-Feature |
| BFF Configuration | Product | BFF loaded with product config, feature gates, extension routes | Running BFF instance |
| Implementation | Both | Backend services + UI components built | Running system |

### 5.2 Feature Ownership Rule

A platform-feature belongs to the suite whose **UBL it primarily uses**.

- `F-PPS-012` "Post Goods Receipt" → PPS (speaks: material, stock, lot, warehouse)
- `F-FI-005` "Post Journal Entry" → FI (speaks: account, debit, credit, period)

**When a feature uses vocabulary from multiple suites** (e.g., "Invoice Matching" speaks both PPS and FI): assign to the **dominant suite** — the one whose UBL the feature PRIMARILY operates in. The secondary suite's data is accessed via cross-suite reads with `requires` constraints (see §6.2). If no suite is clearly dominant, this is a design signal to split the feature.

---

## 6. Cross-Suite Interaction

### 6.1 The Rule: Read-Across, Mutate-Local

A feature may **read** data from any suite's services (via BFF), but may only **write** to services within its own suite. Cross-suite mutations are triggered via domain events (eventual consistency).

```
Feature F-SD-001 "Create Sales Order" (owned by SD)

  ✓ WRITE to sd-ord-svc         (own suite — allowed)
  ✓ READ from pps-im-svc        (cross-suite read — allowed)
  ✓ READ from bp-svc             (T2 read — always allowed)
  ✗ WRITE to pps-im-svc         (cross-suite write — NOT allowed)

  Instead: sd-ord-svc emits "sd.sd.order.created" event
           → pps-im-svc reacts by reserving stock (eventual consistency)
```

### 6.2 Feature Dependencies from Cross-Suite Access

**Every cross-suite access — including reads — creates a feature dependency.** If `F-SD-001` reads stock data from PPS, then `F-SD-001` has a `requires` constraint on the PPS feature that provides that data (e.g., `F-PPS-010` "Stock Overview").

This is formalized in the UVL model:

```uvl
features
    F-SD-001 "Create Sales Order"
        constraints
            F-SD-001 requires F-PPS-010   // needs stock data (read)
            F-SD-001 requires F-BP-001    // needs customer data (read)
```

**Why this matters:** During product configuration (Application Engineering), the variability resolution process must check that all `requires` constraints are satisfied. If a product includes `F-SD-001`, it MUST also include `F-PPS-010` and `F-BP-001`. The UVL validator enforces this — incomplete products are rejected.

This means: **features use other features, not services directly.** The BFF translates feature-level dependencies into service-level calls. The feature only knows "I need stock data from `F-PPS-010`" — the BFF knows that means "call `GET /api/pps/im/v1/stock`".

### 6.3 Consequences

| Concern | Rule |
|---------|------|
| Write operations | Only to own suite's services |
| Read operations | Any suite, but creates `requires` constraint on providing feature |
| Cross-suite mutations | Via domain events (eventual consistency) |
| BFF behavior | Orchestrates reads across suites, writes only to owning suite |
| Feature Spec §5 | Documents all backend deps with `isMutation` flag and providing feature ID |
| UVL constraints | `requires` for every cross-suite read dependency |
| Product validation | Incomplete feature sets (missing required features) are rejected |

---

## 7. Tier Governance

### 7.1 Tier Definitions

```
T1 — Platform & Technical Foundations
     Suite-agnostic services used by all products.
     Examples: IAM, ref, i18n, si, dms, rpt

T2 — Shared Enterprise Business
     Cross-suite business data (DDD "shared kernel").
     Examples: bp (Business Partner), cal (Calendar)

T3 — Core Business Suites
     Suite-specific domain services where feature mutations happen.
     Examples: All PPS, FI, SD, HR, PS domains

T4 — Data, Analytics & Integration
     Read-only consumer of domain events.
     Examples: BI, ETL pipelines, data lake
```

### 7.2 Dependency Direction

Higher tiers depend on lower. Never the reverse.

```
T4 → T3 → T2 → T1
```

### 7.3 Feature ↔ Tier

Features exist at **T2 and T3**. T1 and T4 do not own features.

| Tier | Has Features? | Rationale |
|------|--------------|-----------|
| T1 — Platform | No | T1 services are technical infrastructure (IAM, ref, i18n, si, dms). They expose APIs but have no user-facing features. They are consumed as dependencies by T2/T3 features. |
| T2 — Shared Business | **Yes** | T2 services have fachliche (business) functionality. "Manage Business Partners" (`F-BP-001`) is a real user-facing feature owned by the `bp` suite. |
| T3 — Core Business | **Yes** | The primary tier for features. Most features live here. |
| T4 — Data & Analytics | No | T4 is a read-only consumer. Analytics dashboards may be features of T3 suites that READ T4 data, but T4 itself does not own features. |

This means the feature ID scheme `F-{SUITE}-{NNN}` works for both T2 and T3 suites. `F-BP-001` is a T2 feature, `F-PPS-012` is a T3 feature — the tier is determined by the suite, not by the feature ID.

---

## 8. The Vertical Path — End-to-End Derivation

How a business need becomes a running system:

```
Business Process (discovered in Elara / Product Space)
        │
        │  "We identified a need"
        ▼
Feature Candidate (tagged in Elara / Product Space)
        │
        │  "This need becomes a platform capability"
        ▼
Platform-Feature (specified in Telos / Platform Space)
        │  Feature Spec (.md):   §0–§4 problem space, §5–§8 solution space
        │  Feature Model (.uvl): variability, constraints, binding times
        │  Screen Contract AUI:  task model, zones, absent-rules (from §2–§4)
        │  Screen Contract CUI:  platform-specific rendering (web, mobile, desktop)
        │  Extension Points:     defined hooks for product customization
        │  BFF Contract (§5+§5.2): service calls, view-model shape, failure modes
        │
        ├── "The feature needs backend services"
        │         ▼
        │   Service Spec (aggregates, API, events, data model)
        │         │
        │         ▼
        │   Contracts (OpenAPI + AsyncAPI + JSON Schema)
        │
        └── "The feature needs a product-specific frontend"
                  ▼
            Product-Feature (configured in Elara / Product Space)
                  │  Variability resolved, extensions filled
                  │
                  ▼
            BFF (configured by ProductConfig)
                  │  Composes view models from service calls
                  │  Enforces feature-gating (full/read-only/excluded)
                  │  Routes extension calls to custom services
                  │
                  ▼
            Frontend (CUI implementation)
```

### 8.1 Artifacts at Each Transition

| Transition | Input | Output | Space |
|-----------|-------|--------|-------|
| Process → Feature Candidate | BPMN, actors, rules | Feature ID, suite, rough scope | Product |
| Candidate → Platform-Feature | Feature ID, scope | Spec + UVL + AUI + CUI(s) + Extension Points | Platform |
| Platform-Feature → Service Spec | Spec §5 (backend deps) | Aggregates, API, events, data model | Platform |
| Service Spec → Contracts | Spec content | OpenAPI, AsyncAPI, JSON Schema | Platform |
| Platform-Feature → Product-Feature | Catalog entry + ProductConfig | Configured instance + filled extensions | Product |
| Product-Feature → BFF Config | ProductConfig, Feature Spec §5+§5.2 | BFF routes, view-model composers, feature gates | Product |
| Contracts + BFF → Implementation | All specs + contracts + BFF config | Backend services, BFF instance, Frontend UI | Implementation |

---

## 9. Open Decisions

All open decisions have been resolved:

| ID | Question | Decision | Rationale |
|----|----------|----------|-----------|
| OD-01 | Product composition | Customer-driven Application Engineering | PO selects features from catalog based on processes and personas |
| OD-02 | Cross-suite access | Read-across, mutate-local + `requires` constraints | Reads allowed cross-suite but create feature dependencies. Writes only to own suite. Cross-suite mutations via events. UVL `requires` enforces completeness at product configuration time. |
| OD-03 | Multi-suite vocabulary | Assign to dominant suite | Feature belongs to suite whose UBL it primarily uses. Secondary suite data via cross-suite reads. If no clear dominant → split the feature. |
| OD-04 | T1/T2 features | T2 yes, T1 no | T2 has business features (e.g., `F-BP-001`). T1 is technical infrastructure with APIs but no user-facing features. T4 is read-only, no features. |
| OD-06 | BFF deployment | Per-Product (default), Shared/Modular for SaaS | Start with per-product BFF. Evolve to dynamic configuration per subscription for SaaS. Conceptual model is the same — only config loading changes. |
