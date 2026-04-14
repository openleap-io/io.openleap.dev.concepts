# BFF Guideline — Feature-Driven Backend for Frontend

> **Document Type:** Product Governance
> **ID:** GOV-BFF-001
> **Version:** 1.0 — 2026-04-03
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** All product UIs that expose suite features through a BFF layer

---

## 1. Purpose

OpenLeap follows an SPLE (Software Product Line Engineering) architecture with
a strict separation between platform services (Domain Engineering) and product
applications (Application Engineering). Platform suites expose domain-centric
APIs — each microservice owns a bounded context and publishes endpoints aligned
with its aggregate model. Product UIs, however, need **feature-centric,
screen-optimized** endpoints that match the user's task model, not the domain
model.

Without a dedicated intermediary, the product UI must:

- orchestrate calls to multiple backend services for a single screen,
- enforce field-level security filtering in the browser,
- implement feature gating logic in client-side code,
- transform domain responses into UI view models.

This violates separation of concerns and couples the UI directly to service
internals. The **Backend for Frontend** (BFF) pattern resolves this by
introducing a thin, product-specific API layer that bridges platform services
and the product UI.

This document defines the normative rules for designing, implementing, and
operating BFF layers in OpenLeap product UIs.

---

## 2. What a BFF Is (and Isn't)

### 2.1 A BFF IS

- A **feature-scoped API layer** that exposes exactly the endpoints a
  product's selected features require.
- **Product-specific** — each product has its own BFF tailored to its feature
  selection and persona model.
- **Collocated with the UI** — it lives inside the product UI repository as
  Nitro server routes and shares the same Node.js runtime process.
- **Responsible for** input validation, feature gating, field-level security
  filtering, service orchestration, response transformation, and mock support.

### 2.2 A BFF IS NOT

- **Not a general API gateway.** The platform API gateway (reverse proxy,
  rate limiting, TLS termination) is a separate infrastructure concern. The
  BFF sits behind the gateway.
- **Not a shared backend service.** Each product has its own BFF. Two products
  MUST NOT share a BFF, even if they select overlapping features, because
  their persona models, field visibility rules, and orchestration needs
  differ.
- **Not where business logic lives.** Domain logic stays in the platform
  microservices. The BFF MUST NOT contain aggregate rules, state transitions,
  or persistence — it delegates to services and transforms results.
- **Not a data aggregation layer.** The BFF does not maintain its own data
  store. It is stateless with respect to business data; any caching is
  short-lived and request-scoped.

---

## 3. Architecture — Where the BFF Sits

```
┌───────────────────────────────────────────────────────────┐
│  Browser                                                  │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Product UI (Nuxt 4 / Vue 3)                        │  │
│  │  Pages, Components, Composables                     │  │
│  └──────────────────────┬──────────────────────────────┘  │
└─────────────────────────┼─────────────────────────────────┘
                          │  fetch('/api/v1/{suite}/...')
                          ▼
┌───────────────────────────────────────────────────────────┐
│  Nitro Server (same Node.js process as SSR/CSR)           │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  BFF Layer                                          │  │
│  │  server/api/v1/{suite}/{domain}/{resource}.{verb}.ts│  │
│  │                                                     │  │
│  │  Responsibilities:                                  │  │
│  │  · Input validation (Zod)                           │  │
│  │  · Feature gating                                   │  │
│  │  · Field-level security filtering                   │  │
│  │  · Service orchestration                            │  │
│  │  · Response transformation (_meta envelope)         │  │
│  │  · Mock support                                     │  │
│  └──────────────────────┬──────────────────────────────┘  │
└─────────────────────────┼─────────────────────────────────┘
                          │  HTTP (via gateway)
                          ▼
┌───────────────────────────────────────────────────────────┐
│  Platform API Gateway                                     │
│  (routing, rate limiting, TLS, auth token forwarding)     │
└──────────────────────┬────────────────────────────────────┘
                       │
           ┌───────────┼───────────┐
           ▼           ▼           ▼
      ┌─────────┐ ┌─────────┐ ┌─────────┐
      │ Service │ │ Service │ │ Service │
      │  (T1)   │ │  (T2)   │ │  (T3)   │
      └─────────┘ └─────────┘ └─────────┘
```

### 3.1 Architectural Constraints

