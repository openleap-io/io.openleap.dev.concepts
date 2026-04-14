<!-- Template Meta
     Template-ID:   TPL-FEAT
     Version:       1.0.0
     Last Updated:  2026-04-03
     Changelog:
       1.0.0 (2026-04-03) — Initial versioned baseline.
-->

# F-{SUITE}-{NNN}-{NN} — {Feature Name}

> **Conceptual Stack Layer:** Platform-Feature
> **Space:** Platform
> **Owner:** Domain Engineering Team
> **Companion files:** `F-{SUITE}-{NNN}-{NN}.uvl` (§9), `F-{SUITE}-{NNN}-{NN}.aui.yaml` (§6), `F-{SUITE}-{NNN}-{NN}.attrs.md` (§9, optional)
> **Referenced by:** Product Spec SS17 (Feature Selection), Suite Feature Catalog SS6
> **References:** Domain/Service Spec (SS5 backend deps), Suite Spec (belongs to)

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `feature-spec.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections or "fully compliant"}
> - **Author(s):** Name(s)
> - **Status:** [DRAFT | REVIEW | APPROVED | DEPRECATED]
> - **Feature ID:** `F-{SUITE}-{NNN}-{NN}` (e.g., `F-SD-001-02`)
> - **Suite:** `{suite}` — suite-owned, not product-owned
> - **Node type:** LEAF
> - **Parent:** `F-{SUITE}-{NNN}` — see `F-{SUITE}-{NNN}.md`
> - **Companion UVL:** `F-{SUITE}-{NNN}-{NN}.uvl`
> - **Companion AUI:** `F-{SUITE}-{NNN}-{NN}.aui.yaml`
> - **Attribute doc:** `F-{SUITE}-{NNN}-{NN}.attrs.md` *(if exists)*

> **What this document is — and isn't**
>
> A Platform-Feature Spec describes one functional UI capability owned by
> the `{suite}` suite. It is reusable across products.
>
> **Problem Space (SS0-SS4):** What the user sees and does — stable across
> backend implementations. Changes only when the user journey changes.
>
> **Solution Space (SS5-SS6):** Which backend services this feature calls,
> how the BFF composes the view model, and how the screen contract (AUI)
> defines platform-free UI structure. Backend domain specs are authoritative
> for API contracts, business rules, and events. This spec only states what
> the feature needs; it does not re-specify the API.
>
> **Bridge Artifacts (SS7-SS9):** i18n, permissions, acceptance criteria,
> variability, and extension points that connect the problem and solution
> spaces.
>
> The companion `.uvl` file holds the machine-readable attribute schema
> and cross-suite requires. The companion `.aui.yaml` holds the
> machine-readable screen contract. Write this `.md` first, then derive
> the `.uvl` and `.aui.yaml`.

---

## ═══════════════════════════════════════════════
## PROBLEM SPACE  (stable — user-facing)
## ═══════════════════════════════════════════════

## 0. Feature Identity & Orientation

<!-- This section establishes WHAT the feature is, where it sits in the tree,
     and what it explicitly does NOT do. Fill this in first — it constrains
     everything that follows.
     Schema alignment: feature identity, orientation metadata. -->

### 0.1 One-Line Summary

<!-- "This feature lets {role type} {do what} so that {outcome}."
     One sentence. Must be understandable by a business stakeholder. -->

### 0.2 Non-Goals

<!-- Write these BEFORE the requirements.
     Format: "Does not {X} — that is {where it lives}."
     Non-goals prevent scope creep and clarify boundaries.
     Examples:
     - "Does not create customers — that is F-BP-001-03."
     - "Does not show invoice history — that is F-FI-002-01."
     - "Does not send notifications — handled by the event bus downstream." -->

### 0.3 Entry & Exit Points

<!-- How does the user arrive at this feature? Where do they go after?
     Entry points: navigation menu, task list, deeplink, barcode scan, etc.
     Exit points: what happens after the primary action completes. -->

**Entry points:**
- {e.g., "From the Inbound task list (pending deliveries)"}
- {e.g., "By scanning a delivery note barcode"}

**Exit points:**
- {e.g., "Goods receipt posted -> event emitted -> triggers Put-Away (F-PPS-WM-001)"}

### 0.4 Variability Points

<!-- What can vary about this feature across different products or deployments?
     For each variability point, state whether it is modelled as:
       (a) XOR sibling leaves — when variants have different user journeys
           or different service calls (different spec needed)
       (b) UVL attribute — when the journey is the same but a parameter changes
           (same spec, different configured behaviour)
     This section is the source for the companion .uvl attribute schema.
     SPLE rule: only model variability that is user-relevant or deployment-relevant.
     Do not model implementation details as variability points. -->

| Variability | Modelled as | UVL | Default | Binding time |
|---|---|---|---|---|
| {e.g., Records per page} | Attribute | `pagination.pageSize Integer 50` | 50 | `deploy` |
| {e.g., Approval comment required} | Attribute | `approval.requireComment Boolean false` | false | `deploy` |
| {e.g., Full vs read-only access} | XOR siblings: `F-{SUITE}-{NNN}-{NN}a` / `b` | — | — | — |

### 0.5 Position in Feature Tree

```
F-{SUITE}-{NNN}  {Parent name}          [COMPOSITION]
├── F-{SUITE}-{NNN}-01  {Sibling}       [LEAF]
├── F-{SUITE}-{NNN}-02  {This feature}  [LEAF] ← you are here
└── F-{SUITE}-{NNN}-03  {Sibling}       [LEAF]
```

### 0.6 Related Documents

| Document | What to find there |
|---|---|
| `F-{SUITE}-{NNN}.md` | Parent composition node — variability structure, valid configurations |
| `F-{SUITE}-{NNN}-{NN}.uvl` | Companion UVL — attribute schema, cross-suite requires |
| `F-{SUITE}-{NNN}-{NN}.aui.yaml` | Companion AUI — screen contract (task model, zones, absent-rules) |
| `F-{SUITE}-{NNN}-{NN}.attrs.md` | Attribute documentation *(if exists)* |
| `{suite}-suite-spec.md` §6 | Suite Feature Catalog — feature tree, mandatory features, cross-suite deps |
| `{suite}_{domain}-spec.md` | Backend: business rules, API contracts *(authoritative)* |
| `{suite}_{domain2}-spec.md` | Additional backend spec *(if this feature spans domains)* |
| `UI_STANDARDS.md` | Platform UX standards — error handling, i18n, accessibility |

---

## 1. User Goal & Scenarios

<!-- This section captures WHY the user uses this feature and WHAT they do with it.
     The goal is stable even if the implementation changes.
     Scenarios become the basis for acceptance criteria (SS8). -->

### 1.1 The User Goal

<!-- What is the user trying to accomplish?
     State as a GOAL, not a task.
     Bad:  "Fill in the form and click Save."
     Good: "Get a new customer order into the system accurately and quickly
            so the customer can be confirmed and fulfilment can begin."
     The goal is stable even if the implementation changes. -->

### 1.2 User Scenarios

<!-- 2-5 concrete stories of real users in real conditions.
     These are mainstream situations — NOT edge cases (those go in SS4).
     Each scenario names: a role type, a context, and the expected outcome.
     Scenarios become acceptance test bases (SS8). -->

**Scenario 1: {Name}**
> A {role type} wants to {goal} because {reason}.
> They {action sequence at user goal level — not UI mechanics}.
> They expect {observable outcome}.

**Scenario 2: {Name}**
> ...

**Scenario 3: {Name}**
> ...

---

## 2. User Journey & Screen Layout

<!-- The primary axis of this spec.
     Mermaid for flows. ASCII for screen layouts.
     Problem space: describe what the user sees and does.
     Do NOT name API endpoints, component classes, or CSS here.

     DERIVATION NOTE: This section feeds the AUI task model.
     - The sequence diagram maps to task model operators:
       sequential steps -> sequential(A, B)
       parallel steps -> concurrent(A, B)
       conditional steps -> alternative(A | B)
       optional steps -> optional(A)
       dependent steps -> enabling(B <- A)
     - See SS6 for the AUI derivation. -->

### 2.1 Happy-Path Flow

```mermaid
sequenceDiagram
    actor User as {Role type}
    participant UI as Feature UI
    participant SYS as System (BFF / services — detail in SS5)

    User->>UI: {Entry action}
    UI->>SYS: Load data needed to render
    SYS-->>UI: {What the user sees on arrival}
    UI-->>User: Render {screen name}

    User->>UI: {Interaction 1}
    UI->>UI: Validate on blur

    User->>UI: {Primary action}
    UI->>UI: Full validation
    alt Validation fails
        UI-->>User: Inline errors; focus first error
    else Validation passes
        UI->>SYS: {Submit / trigger action}
        alt Success
            SYS-->>UI: Confirmation
            UI-->>User: {Navigate / toast / state change}
        else Business rule violation
            SYS-->>UI: Error with reason
            UI-->>User: Inline error on relevant field or banner
        else System unavailable
            SYS-->>UI: Failure
            UI-->>User: Error banner with retry
        end
    end
