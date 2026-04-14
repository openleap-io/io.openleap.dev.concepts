# Suite Package Blueprint — Canonical Structure

> **Purpose:** This document is the single source of truth for what a complete suite delivery package MUST contain, how it is organized, and which naming conventions apply. Use it as input when generating a new suite or upgrading an existing one to full compliance.
>
> **Reference implementation:** `ps-suite-complete.zip` (PS — Project Management Suite)
>
> **Version:** 1.0.0 — 2026-04-03

---

## 1. Package Root Layout

```
{suite}-suite/
├── _{suite}_suite.md                                        ← Suite Spec
├── {suite}.catalog.uvl                                      ← UVL Catalog Root
│
├── domain-specs/                                            ← Domain / Service Specs
│   ├── {suite}_{domain1}-spec.md
│   ├── {suite}_{domain2}-spec.md
│   └── ...
│
├── contracts/                                               ← Machine-Readable Contracts
│   ├── http/                                                  ← REST API Contracts
│   │   └── {suite}/
│   │       ├── {domain1}/openapi.yaml
│   │       ├── {domain2}/openapi.yaml
│   │       └── ...
│   ├── events/                                                ← Event Schema Contracts
│   │   └── {suite}/
│   │       ├── {domain1}/
│   │       │   ├── {aggregate}.{action}.schema.json
│   │       │   └── ...
│   │       ├── {domain2}/
│   │       │   └── ...
│   │       └── ...
│   └── aui/                                                   ← AUI Screen Contracts
│       ├── F-{SUITE}-001-01.aui.yaml
│       ├── F-{SUITE}-001-02.aui.yaml
│       └── ...                                                  (one per leaf feature)
│
├── features/                                                ← Feature Specifications
│   ├── compositions/                                          ← Composition Node Specs
│   │   ├── F-{SUITE}-001.md
│   │   ├── F-{SUITE}-002.md
│   │   └── ...
│   └── leaves/                                                ← Leaf Feature Specs
│       ├── F-{SUITE}-001-01/
│       │   └── feature-spec.md
│       ├── F-{SUITE}-001-02/
│       │   └── feature-spec.md
│       └── ...
│
└── uvl/                                                     ← UVL Variability Models
    ├── compositions/                                          ← Composition UVL
    │   ├── F-{SUITE}-001.uvl
    │   ├── F-{SUITE}-002.uvl
    │   └── ...
    └── leaves/                                                ← Leaf Feature UVL
        ├── F-{SUITE}-001-01.uvl
        ├── F-{SUITE}-001-02.uvl
        └── ...
```

---

## 2. Artifact Categories

### 2.1 Root Artifacts (2 files)

| # | File | Template | Naming Convention | Description |
|---|------|----------|-------------------|-------------|
| 1 | `_{suite}_suite.md` | `suite-spec.md` v1.0.0 | Prefix underscore + suite ID + `_suite.md` | The suite specification. Sections SS0–SS11. 100% template compliance required. |
| 2 | `{suite}.catalog.uvl` | `suite-catalog.uvl` v1.0.0 | Suite ID + `.catalog.uvl` | Machine-readable UVL root. Imports all composition UVLs. Defines `{SUITE}_UI` root feature with optional group. |

### 2.2 Domain Specs (1 per service)

| Location | `domain-specs/{suite}_{domain}-spec.md` |
|----------|----------------------------------------|
| Template | `domain-service-spec.md` v1.0.0 |
| Naming | `{suite}_{domain}-spec.md` where `{domain}` is the bounded context short code (e.g., `prj`, `bud`, `tim`) |
| Sections | §0–§15 all present. 95%+ template compliance required. |
| Count | **1 per microservice** in the suite's service landscape (SS3 of suite spec) |

**Contents per domain spec:**