| ID | Constraint | Rationale |
|---|---|---|
| AC-01 | The BFF MUST live in `server/api/v1/{suite}/` inside the product UI repository. | Collocated with the UI; shared runtime config; no separate deployment artifact. |
| AC-02 | There MUST be one BFF per suite per product. | A product that consumes features from N suites has N BFFs, each scoped to one suite's features. Each BFF exposes only the subset of that suite's features selected by the product's UVL config. |
| AC-03 | A BFF MUST NOT call another BFF. | BFF-to-BFF communication creates hidden coupling between products. If shared data is needed, it MUST be obtained from platform services. |
| AC-04 | A BFF MUST NOT contain domain logic. | Business rules, aggregate invariants, and state transitions belong in platform microservices. The BFF delegates and transforms. |
| AC-05 | A BFF MUST NOT maintain its own persistent data store. | The BFF is a stateless pass-through layer. Caching, if needed, MUST be short-lived and request-scoped. |
| AC-06 | All BFF-to-service communication MUST go through the platform API gateway. | Ensures consistent auth token forwarding, rate limiting, and observability. |

---

## 4. Feature Selection — Product Config UVL

The BFF's API surface is determined by the product's **feature selection**, not
by what the platform offers. The mapping works as follows:

1. **Platform suites** declare all available features in their
   `{suite}.catalog.uvl` files. Each leaf feature (e.g., `F-IAM-001-01`)
   has a feature spec, a UVL declaration, and an AUI screen contract.

2. **The product** defines its selection in `product-config.uvl`. This file
   imports the platform catalogs and selects a subset of features.

3. **The BFF** exposes routes ONLY for selected features. The relationship
   is deterministic:

   | Product Config | BFF Behavior |
   |---|---|
   | `F-IAM-001-01` selected | `POST /api/v1/iam/principals` route exists |
   | `F-IAM-001-02` selected | `GET /api/v1/iam/principals` route exists |
   | `F-IAM-001-04` NOT selected | No device principal route; requests return 404 |

4. **Feature selection is compile-time.** The `product-config.uvl` is
   resolved at build time. The BFF does not evaluate UVL constraints at
   runtime. Routes for unselected features MUST NOT be present in the
   built artifact.

### 4.1 Multi-Suite Products

A product MAY select features from multiple suites. In this case the BFF
contains route trees for each suite:

```
server/api/v1/iam/...       ← from IAM suite
server/api/v1/fi/...        ← from FI suite
server/api/v1/hr/...        ← from HR suite
```

The product's `product-config.uvl` is the single source of truth for which
suite features are included.

---

## 5. The Six BFF Responsibilities

Every BFF route handler addresses up to six responsibilities. Not every route
requires all six, but the BFF layer as a whole MUST support all of them.

### 5.1 Input Validation

The BFF MUST validate all incoming request bodies before forwarding to
backend services. Validation uses **Zod schemas** derived from the
service's OpenAPI contract.

**Rules:**

- Each write endpoint (POST, PUT, PATCH) MUST define a Zod schema.
- The schema MUST match the backend service's OpenAPI request body
  definition. Schema drift is a build-time error.
- Validation failure MUST return HTTP `400` with a structured error
  response that maps field paths to error messages, enabling inline
  form-level error display in the UI.

**Error response format:**

```json
{
  "status": 400,
  "code": "VALIDATION_ERROR",
  "errors": [
    { "field": "email", "message": "Must be a valid email address" },
    { "field": "displayName", "message": "Required" }
  ]
}
```

### 5.2 Feature Gating

Each BFF route operates in one of three **feature modes**, as defined in
the feature spec (section 5 — Feature Attributes):

| Mode | Behavior |
|---|---|
| `full` | All fields editable, submit actions enabled. |
| `read-only` | Fields rendered as disabled, submit actions hidden. Response includes data but `_meta.allowedActions` is empty. |
| `excluded` | Route returns `404 Not Found`, or the UI entry point is hidden entirely. |

The effective mode is computed from three inputs:

1. **Feature selection** (UVL) — is the feature selected at all?
2. **User roles** — does the user have the required permissions?
3. **Runtime feature flags** — is the feature toggled off for gradual rollout?