```

### 2.2 Screen Layout(s)

<!-- ASCII sketch = information hierarchy + action placement.
     One sketch per distinct screen state.
     NOT visual design — visual design lives in Figma / Design System.

     DERIVATION NOTE: Each named section in the layout becomes a ZONE
     in the AUI screen contract (SS6). Mark sections that should be
     extension zones with [EXT]. -->

**Screen: {Feature name} — {State, e.g. "Entry form"}**
```
┌────────────────────────────────────────────────────────┐
│  ← {Back label}        {Page title}       [{Actions}]  │  ← action bar
├────────────────────────────────────────────────────────┤
│  SECTION: {Name}                                       │
│  ┌─────────────────────┐  ┌─────────────────────────┐ │
│  │ {Field A} *         │  │ {Field B}               │ │
│  └─────────────────────┘  └─────────────────────────┘ │
│  ┌─────────────────────────────────────────────────┐   │
│  │ {Field C — full width}                          │   │
│  └─────────────────────────────────────────────────┘   │
├────────────────────────────────────────────────────────┤
│  SECTION: {Name}                                       │
│  {fields...}                                           │
├────────────────────────────────────────────────────────┤
│  SECTION: {Extension Zone} [EXT]                       │
│  {Product-specific custom fields rendered here}        │
├────────────────────────────────────────────────────────┤
│                              [Cancel]  [{Primary CTA}] │
└────────────────────────────────────────────────────────┘
```
*(Add further states: empty, error, read-only, loading skeleton.)*

---

## 3. Interaction Requirements

<!-- This section defines the concrete inputs, outputs, and rules for
     the user's interaction with the feature.

     DERIVATION NOTE: This section feeds AUI zones (SS6).
     - Each form section maps to a zone.
     - Field tables define what data binds to each zone.
     - Actions define the zone's behavior.
     - Cross-field rules define the zone's constraints. -->

### 3.1 Form Fields

<!-- * = required.
     "Data source" names the domain aggregate — not the endpoint.
     Validation rules with business meaning reference the backend spec by rule ID. -->

| Field Label | Data source | Control | Req | Validation | Notes |
|---|---|---|:---:|---|---|
| {Label} * | `{suite}.{aggregate}.{field}` | Text input | Y | Max 255 chars | |
| {Label} | `{suite}.{aggregate}.{field}` | Select (ref-data) | | Code in `{catalog}` | Labels via i18n |
| {Label} * | `{suite}.{aggregate}.{field}` | Number | Y | > 0, 2 dp | BR-{NNN} in `{spec}` |
| {Label} | `{suite}.{aggregate}.{field}` | Date picker | | >= today | |
| {Label} | `{suite2}.{aggregate}.{field}` | Typeahead search | Y | Valid record | Searches {suite2} |

**Cross-field rules** (UI-visible consequences only):
- `{Field A}` must be before `{Field B}`.
- When `{Field X}` changes, reload options for `{Field Y}`.
- See `{suite}_{domain}-spec.md SS4.3` for the complete set.

### 3.2 Actions

| Action | Trigger | Precondition | Confirmation | Required role | Outcome |
|---|---|---|:---:|---|---|
| {Primary CTA} | Button | Form valid | — | `{ROLE}` | Navigate + toast |
| Save as Draft | Button | — | — | `{ROLE}` | Stay + toast |
| {Lifecycle action} | Button | Status = `{STATE}` | Y SS3.3 | `{ROLE}` | Reload + toast |
| Cancel / Discard | Button / nav away | — | If dirty: SS3.4 | Any | Navigate back |
| Delete | Menu | Status = `DRAFT` | Y type-to-confirm | `{ROLE_ADMIN}` | List + toast |

### 3.3 Confirmation Dialogs

<!-- For each action marked Y in SS3.2: -->

**{Lifecycle action}**
- Title: `"{Action verb} {entity}?"`
- Body: "{One sentence describing the consequence.}"
- Primary: `"{Action verb}"` — `danger` or `primary` variant
- Secondary: `"Cancel"`
- While pending: both buttons disabled; spinner on primary
- On error: error inline in dialog; do not close
- On success: close; reload; toast

### 3.4 Unsaved Changes Guard

If form is dirty and user navigates away:
dialog — `"You have unsaved changes. Leave and discard them?"`
Options: `"Leave"` (discard) | `"Stay"` (return).

### 3.5 Inline Validation Messages

<!-- All messages externalised via i18n-svc.
     Key: {suite}.F-{SUITE}-{NNN}-{NN}.{field}.{errorCode} -->

| Field | Condition | Message |
|---|---|---|
| `{Field A}` | Blank on submit | `"{Label} is required."` |
| `{Field A}` | Exceeds max | `"{Label} must not exceed {N} characters."` |
| `{Field B}` | Not in catalog | `"Please select a valid {Label}."` |
| Cross-field | `{dateFrom}` > `{dateTo}` | `"Start date must be before end date."` |
| API 422 | BR-{NNN} violated | `"{Message from {suite}_{domain}-spec.md SS4.2}"` |

---

## 4. Edge Cases & Screen States

<!-- DERIVATION NOTE: This section feeds AUI absent-rules (SS6).
     - Each state in SS4.1 maps to absent-rule behavior.
     - Attribute-driven changes (SS4.3) map to feature-gated zones. -->

### 4.1 Component States

<!-- Every dynamic section handles all four base states: -->

| State | When | Behaviour |
|---|---|---|
| **Loading** | Awaiting response | Content-shaped skeleton; controls disabled |
| **Empty** | No data / null | Message +/- CTA (i18n key in SS5.3) |
| **Error** | Call failed | Inline message + retry; no raw HTTP codes |
| **Populated** | Data ready | Render normally |

### 4.2 Specific Edge Cases

| Case | Behaviour | Affected users |
|---|---|---|
| Insufficient role | Action absent from DOM entirely | Viewer roles |
| Concurrent edit — save rejected (412) | Form stays open; banner: "This record was updated by another user. Reload." | Concurrent editors |
| Required service unavailable | Dependent fields disabled; inline warning naming the affected fields | All users |
| Reference catalog load failure | Silent retry x3; then inline "Could not load options" + retry button | All users |
| Session expires mid-form | On next call: redirect to login with `returnUrl`; form state lost | Long sessions |
| {Domain-specific case} | {Behaviour} | {Who} |

### 4.3 Attribute-Driven Behaviour Changes

<!-- For each attribute in SS0.4, describe what changes in the rendered UI
     when the attribute takes a non-default value.
     This makes attributes observable — it closes the loop between
     the .uvl schema and the actual experience. -->

| Attribute | Non-default value | Observable change |
|---|---|---|
| `pagination.pageSize` | 10 | Shorter list; pagination bar appears at fewer records |
| `approval.requireComment` | true | "Comment" field appears as required below the approval action; empty comment prevents confirm |
| `{attr}` | `{value}` | `{what the user sees differently}` |

### 4.4 Connectivity

<!-- Does this feature require a live connection?
     If yes: state it and describe the error shown on connectivity loss.
     If partial offline is supported: describe what works and what doesn't. -->

This feature requires a live connection.
On network loss: top-of-page banner — "{Feature name} is unavailable offline."

---

## ═══════════════════════════════════════════════
## SOLUTION SPACE  (changes with implementation)
## ═══════════════════════════════════════════════

## 5. Backend Dependencies & BFF Contract

<!-- Solution space — review when services are refactored.
     This section is the N:M binding: which services this feature calls,
     when, and what it needs. It is the integration handshake with
     backend teams. Full API contracts are in the backend specs;
     this table states the feature's view of what it needs.

     Every service call listed here must also appear as a cross-suite
     require in the companion .uvl (if from a feature-bearing suite).

     This section also defines the BFF contract: how the BFF composes
     the view model, what feature-gating rules apply, how failures
     are handled, and what caching hints exist. -->

### 5.1 Service Calls

<!-- Each row documents one service call this feature makes.
     - Tier: T1/T2/T3 (determines dependency governance)
     - isMutation: Y = write (affected by feature-gating), N = read
     - Failure Mode: what happens in the UI when this call fails
       Options: Block (feature cannot proceed), Degrade (partial rendering),
       Retry (auto-retry then degrade) -->

| # | When | Service | Tier | Method + Endpoint | isMutation | What UI needs | Failure Mode |
|---|---|---|---|---|:---:|---|---|
| 1 | On init | `ref-data-svc` | T1 | `GET /api/ref/ref/v1/catalogs/{catalog}` | N | `[{code, label}]` | Retry then degrade |
| 2 | On typeahead | `bp-svc` | T2 | `GET /api/shared/bp/v1/parties?q={q}` | N | `[{id, name, number}]` | Inline search error |
| 3 | On {trigger} | `{suite}-{domain}-svc` | T3 | `GET /api/{suite}/{domain}/v1/{resource}?{params}` | N | `{shape}` | Degrade: disable field |
| 4 | On submit | `{suite}-{domain}-svc` | T3 | `POST /api/{suite}/{domain}/v1/{resource}` | Y | `{id, number}` | Block: map errors to fields |
| 5 | On {action} | `{suite}-{domain}-svc` | T3 | `POST /api/{suite}/{domain}/v1/{resource}/{id}/{action}` | Y | `{status}` | Error in dialog |

**Cross-suite reads -> UVL `requires` constraints:**
<!-- Every cross-suite access (including reads) creates a feature dependency.
     List them here and ensure they appear in the companion .uvl file. -->
- Reads `bp-svc` -> requires `F-BP-001` (Business Partner Search)
- Reads `{suite2}-{domain}-svc` -> requires `F-{SUITE2}-{NNN}` ({Feature Name})

### 5.2 BFF View-Model Shape

<!-- The BFF aggregates multiple service responses into one payload for the frontend.
     This JSON-C block IS the BFF <-> frontend contract.
     Changes here require coordinated BFF + frontend updates.

     Comments in the JSON-C indicate which backend service provides each field.
     The _meta object carries server-side computed permissions and editability
     so the frontend does not need to re-implement role/status logic. -->

```jsonc
// GET /bff/{suite}/features/F-{SUITE}-{NNN}-{NN}/init
// Aggregates: ref-data-svc (catalogs) + {svc} (record if editing)
{
  "catalogs": {
    "{catalogName}": [{ "code": "string", "label": "string" }]
  },
  "record": {                        // present when editing
    "id": "uuid",
    "version": 1,
    "{field}": "value",
    "_meta": {
      "allowedActions": ["edit", "approve"],  // role + status filtered server-side
      "editableFields": ["{field}"]           // fields immutable after creation omitted
    }
  }
}
```

### 5.3 Feature-Gating Rules

<!-- Feature-gating defines how the BFF behaves when this feature is configured
     in different modes by a product.
     Three modes: full (all endpoints), read-only (reads only), excluded (no calls).
     For each mode, state which service calls from SS5.1 are active. -->

| Mode | Behavior | Active Calls (from SS5.1) |
|------|----------|--------------------------|
| `full` | All read and write endpoints active | #1, #2, #3, #4, #5 |
| `read-only` | Read endpoints active; write endpoints blocked by BFF. Submit/action buttons hidden. | #1, #2, #3 |
| `excluded` | BFF does not call any endpoints for this feature. Feature entry point hidden. | — |

### 5.4 Failure Modes & Resilience

<!-- Detailed failure handling beyond the per-call failure mode in SS5.1.
     How does the feature behave when multiple services fail simultaneously?
     What is the minimum viable rendering? -->

| Scenario | Behavior | Minimum Viable Rendering |
|----------|----------|------------------------|
| Primary service (`{suite}-{domain}-svc`) down | Feature is non-functional; error page with retry | Error page |
| T1 ref-data-svc down | Form renders; dropdowns show codes instead of labels | Form without labels |
| T2 bp-svc down | Form renders; partner fields show IDs only | Degraded form |
| All optional enrichment services down | Core form renders with degraded display | Core fields only |

### 5.5 Caching Hints

<!-- Guidance for the BFF on what can be cached and for how long.
     These are HINTS, not mandates — the BFF implementation decides the final strategy. -->

| Data | Cache Strategy | TTL | Invalidation |
|------|---------------|-----|-------------|
| Reference data catalogs | Cache per tenant | 1 hour | On ref-data change event |
| Material master data | Cache per entity | 5 min | On material.updated event |
| {Data} | {Strategy} | {TTL} | {Invalidation trigger} |

### 5.6 i18n Keys

<!-- Key pattern: {suite}.F-{SUITE}-{NNN}-{NN}.{element}
     All keys resolved at runtime by i18n-svc. -->

| Key | Default (en) | Context |
|---|---|---|
| `{suite}.F-{SUITE}-{NNN}-{NN}.title` | `"{Feature Name}"` | Page title |
| `{suite}.F-{SUITE}-{NNN}-{NN}.empty` | `"No {entities} yet."` | Empty state |
| `{suite}.F-{SUITE}-{NNN}-{NN}.form.{field}.label` | `"{Label}"` | Field label |
| `{suite}.F-{SUITE}-{NNN}-{NN}.form.{field}.required` | `"{Label} is required."` | Validation error |
| `{suite}.F-{SUITE}-{NNN}-{NN}.action.{action}.confirm.title` | `"{Action} {entity}?"` | Dialog title |
| `{suite}.F-{SUITE}-{NNN}-{NN}.action.{action}.success` | `"{Entity} {past-tense action}."` | Toast |
| `{suite}.F-{SUITE}-{NNN}-{NN}.error.{svc}.unavailable` | `"{Svc} is currently unavailable."` | Service error |

---

## 6. Screen Contract (AUI)

<!-- This section defines the Abstract UI (AUI) for this feature, applying
     the Cameleon Reference Framework (Calvary et al., 2003).
     The AUI separates WHAT a screen IS from HOW it RENDERS.

     The AUI can be defined:
       (a) INLINE in this section as structured YAML, or
       (b) BY REFERENCE to the companion file F-{SUITE}-{NNN}-{NN}.aui.yaml

     If the AUI is simple (few zones, straightforward task model), inline it.
     If the AUI is complex, reference the companion file and summarize here.

     DERIVATION from Problem Space:
     - SS2 (User Journey) -> Task Model operators
     - SS3 (Form Fields + Actions) -> Zones
     - SS4 (Edge Cases) -> Absent-Rules + screen state definitions
     - SS0.4 (Variability) -> Feature-gated zones -->

### 6.1 Task Model

<!-- The task model defines the temporal relationships between user tasks.
     Five operators (from Cameleon Reference Framework):
     - sequential(A, B) — A must complete before B starts
     - concurrent(A, B) — A and B are co-accessible (side-by-side or tabs)
     - alternative(A | B) — mutual exclusion (one or the other)
     - optional(A) — A may be absent (triggers absent-rule)
     - enabling(B <- A) — B is disabled until A provides data -->

```yaml
# Task Model for F-{SUITE}-{NNN}-{NN}
task_model:
  root:
    operator: sequential
    children:
      - id: task-init
        label: "{Initial data loading / entry}"
      - id: task-main
        operator: concurrent
        children:
          - id: task-section-a
            label: "{Section A — primary data entry}"
          - id: task-section-b
            label: "{Section B — secondary data}"
            operator: optional
      - id: task-submit
        label: "{Submit / primary action}"
        operator: enabling
        enabled_by: task-main