| Section | Title | Must contain |
|---------|-------|-------------|
| §0 | Document Purpose & Scope | Purpose, audience, in/out scope, related docs |
| §1 | Business Context | Domain purpose, business value, stakeholders, strategic positioning |
| §2 | Service Identity | Service ID, suite, domain, base package, API path, port, repo, team |
| §3 | Domain Model | Aggregates with root + entities + VOs, enums, lifecycle states, invariants |
| §4 | Business Rules | Catalog with ID, name, type, enforcement, error code |
| §5 | Use Cases | Canonical format: id, type, trigger, aggregate, operation, inputs, outputs, events, REST endpoint |
| §6 | REST API | Base path, auth, endpoints with full request/response examples. References OpenAPI companion. |
| §7 | Events & Integration | Published/consumed events with routing keys, payloads, flow diagrams |
| §8 | Data Model | Tables with columns/types/constraints/indexes, ERD |
| §9 | Security & Compliance | Data classification, role-permission matrix, compliance reqs |
| §10 | Quality Attributes | Performance targets, availability SLAs, scalability, maintainability |
| §11 | Feature Dependencies | Which F-{SUITE}-{NNN} features call this service |
| §12 | Extension Points | Extension events, aggregate hooks, extension API |
| §13 | Migration & Evolution | Data migration, deprecation path, versioning |
| §14 | Decisions & Open Questions | ADRs, consistency checks, open questions |
| §15 | Appendix | Glossary, references, change log, review & approval |

### 2.3 Contracts

#### 2.3.1 HTTP / REST API Contracts (1 OpenAPI per service)

| Location | `contracts/http/{suite}/{domain}/openapi.yaml` |
|----------|------------------------------------------------|
| Format | OpenAPI 3.1.0 |
| Naming | Always `openapi.yaml` inside `{suite}/{domain}/` |
| Derived from | Domain spec §6 (REST API) |
| Count | **1 per microservice** |

**Required OpenAPI metadata:**

```yaml
x-owner: team-{suite}
x-suite: {suite}
x-domain: {domain}
```

#### 2.3.2 Event Schema Contracts (1 JSON Schema per event)

| Location | `contracts/events/{suite}/{domain}/{aggregate}.{action}.schema.json` |
|----------|---------------------------------------------------------------------|
| Format | JSON Schema Draft 2020-12 |
| Naming | `{aggregate}.{action}.schema.json` matching the routing key pattern `{suite}.{domain}.{aggregate}.{action}` |
| Derived from | Domain spec §7 (Events), Suite spec SS5 (Event Catalog) |
| Count | **1 per published event** across all services |

**Required envelope fields in every event schema:**

```json
{
  "eventId": "uuid",
  "eventType": "{suite}.{domain}.{aggregate}.{action}",
  "timestamp": "ISO-8601",
  "tenantId": "uuid",
  "correlationId": "uuid",
  "causationId": "uuid (optional)",
  "producer": "{suite}-{domain}-svc",
  "schemaVersion": "1.0.0",
  "payload": { }
}
```

#### 2.3.3 AUI Screen Contracts (1 per leaf feature)

| Location | `contracts/aui/F-{SUITE}-{NNN}-{NN}.aui.yaml` |
|----------|------------------------------------------------|
| Format | YAML following `screen-contract-aui.yaml` template |
| Naming | Feature ID + `.aui.yaml` — **flat directory**, no nesting |
| Derived from | Feature spec §2 (journey → task model), §3 (fields → zones), §4 (edge cases → absent-rules) |
| Count | **1 per leaf feature** |

**Required AUI sections:**

| Section | Description |
|---------|-------------|
| `featureId` | Feature ID reference |
| `taskModel` | Task operators: sequential, concurrent, alternative, optional, enabling |
| `zones` | Named layout regions with type (fixed/variable/feature-gated), priority, dataBinding, fields |
| `absentRules` | What happens when optional zones are absent (collapse-up, end-page, tab-hidden, panel-section-hidden) |
| `businessRules` | Role gates, state conditions, action consequences |

### 2.4 Feature Specifications

#### 2.4.1 Composition Specs (1 per composition node)

| Location | `features/compositions/F-{SUITE}-{NNN}.md` |
|----------|---------------------------------------------|
| Template | `feature-composition-spec.md` v1.0.0 |
| Naming | Feature ID + `.md` — **flat directory** |
| Count | **1 per composition node** in the feature tree (SS6 of suite spec) |

**Required sections:**

| Section | Content |
|---------|---------|
| SS0 | Identity: purpose, children table, position in tree |
| SS1 | Children & Variability Structure: group logic (mandatory/optional/alternative/or/mixed), business rationale |
| SS2 | Constraints: cross-suite deps, invalid combinations, edge cases |
| SS3 | Quality & Testing Guidance |
| SS4 | Change Log: open questions, decision log, version history |