The BFF MUST resolve the mode before executing any service call. If the mode
is `excluded`, the BFF MUST short-circuit with a `404` response. If the mode
is `read-only`, the BFF MUST suppress write operations and strip mutation
actions from the `_meta` envelope.

### 5.3 Field-Level Security Filtering

Per ONT-SBT-172 (field-level access control), platform services return the
full state of an entity. The BFF is responsible for filtering fields based
on the requesting user's roles.

**Rules:**

- Each field definition in the feature spec declares `readPermission` and
  `writePermission` attributes.
- The BFF MUST check the user's roles (extracted from the auth token)
  against each field's permissions.
- Fields the user is not permitted to read MUST be **removed** from the
  response — not nullified, not masked, but absent from the JSON payload.
- Fields the user is not permitted to write MUST be **stripped** from write
  request bodies before forwarding to the service, preventing privilege
  escalation.
- The `_meta` envelope SHOULD include a `visibleFields` list so the UI
  knows which fields to render without inspecting the data payload.

### 5.4 Service Orchestration

Some features require the BFF to call multiple backend services and compose
a unified response. The BFF is the orchestration point for these multi-service
interactions.

**Examples:**

- `F-IAM-001-01` (Create Human Principal): calls `principal-svc` to create
  the principal record, then triggers a Keycloak sync via the IAM
  integration service.
- A dashboard feature might call `fi-svc` for financial summaries and
  `hr-svc` for headcount data, composing both into a single response.

**Rules:**

- Orchestration MUST follow the feature's AUI task model. If the task model
  defines sequential steps (`load-data` then `validate-and-submit`), the
  BFF MUST execute them in order. If steps are independent, they SHOULD
  execute in parallel.
- If any downstream service call fails, the BFF MUST apply the error
  handling rules in section 8 and MUST NOT return a partial success as if
  it were a full success.
- The BFF MUST NOT implement saga logic. Cross-service transactions that
  require compensating actions belong in platform-level saga orchestrators
  (per ADR-029).

### 5.5 Response Transformation

The BFF transforms service responses into **UI view models** wrapped in a
`_meta` envelope. This envelope enables the UI to render any feature
generically without hardcoded feature-specific logic.

**Standard response format:**

```json
{
  "data": {
    "principalId": "01J8...",
    "displayName": "Jane Doe",
    "email": "jane.doe@example.com",
    "status": "ACTIVE"
  },
  "_meta": {
    "featureId": "F-IAM-001-01",
    "featureMode": "full",
    "allowedActions": ["Submit", "Cancel"],
    "zones": ["zone-header", "zone-core", "zone-extension", "zone-actions"],
    "visibleFields": ["principalId", "displayName", "email", "status"]
  }
}
```

**Rules:**

- Every BFF response MUST include the `_meta` envelope at the top level.
- `_meta.featureId` MUST match the feature ID from the feature spec.
- `_meta.featureMode` MUST reflect the resolved mode (see section 5.2).
- `_meta.allowedActions` MUST list only the actions the current user may
  perform given the feature mode and their roles.
- `_meta.zones` MUST list the AUI zones that are active for this response,
  enabling the UI to show or hide layout regions.
- The `data` field contains the transformed service response. Field names
  SHOULD match the AUI contract field identifiers, not the raw service
  response field names, if they differ.

### 5.6 Mock Support

The BFF MUST support a mock mode for local UI development without running
backend services.

**Rules:**

- A runtime flag `useMocks` (default: `true` in development, `false` in
  production) controls whether the BFF returns mock data or proxies to real
  services.
- Mock data MUST reside in the `server/mock/` directory within the product
  UI repository.
- Mock responses MUST use the same response shape (including the `_meta`
  envelope) as real service responses. This ensures the UI code path is
  identical in both modes.
- Mock data SHOULD cover both success and error scenarios to support UI
  error state development.
- The `mockOrProxy` utility function (see section 6) is the standard
  mechanism for switching between modes.

---

## 6. Route Structure Convention

### 6.1 File Naming

BFF routes follow the Nitro file-based routing convention:

```
server/api/v1/{suite}/{domain}/{resource}.{method}.ts
```

Where:

- `{suite}` — lowercase suite short code (e.g., `iam`, `fi`, `hr`)
- `{domain}` — lowercase domain short code within the suite
- `{resource}` — REST resource name (plural nouns)
- `{method}` — HTTP method (`.get`, `.post`, `.patch`, `.delete`)