```

### 6.2 Zones

<!-- Zones are named layout regions with priorities (not pixel sizes).
     Zone types:
     - fixed: owned by the platform shell, cannot be overridden
     - variable: product team integration slots
     - feature-gated: visible only when a feature flag is active

     Priority determines rendering order on constrained screens.
     Lower number = higher priority = rendered first. -->

```yaml
# Zones for F-{SUITE}-{NNN}-{NN}
zones:
  - id: zone-header
    type: fixed
    priority: 1
    binds_to: "record._meta"
    description: "Page title, status badge, action bar"

  - id: zone-section-a
    type: fixed
    priority: 2
    binds_to: "record.{sectionA fields}"
    description: "{Section A description}"

  - id: zone-section-b
    type: fixed
    priority: 3
    binds_to: "record.{sectionB fields}"
    description: "{Section B description}"

  - id: zone-extension
    type: variable
    priority: 10
    binds_to: "extensions.customFields"
    description: "Product-specific custom fields (extension zone)"

  - id: zone-actions
    type: fixed
    priority: 99
    binds_to: "record._meta.allowedActions"
    description: "Primary and secondary action buttons"
```

### 6.3 Absent-Rules

<!-- What happens when an optional zone is not rendered?
     Options:
     - collapse-up: predecessor expands to fill the space
     - end-page: page ends at previous zone
     - tab-hidden: tab removed from navigation
     - panel-section-hidden: panel section collapses -->

```yaml
# Absent-Rules for F-{SUITE}-{NNN}-{NN}
absent_rules:
  - zone: zone-section-b
    condition: "{When this zone is absent, e.g., attribute is false}"
    rule: collapse-up

  - zone: zone-extension
    condition: "No extension point filled for this product"
    rule: panel-section-hidden