#### 2.4.2 Leaf Feature Specs (1 per leaf feature)

| Location | `features/leaves/F-{SUITE}-{NNN}-{NN}/feature-spec.md` |
|----------|----------------------------------------------------------|
| Template | `feature-spec.md` v1.0.0 |
| Naming | Always `feature-spec.md` inside a directory named by Feature ID |
| Count | **1 per leaf feature** |

**Required sections (Problem Space + Solution Space + Bridge):**

| Section | Title | Content |
|---------|-------|---------|
| §0 | Feature Identity & Orientation | One-line summary, non-goals, entry/exit points, variability points, tree position |
| §1 | User Goal & Scenarios | User goal, 2–5 scenarios (mainstream, not edge cases) |
| §2 | User Journey & Screen Layout | Mermaid sequence diagram (happy path + error), ASCII screen layouts |
| §3 | Interaction Requirements | Form fields table, actions table, cross-field rules, confirmation dialogs, unsaved changes guard, validation messages |
| §4 | Edge Cases & Screen States | Component states (loading/empty/error/populated), specific edge cases, attribute-driven behaviour, connectivity |
| §5 | Backend Dependencies & BFF Contract | Service calls table, BFF view-model (JSON-C), feature-gating rules, failure modes, caching hints, i18n keys |
| §6 | Screen Contract (AUI) | Reference to companion `.aui.yaml` OR inline YAML |
| §7 | i18n, Permissions & Accessibility | Permission matrix, accessibility requirements |
| §8 | Acceptance Criteria | Given/When/Then per scenario, edge case, permission rule |
| §9 | Dependencies, Variability & Extension Points | Feature deps (UVL requires), variability points, extension zones |
| §10 | Change Log & Review | Version history, approval status |

### 2.5 UVL Variability Models

#### 2.5.1 Composition UVL (1 per composition node)

| Location | `uvl/compositions/F-{SUITE}-{NNN}.uvl` |
|----------|------------------------------------------|
| Template | `feature-composition.uvl` v1.0.0 |
| Naming | Feature ID + `.uvl` — **flat directory** |
| Imports | All child leaf UVL files |
| Group type | Matches the composition spec's SS1 group logic |
| Count | **1 per composition node** |

#### 2.5.2 Leaf UVL (1 per leaf feature)

| Location | `uvl/leaves/F-{SUITE}-{NNN}-{NN}.uvl` |
|----------|------------------------------------------|
| Template | `feature-leaf.uvl` v1.0.0 |
| Naming | Feature ID + `.uvl` — **flat directory** |
| Content | Feature attributes with binding times, cross-suite `requires` constraints |
| Count | **1 per leaf feature** |

---

## 3. Counting Rules

Given a suite with **D** domain services, **C** composition nodes, and **L** leaf features, the total file count is:

| Category | Formula | Example (D=6, C=6, L=37) |
|----------|---------|---------------------------|
| Root (suite spec + catalog) | 2 | 2 |
| Domain specs | D | 6 |
| OpenAPI contracts | D | 6 |
| Event schema contracts | varies (E events total) | 38 |
| AUI screen contracts | L | 37 |
| Composition specs | C | 6 |
| Leaf feature specs | L | 37 |
| Composition UVL | C | 6 |
| Leaf UVL | L | 37 |
| **Total** | **2 + D×2 + E + L×3 + C×2** | **175** |

---

## 4. Naming Conventions Summary