### 6.2 Example Route Tree (IAM)

```
server/api/v1/iam/principals/index.get.ts       → List principals
server/api/v1/iam/principals/index.post.ts      → Create human principal
server/api/v1/iam/principals/[id].get.ts        → Get principal detail
server/api/v1/iam/principals/[id].patch.ts      → Update principal
server/api/v1/iam/principals/[id].delete.ts     → Deactivate principal
server/api/v1/iam/authz/roles/index.get.ts      → List roles
server/api/v1/iam/authz/roles/[id].get.ts       → Get role detail
```

### 6.3 Route Handler Pattern

Every route handler MUST follow this canonical structure:

```typescript
import { z } from 'zod'
import { mockOrProxy } from '~/server/utils/bff'

const CreatePrincipalSchema = z.object({
  displayName: z.string().min(1),
  email: z.string().email(),
  type: z.enum(['HUMAN', 'SYSTEM']),
})

export default defineEventHandler(async (event) => {
  // 1. Validate input
  const body = await readValidatedBody(event, CreatePrincipalSchema.parse)

  // 2. Resolve feature mode (gating)
  const mode = await resolveFeatureMode(event, 'F-IAM-001-01')
  if (mode === 'excluded') {
    throw createError({ statusCode: 404 })
  }

  // 3. Mock or proxy to real service
  return mockOrProxy(event, {
    mock: () => import('~/server/mock/iam/create-principal.json'),
    proxy: {
      path: '/api/v1/iam/principals',
      method: 'POST',
      body,
    },
    featureId: 'F-IAM-001-01',
    featureMode: mode,
  })
})
```

The `mockOrProxy` utility:

- Checks the `useMocks` runtime flag.
- If mocking, returns the mock data wrapped in the `_meta` envelope.
- If proxying, forwards the request to the gateway URL, applies field-level
  security filtering to the response, and wraps the result in the `_meta`
  envelope.

---

## 7. Relationship to AUI Contracts

Each leaf feature has an AUI (Abstract UI) contract file
(`F-{SUITE}-{NNN}-{NN}.aui.yaml`) that defines the screen's structure
independently of any concrete UI framework. The BFF implements the backend
side of this contract.

### 7.1 Task Model Mapping

The AUI contract defines a **task model** — the sequence of steps the user
performs to complete a feature's use case. The BFF implements the
server-side steps:

| AUI Task Step | BFF Implementation |
|---|---|
| `load-reference-data` | GET endpoint returning dropdown options, catalogs, and lookup values. |
| `load-entity` | GET endpoint returning the entity with field filtering and `_meta`. |
| `validate-and-submit` | POST/PATCH endpoint with Zod validation, service proxy, and `_meta` response. |
| `confirm-action` | POST endpoint for destructive actions (e.g., deactivation) with additional confirmation semantics. |

### 7.2 Zone Mapping

The AUI contract defines **zones** — logical layout regions (e.g., `header`,
`core`, `extension`, `actions`). The BFF communicates which zones are active
via `_meta.zones`:

- A zone is active if the current user has permission to see at least one
  field in that zone.
- A zone is inactive if all its fields are filtered out by field-level
  security.
- The UI uses `_meta.zones` to hide empty layout regions rather than
  rendering empty containers.

### 7.3 Business Rule Mapping