```

### 6.4 Business Rules (AUI)

<!-- Screen-level business rules that affect rendering.
     Role gates: which roles see which zones/actions.
     State conditions: which status values enable which actions.
     Action consequences: what happens after an action succeeds. -->

```yaml
# Business Rules for F-{SUITE}-{NNN}-{NN}
business_rules:
  role_gates:
    - zone: zone-actions
      action: "{Primary CTA}"
      requires_role: "{ROLE}"

    - zone: zone-actions
      action: "Delete"
      requires_role: "{ROLE_ADMIN}"

  state_conditions:
    - action: "{Lifecycle action}"
      enabled_when: "record.status == '{STATE}'"

  action_consequences:
    - action: "{Primary CTA}"
      on_success: "navigate('{target}') + toast('{success key}')"
```

### 6.5 Extension Zones

<!-- Zones marked as `variable` in SS6.2 that products can fill.
     Each extension zone has a defined interface: what the product
     must provide when filling this zone.
     Extension zones follow the Open-Closed Principle (Meyer, 1988). -->

| Extension Zone | Description | Interface | Default (unfilled) |
|---------------|-------------|-----------|-------------------|
| `zone-extension` | Custom fields on the form | `(recordId) -> customFieldValues` | Hidden (absent-rule: panel-section-hidden) |

<!-- If the AUI is managed in the companion file, add a reference: -->
<!-- **Full AUI definition:** See `F-{SUITE}-{NNN}-{NN}.aui.yaml` -->

---

## 7. i18n, Permissions & Accessibility

### 7.1 Permissions

<!-- Role codes from _iam_suite.md.
     Platform default: actions the user's role cannot perform are absent from the DOM.
     Exception: show disabled with tooltip when the role permits the action in
     principle but a precondition (wrong status, not the owner) is unmet. -->

| Action / Element | Show when | Enable when | Required role(s) |
|---|---|---|---|
| Feature entry point | Always | Any product role | Any |
| `{Primary CTA}` | Always on form | Form valid | `{ROLE}` |
| `{Lifecycle action}` | Status = `{STATE}` | User holds `{ROLE}` | `{ROLE}` |
| `{Sensitive field}` | — | — | `{ROLE_MANAGER}+` |
| Delete | Status = `DRAFT` | — | `{ROLE_ADMIN}` only |

### 7.2 Accessibility Requirements

<!-- WCAG 2.1 AA compliance requirements specific to this feature.
     The platform provides baseline accessibility; list feature-specific needs here. -->

| Requirement | Implementation |
|------------|---------------|
| Keyboard navigation | All actions reachable via Tab; primary CTA via Enter |
| Screen reader | All form fields have associated labels; error messages announced via aria-live |
| Focus management | On validation error, focus moves to first invalid field |
| Color independence | Variance indicators (match/mismatch) use icons + text, not color alone |
| {Feature-specific} | {Implementation} |

---

## 8. Acceptance Criteria

<!-- Given / When / Then.
     One AC per scenario (SS1.2), per major edge case (SS4.2), per permission rule (SS7.1),
     per attribute-driven variation (SS4.3), and per feature-gating mode (SS5.3).
     These become sprint definition-of-done and QA test cases. -->

**AC-001: Happy path — {Scenario 1 name}**
- Given a user with role `{ROLE}` arrives at this feature via {entry point}
- When they {action sequence}
- Then {observable outcome — what is shown, saved, or emitted}

**AC-002: Validation — required field blank**
- Given the form is open and `{Field A}` is blank
- When the user submits
- Then `{Field A}` shows `"{error message}"` and receives focus

**AC-003: Permission — action absent from DOM**
- Given the user has role `{ROLE_VIEWER}`
- When they access this feature
- Then `{action}` is not present in the DOM *(not merely CSS-hidden)*

**AC-004: Concurrent modification**
- Given this feature is open for editing
- When another user saves the same record (API returns 412 on this user's save)
- Then the form stays open
- And a banner reads: "This record was updated by another user. Reload."

**AC-005: Service unavailable**
- Given `{svc}` is unavailable when the feature loads
- When the feature renders
- Then `{svc}`-dependent fields are disabled
- And a warning banner names the affected fields

**AC-006: Attribute — {attribute name}**
- Given the ProductConfig sets `{attr}` = `{non-default value}`
- When the user `{relevant action}`
- Then `{observable change from SS4.3}`

**AC-007: Feature-gating — read-only mode**
- Given this feature is configured as `read-only` in the ProductConfig
- When the user opens the feature
- Then all data is displayed but submit/action buttons are hidden
- And the BFF does not expose write endpoints

**AC-008: Feature-gating — excluded mode**
- Given this feature is configured as `excluded` in the ProductConfig
- When the user navigates to the application
- Then this feature's entry point is not visible

**AC-009: Extension zone — unfilled**
- Given no product has filled the `zone-extension` extension point
- When the feature renders
- Then the extension zone is hidden (absent-rule: panel-section-hidden)

**AC-010: Extension zone — filled**
- Given a product has filled the `zone-extension` extension point
- When the feature renders
- Then the extension zone is visible with the product-specific content

---

## 9. Dependencies, Variability & Extension Points

<!-- This section is the source of truth for the companion .uvl file.
     It bridges the problem space (what varies) with the solution space
     (how it is formally modeled). -->

### 9.1 Feature Dependencies (UVL `requires`)

<!-- Every cross-suite access creates a feature dependency.
     These become `requires` constraints in the companion .uvl file.
     Rule: features use other features, not services directly. -->

| Required Feature | Suite | Access Type | Reason |
|-----------------|-------|------------|--------|
| `F-{OTHER}-{NNN}` | `{other}` | READ_ONLY | {e.g., "Supplier data lookup"} |

### 9.2 Attributes (UVL)

<!-- Each attribute from SS0.4 formalized with its UVL declaration.
     Binding times: compile (build-time), deploy (deployment config), runtime (tenant/user toggle).
     These become `attributes` in the companion .uvl file. -->

| Attribute | Type | Default | Binding Time | UVL Declaration |
|-----------|------|---------|-------------|----------------|
| `{attr}` | `Boolean` / `Integer` / `String` | `{default}` | `deploy` | `{attr} Boolean {default}` |
| `{attr}` | `Integer` | `{default}` | `deploy` | `{attr} Integer {default}` |

### 9.3 Extension Points

<!-- Platform-features define explicit hooks where product-level customization is allowed.
     Extension points follow the Open-Closed Principle (Meyer, 1988):
     platform assets are open for extension but closed for modification.
     Products extend at defined points; they do not fork or modify platform code.

     Extension point types:
     - Extension zones: additional form sections, custom panels (defined in SS6.5)
     - Extension rule slots: custom validation rules beyond the platform's rules
     - Extension action slots: custom buttons, menu items
     - Extension event hooks: custom handlers for domain events -->

| Extension Point ID | Type | Description | Interface | Default Behavior |
|-------------------|------|-------------|-----------|-----------------|
| `ext.{name}` | zone / rule / action / event-hook | {What can be customized} | `{Input -> Output signature}` | {What happens if not filled} |
| `ext.customFields` | zone | Additional fields on the form | `(recordId) -> customFieldValues` | Zone hidden |
| `ext.{name}` | event-hook | Custom handler for `{event}` | `(eventPayload) -> void` | No-op |

### 9.4 UVL Companion Summary

<!-- Summary of what the companion .uvl file should contain.
     Write this section, then derive the .uvl file from it. -->

```uvl
features
    F-{SUITE}-{NNN}-{NN} "{Feature Name}"
        {mandatory | optional}
        attributes
            {attr1} {Type} {default}
            {attr2} {Type} {default}