| Element | Pattern | Example |
|---------|---------|---------|
| Suite ID | `^[a-z]{2,4}$` | `ps`, `iam`, `fi`, `pps` |
| Suite spec file | `_{suite}_suite.md` | `_ps_suite.md` |
| Catalog UVL | `{suite}.catalog.uvl` | `ps.catalog.uvl` |
| Domain short code | lowercase, 2–4 chars | `prj`, `bud`, `tim`, `authz` |
| Domain spec file | `{suite}_{domain}-spec.md` | `ps_prj-spec.md` |
| Feature ID (composition) | `F-{SUITE}-{NNN}` | `F-PS-001` |
| Feature ID (leaf) | `F-{SUITE}-{NNN}-{NN}` | `F-PS-001-01` |
| OpenAPI path | `contracts/http/{suite}/{domain}/openapi.yaml` | `contracts/http/ps/prj/openapi.yaml` |
| Event schema | `contracts/events/{suite}/{domain}/{agg}.{action}.schema.json` | `contracts/events/ps/prj/project.created.schema.json` |
| AUI contract | `contracts/aui/F-{SUITE}-{NNN}-{NN}.aui.yaml` | `contracts/aui/F-PS-001-01.aui.yaml` |
| Composition spec | `features/compositions/F-{SUITE}-{NNN}.md` | `features/compositions/F-PS-001.md` |
| Leaf feature spec | `features/leaves/F-{SUITE}-{NNN}-{NN}/feature-spec.md` | `features/leaves/F-PS-001-01/feature-spec.md` |
| Composition UVL | `uvl/compositions/F-{SUITE}-{NNN}.uvl` | `uvl/compositions/F-PS-001.uvl` |
| Leaf UVL | `uvl/leaves/F-{SUITE}-{NNN}-{NN}.uvl` | `uvl/leaves/F-PS-001-01.uvl` |
| Event routing key | `{suite}.{domain}.{aggregate}.{action}` | `ps.prj.project.created` |

---

## 5. Derivation Chain

Each artifact is derived from a specific source. The chain must be traceable:

```
Suite Spec (SS5 Event Catalog)
  └──→ Event Schema contracts (contracts/events/)

Suite Spec (SS3 Service Landscape)
  └──→ Domain Specs (domain-specs/)
        ├──→ OpenAPI contracts (contracts/http/) ← from §6
        └──→ Event Schemas (contracts/events/)   ← from §7

Suite Spec (SS6 Feature Catalog)
  ├──→ Catalog UVL ({suite}.catalog.uvl)
  ├──→ Composition Specs (features/compositions/)
  │     └──→ Composition UVL (uvl/compositions/)
  └──→ Leaf Feature Specs (features/leaves/)
        ├──→ AUI Screen Contracts (contracts/aui/) ← from §2+§3+§4
        └──→ Leaf UVL (uvl/leaves/)                ← from §9
```

---

## 6. Quality Gates

Before a suite package is considered **complete**, verify:

- [ ] Suite spec present at root and at 100% template compliance (SS0–SS11)
- [ ] Catalog UVL present at root, imports all composition UVLs
- [ ] One domain spec per service listed in suite spec SS3
- [ ] One OpenAPI per domain spec, matching §6 endpoints
- [ ] One event schema per event listed in suite spec SS5.4
- [ ] One composition spec per composition node in SS6 feature tree
- [ ] One leaf feature spec per leaf in SS6 feature tree
- [ ] One AUI contract per leaf feature
- [ ] One composition UVL per composition node
- [ ] One leaf UVL per leaf feature
- [ ] All feature IDs consistent across specs, UVL, and AUI
- [ ] All event routing keys consistent between suite spec SS5.4, domain spec §7, and event schema `$id`
- [ ] All API endpoints consistent between domain spec §6 and OpenAPI contract

---

## 7. Generation Prompt Template

When instructing an AI to generate a complete suite package, provide:

```
Generate a complete suite package for the {SUITE_NAME} suite ({suite_id}).

Inputs:
- Suite spec: _{suite}_suite.md (or describe the suite)
- Domains: {list of domain short codes and names}
- Features: {feature tree from SS6}

Output: ZIP file following SUITE_PACKAGE_BLUEPRINT.md structure.

Required artifacts per the blueprint:
1. Root: _{suite}_suite.md + {suite}.catalog.uvl
2. domain-specs/: one {suite}_{domain}-spec.md per domain
3. contracts/http/{suite}/{domain}/openapi.yaml per domain
4. contracts/events/{suite}/{domain}/*.schema.json per event
5. contracts/aui/F-{SUITE}-{NNN}-{NN}.aui.yaml per leaf
6. features/compositions/F-{SUITE}-{NNN}.md per composition
7. features/leaves/F-{SUITE}-{NNN}-{NN}/feature-spec.md per leaf
8. uvl/compositions/F-{SUITE}-{NNN}.uvl per composition
9. uvl/leaves/F-{SUITE}-{NNN}-{NN}.uvl per leaf
```

---

**END OF BLUEPRINT**