The AUI contract declares business rules (e.g., "Submit button enabled only
when all required fields are filled"). The BFF enforces the server-side aspect:

- `_meta.allowedActions` reflects which actions the server will accept.
- The UI SHOULD disable actions not in `allowedActions` but MUST NOT rely
  solely on client-side enforcement — the BFF MUST reject disallowed actions
  with a `403` or `422` response.

---

## 8. Error Handling

The BFF translates backend service errors into structured responses that the
UI can render consistently. The UI MUST NOT parse raw service error bodies
directly — the BFF normalizes them.

### 8.1 Error Translation Table

| Service Error | BFF Response Code | BFF Response Body | UI Behavior |
|---|---|---|---|
| `400 Bad Request` | `400` | `{ code: "VALIDATION_ERROR", errors: [...] }` | Inline field-level errors |
| `403 Forbidden` | `403` | `{ code: "FORBIDDEN", featureId, featureMode }` | "Insufficient permissions" banner |
| `404 Not Found` | `404` | `{ code: "NOT_FOUND", resource, id }` | "Resource not found" with navigation back |
| `409 Conflict` | `409` | `{ code: "CONFLICT", message }` | Conflict resolution prompt |
| `422 Business Rule` | `422` | `{ code: "BUSINESS_RULE", rule, message }` | Banner with business rule message |
| `503 Unavailable` | `503` | `{ code: "SERVICE_UNAVAILABLE", retryAfter }` | Error banner with retry button |
| Timeout | `504` | `{ code: "GATEWAY_TIMEOUT" }` | Error banner with retry button |
| Unknown / 5xx | `502` | `{ code: "UPSTREAM_ERROR" }` | Generic error banner |

### 8.2 Error Handling Rules

- The BFF MUST NOT expose internal service error details (stack traces,
  internal identifiers) to the UI.
- The BFF MUST log the full upstream error for observability before
  returning the sanitized response.
- For orchestrated calls (section 5.4), if one service fails, the BFF MUST
  return the error from the failing step and MUST NOT return partial
  success data alongside the error.

---

## 9. Configuration

The BFF is configured through Nuxt runtime configuration. All BFF-related
settings live under the server-side `runtimeConfig` to prevent accidental
exposure to the browser.

### 9.1 Runtime Config

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    // BFF settings (server-side only)
    useMocks: process.env.NUXT_USE_MOCKS !== 'false',
    gatewayUrl: process.env.NUXT_GATEWAY_URL || 'http://localhost:8090',
  },
})
```

| Setting | Type | Default | Description |
|---|---|---|---|
| `useMocks` | `boolean` | `true` (dev) / `false` (prod) | Toggle between mock and proxy mode. |
| `gatewayUrl` | `string` | `http://localhost:8090` | Base URL of the platform API gateway. All service calls are routed through this gateway. |

### 9.2 Environment Variables

| Variable | Maps To | Required In |
|---|---|---|
| `NUXT_USE_MOCKS` | `runtimeConfig.useMocks` | Development (optional — defaults to `true`) |
| `NUXT_GATEWAY_URL` | `runtimeConfig.gatewayUrl` | Staging, Production |

### 9.3 Configuration Rules

- The BFF MUST NOT hardcode service URLs. All upstream communication goes
  through `gatewayUrl`.
- The `useMocks` flag MUST default to `true` in development to support
  UI-first development without running backend services.
- In production, `useMocks` MUST be `false`. The CI/CD pipeline SHOULD
  enforce this via environment variable injection.

---

## 10. Decisions

| ID | Decision | Rationale |
|---|---|---|
| D-001 | BFF lives in the product UI repository | Collocated with the UI; shares Nitro runtime and config; eliminates a separate deployment artifact. The BFF lifecycle is tied to the product UI, not to the platform. |
| D-002 | Feature selection via `product-config.uvl` | Consistent with the SPLE model. The UVL file is the single source of truth for which features a product includes. BFF routes are a direct projection of this selection. |
| D-003 | Zod for input validation | Type-safe at runtime; generates TypeScript types for compile-time safety; integrates natively with Nitro's `readValidatedBody`. Schema definitions serve as living documentation of the API contract. |
| D-004 | `_meta` response envelope | Enables the UI to render any feature generically based on metadata (mode, zones, actions) without hardcoded per-feature rendering logic. Supports the AUI-driven rendering pipeline. |
| D-005 | Mock support via runtime flag | Enables parallel UI and backend development. UI developers can build and test screens without waiting for service implementations. Mock data validates that the UI handles the expected response shape. |
| D-006 | One BFF per suite per product | A product has one BFF for each suite it consumes. Each BFF contains only the routes for features selected in the product's UVL config — not the full suite. Two products using the same suite will have separate BFF instances with potentially different feature subsets. This preserves product independence while keeping each BFF focused on a single suite's domain. |
| D-007 | BFF-to-service calls go through the gateway | Centralizes auth token forwarding, rate limiting, service discovery, and observability. The BFF does not need to know individual service URLs. |
| D-008 | No BFF-to-BFF communication | Products are independent derivation units. If product A needs data that product B's BFF computes, both must obtain it from the underlying platform service. |
