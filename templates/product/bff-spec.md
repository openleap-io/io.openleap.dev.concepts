<!-- Template Meta
     Template-ID:   TPL-BFF
     Version:       1.0.0
     Last Updated:  2026-04-03
     Changelog:
       1.0.0 (2026-04-03) — Initial versioned baseline.
-->

# {Product Name} — {SUITE} BFF Specification

> **Conceptual Stack Layer:** Product (BFF)
> **Space:** Product (Application Engineering)
> **Owner:** Product Engineering Team
> **Scope:** One BFF per suite per product. This spec covers the `{suite}` suite's
>   features as selected by the `{product-id}` product. If a product uses multiple
>   suites, each suite gets its own BFF spec instance.
> **Companion files:** `product-config.uvl`, `server/schemas/{suite}/*.ts`, `server/api/v1/{suite}/**/*.ts`
> **Referenced by:** Product Spec, Suite Feature Specs
> **Governance:** `concepts/governance/bff-guideline.md` (GOV-BFF-001)

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `bff-spec.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections or "fully compliant"}
> - **Author(s):** Name(s)
> - **Status:** [DRAFT | REVIEW | APPROVED | DEPRECATED]
> - **Product:** `{product-id}` (e.g., `iam-console`, `demo`)
> - **Suite:** `{suite}` (the single suite this BFF covers)
> - **Product Config:** `{product-id}.product-config.uvl`
> - **Gateway URL:** `{gateway-url}` (default: `http://localhost:8090`)
> - **BFF Base Path:** `/api/v1/{suite}/`

---

## 0. Product & Suite Context

<!-- Describe which product this BFF serves, which suite(s) it draws features from, and the product-config.uvl that defines feature selection. -->

### 0.1 Product Identity

| Field | Value |
|-------|-------|
| Product | `{product-id}` |
| Product UI Repo | `io.openleap.{product}.ui` |
| Source Suite(s) | `{suite1}`, `{suite2}` |
| Product Config | `{product-id}.product-config.uvl` |

### 0.2 Suite Services

| Service ID | Suite | Port | API Base | Responsibility |
|-----------|-------|------|----------|----------------|
| `{suite}-{domain}-svc` | `{suite}` | `{port}` | `/api/v1/{suite}/{domain}` | {Primary responsibility} |

---

## 1. Selected Features

<!-- Table of features selected in product-config.uvl, with their gating mode and the BFF routes they map to. -->

| Feature ID | Name | Gating Mode | BFF Route | Backend Service |
|-----------|------|------------|-----------|----------------|
| `F-{SUITE}-{NNN}-{NN}` | {Feature Name} | `full` / `read-only` / `excluded` | `{METHOD} /api/v1/{suite}/{path}` | `{suite}-{domain}-svc` |

---

## 2. Route Catalog

<!-- Complete mapping of BFF routes to features, Zod schemas, and backend endpoints. -->

### 2.1 Route Table

| Route | Method | Feature | Zod Schema | Backend Endpoint | Description |
|-------|--------|---------|-----------|-----------------|-------------|
| `/api/v1/{suite}/{domain}/{resource}` | `GET` | `F-{SUITE}-{NNN}-{NN}` | `{SchemaName}` | `{backend-path}` | {description} |

### 2.2 Route Handler Pattern

<!-- All route handlers MUST follow this pattern: -->

```typescript
// server/api/v1/{suite}/{domain}/{resource}.{method}.ts
import { {RequestSchema} } from '~/server/schemas/{domain}'

export default defineEventHandler(async (event) => {
  const body = await readValidatedBody(event, {RequestSchema}.parse)
  return mockOrProxy(event, {
    mock: () => ({
      // Mock response matching service response shape
    }),
    proxy: { path: '{backend-endpoint}', body },
  })
})
```

---

## 3. Zod Schema Catalog

<!-- List all Zod schemas used by BFF routes, organized by domain. -->

### 3.1 Schema Files

| File | Domain | Schemas |
|------|--------|---------|
| `server/schemas/{domain}.ts` | `{domain}` | `{Schema1}`, `{Schema2}`, ... |

### 3.2 Shared Enums

| Enum | Values | Used By |
|------|--------|---------|
| `{EnumName}` | `{value1}`, `{value2}`, ... | `{Schema1}`, `{Schema2}` |

---

## 4. Orchestration Flows

<!-- Features requiring multi-service calls. Each flow follows the AUI contract's task model. -->

### 4.1 {Feature Name} (F-{SUITE}-{NNN}-{NN})

**Task Model:** {task1} → {task2} → {task3} (from AUI contract)

**Sequence:**

```
UI → BFF: {request}
BFF → Service A: {call 1}
Service A → BFF: {response 1}
BFF → Service B: {call 2}
Service B → BFF: {response 2}
BFF → UI: {composed response with _meta}
```

---

## 5. View Model Contracts

<!-- TypeScript interfaces for BFF responses. Every response includes _meta. -->

### 5.1 Response Envelope

```typescript
interface BffResponse<T> {
  data: T
  _meta: {
    featureId: string
    featureMode: 'full' | 'read-only' | 'excluded'
    allowedActions: string[]
    zones: string[]
  }
}
```

### 5.2 Domain View Models

<!-- One interface per domain response type -->

```typescript
// {Domain} view models
interface {ViewModelName} {
  // Fields from service response
}
```

---

## 6. Security

### 6.1 Feature-to-Role Mapping

| Feature ID | Required Role | Action |
|-----------|--------------|--------|
| `F-{SUITE}-{NNN}-{NN}` | `{ROLE_NAME}` | Submit / Read / Admin |

### 6.2 Field-Level Filtering Rules

| Field | Read Permission | Write Permission | Service |
|-------|----------------|-----------------|---------|
| `{fieldName}` | `{permission}` or `null` (public) | `{permission}` | `{service}` |

---

## 7. Mock Data

### 7.1 Mock File Structure

```
server/mock/
  {domain}/
    {resource}.ts    — Mock data factory
    index.ts         — Re-exports
```

### 7.2 Mock Data Conventions

- Mock data MUST match the same response shape as real services
- Use `crypto.randomUUID()` for IDs
- Use realistic-looking data (not "test123")
- Dates should be relative to `new Date()`

---

## 8. Configuration

### 8.1 Runtime Config

| Key | Env Var | Default | Purpose |
|-----|---------|---------|---------|
| `runtimeConfig.useMocks` | `NUXT_USE_MOCKS` | `true` | Toggle mock/real mode |
| `runtimeConfig.gatewayUrl` | `NUXT_GATEWAY_URL` | `http://localhost:8090` | Backend API gateway |

### 8.2 Feature Flags

| Flag | Default | Controls |
|------|---------|----------|
| `feature.{SUITE}.{NNN}.{NN}` | from UVL | Feature gating mode override |

---

## 9. Error Handling

| Service Error | BFF Status | Response Shape | UI Behavior |
|---|---|---|---|
| 400 Validation | 400 | `{ errors: [{ field, message }] }` | Inline field errors |
| 403 Forbidden | 403 | `{ error: 'forbidden', requiredRole }` | Permission denied message |
| 422 Business Rule | 422 | `{ error: 'business_rule', code, message }` | Banner with message |
| 503 Unavailable | 503 | `{ error: 'service_unavailable', retryAfter }` | Error with retry |
| Timeout | 504 | `{ error: 'timeout' }` | Error with retry |

---

## 10. Open Questions

| ID | Question | Impact | Owner |
|---|---|---|---|
| `{id}` | {Question} | {Impact} | {Owner} |