constraints
    F-{SUITE}-{NNN}-{NN} requires F-{OTHER}-{NNN}
```

---

## 10. Change Log & Review

### 10.1 Open Questions & Decisions

| ID | Question | Impact | Owner | Needed by |
|---|---|---|---|---|
| Q-001 | {Question} | {Problem space or solution space impact} | {Name} | {Date} |

#### Decision Log

**D-001: {Title}**
- **Decided:** {Date} by {Name}
- **Decision:** {What}
- **Rationale:** {Why}
- **Rejected alternatives:** {What else was considered and why it lost}

### 10.2 Change Log

| Date | Version | Author | Changes |
|---|---|---|---|
| YYYY-MM-DD | 1.0 | {Name} | Initial spec |

### 10.3 Review & Approval

**Status:** [DRAFT | REVIEW | APPROVED | DEPRECATED]

**Reviewers:**
- Suite UX Lead: {Name} — {Date}
- Frontend Lead ({suite}): {Name} — {Date}
- Backend Lead (primary domain): {Name} — {Date}
- BFF Lead: {Name} — {Date}

**Approval:**
- Suite UX Lead: {Name} — {Date} — [ ] Approved
- Frontend Lead: {Name} — {Date} — [ ] Approved
- Backend Lead: {Name} — {Date} — [ ] Approved

---

## Authoring Checklist

> Before moving to REVIEW status, verify:

### Problem Space (SS0-SS4)
- [ ] Non-goals written before requirements (SS0.2)
- [ ] Entry and exit points documented (SS0.3)
- [ ] Variability points table (SS0.4) matches companion `.uvl` attribute schema
- [ ] User goal stated as a goal, not a task (SS1.1)
- [ ] 2-5 scenarios cover mainstream use, not just happy path (SS1.2)
- [ ] Sequence diagram covers happy path, validation failure, and system error (SS2.1)
- [ ] Screen layouts show all distinct states (SS2.2)
- [ ] Extension zones marked with [EXT] in layouts (SS2.2)
- [ ] Attribute-driven behaviour changes documented for every attribute (SS4.3)

### Solution Space (SS5-SS6)
- [ ] Service calls table complete — every call in the journey is listed (SS5.1)
- [ ] Each call has tier, isMutation flag, and failure mode (SS5.1)
- [ ] Cross-suite requires in SS5.1 match `constraints` block in `.uvl` (SS5.1)
- [ ] BFF view-model shape documented with JSON-C and source annotations (SS5.2)
- [ ] Feature-gating rules defined for full, read-only, and excluded modes (SS5.3)
- [ ] Failure modes documented for multi-service failure scenarios (SS5.4)
- [ ] Caching hints provided for BFF (SS5.5)
- [ ] i18n keys defined for every user-visible string (SS5.6)
- [ ] AUI task model operators derived from SS2 sequence diagram (SS6.1)
- [ ] AUI zones derived from SS3 form sections (SS6.2)
- [ ] AUI absent-rules derived from SS4 edge cases (SS6.3)
- [ ] Extension zones defined in SS6.5 with interfaces (SS6.5)

### Bridge Artifacts (SS7-SS9)
- [ ] Permission matrix covers all actions and roles (SS7.1)
- [ ] Accessibility requirements documented (SS7.2)
- [ ] One AC per scenario, edge case, permission, attribute variation, and gating mode (SS8)
- [ ] Feature dependencies formalized with UVL `requires` (SS9.1)
- [ ] All attributes have binding times (SS9.2)
- [ ] Extension points have interfaces and default behavior (SS9.3)
- [ ] Companion `.uvl` created and validated (SS9.4)
- [ ] Companion `.aui.yaml` created (or AUI inlined in SS6) (SS6)
- [ ] Leaf added to parent composition node's `.uvl` children
- [ ] Catalog validator passes
