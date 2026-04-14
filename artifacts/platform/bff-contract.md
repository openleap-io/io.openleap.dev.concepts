# Artifact: BFF Contract

> **Space:** Platform (Domain Engineering) — bridges to Product Space at runtime
> **Conceptual Stack Layer:** BFF (Backend for Frontend)
> **Source:** Conceptual Stack §4.6

---

## Definition

A BFF Contract specifies how the Backend for Frontend serves a specific platform-feature. It is derived from the Platform-Feature Spec §5 (Backend Dependencies) and §5.2 (View-Model Shape). The BFF Contract defines which domain services are called, how responses are aggregated into a view model, how feature-gating modifies behavior per product mode, where extension point calls are routed, and what happens when services fail. The BFF is not a free-form integration layer — its behavior is specified by the platform-features it serves.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| featureId | string | ✓ | Parent Platform-Feature ID (e.g., F-PPS-012) |
| aggregatedServices | ServiceCall[] | ✓ | Which domain services are called for this feature |
| viewModelShape | JSON-C | ✓ | BFF response structure — the frontend's contract (from Feature Spec §5.2) |
| featureGatingRules | GatingRule[] | ✓ | How BFF behavior changes per feature mode |
| extensionRouting | ExtensionRoute[] | | Where extension point calls are routed |
| failureModes | FailureMode[] | | What happens when a service is unavailable |
| cachingHints | CacheHint[] | | Which responses can be cached and for how long |

### aggregatedServices (per service call)

Derived from Feature Spec §5 Backend Dependencies.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| serviceId | string | ✓ | Domain service ID (e.g., pps-im-svc, fi-gl-svc, bp-svc) |
| endpoint | string | ✓ | API endpoint path (e.g., GET /api/pps/im/v1/stock/{materialId}) |
| method | enum | ✓ | GET, POST, PUT, PATCH, DELETE |
| tier | enum | ✓ | T1, T2, T3 — determines dependency governance |
| isMutation | boolean | ✓ | true = write operation, false = read. Affects feature-gating: read-only mode blocks mutations. |
| purpose | string | | Why this call is needed for the view model |
| viewModelMapping | string | | Which part of the viewModelShape this response populates |

### featureGatingRules (per mode)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| mode | enum | ✓ | FULL, READ_ONLY, EMBEDDED, EXCLUDED |
| behavior | string | ✓ | What the BFF does in this mode |
| allowedCalls | string[] | | Service calls permitted (by serviceId). For FULL: all calls. For READ_ONLY: only calls where isMutation=false. For EXCLUDED: no calls. |
| blockedCalls | string[] | | Service calls blocked in this mode |
| viewModelAdjustment | string | | How the view model changes (e.g., "write action fields removed") |

**Standard gating rules:**

| Mode | Behavior |
|------|----------|
| FULL | All service calls executed — reads and mutations. Full view model returned. |
| READ_ONLY | Only calls where isMutation=false. Mutation endpoints blocked. Write-action fields stripped from view model. |
| EMBEDDED | Subset of calls — only those needed for embedding context (e.g., summary widget). Reduced view model. |
| EXCLUDED | No calls made. Feature is absent from the BFF response. |

### extensionRouting (per extension point)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| extensionPointId | string | ✓ | Extension point ID from the Platform-Feature Spec §8 |
| routeTarget | string | ✓ | Where extension calls are routed: platform extension API endpoint or custom service URL |
| protocol | enum | | REST, gRPC, event |
| fallbackBehavior | string | | What happens if the extension target is unavailable |

### failureModes (per service)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| serviceId | string | ✓ | Which service this failure mode applies to |
| failureType | enum | | timeout, unavailable, error_response |
| fallbackBehavior | string | ✓ | What the BFF does: return cached data, return partial view model, return error, degrade gracefully |
| isCritical | boolean | | If true, the entire feature fails. If false, the view model is returned with missing sections. |

### cachingHints (per hint)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| serviceId | string | ✓ | Which service response to cache |
| ttl | string | | Time-to-live (e.g., "5m", "1h", "24h") |
| invalidationEvent | string | | Domain event that invalidates the cache (e.g., "pps.pd.product.updated") |
| scope | enum | | tenant, user, global |

---

## Constraints

1. Every service call in Feature Spec §5 MUST appear in aggregatedServices.
2. The viewModelShape MUST match Feature Spec §5.2 exactly — it IS the same artifact.
3. Cross-suite reads (isMutation=false to a service in a different suite) MUST have a corresponding `requires` constraint in the Feature Model UVL.
4. Cross-suite writes are NOT allowed — aggregatedServices with isMutation=true MUST only reference services within the feature's own suite.
5. Feature-gating rules MUST cover at least FULL, READ_ONLY, and EXCLUDED modes.
6. The BFF MUST NOT add business logic beyond aggregation, gating, and routing — domain logic belongs in services.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derived from | [Platform-Feature Spec](platform-feature-spec.md) | §5 backend dependencies + §5.2 view-model shape |
| calls | [Domain/Service Spec](domain-spec.md) | aggregatedServices[].serviceId → service endpoints |
| response shaped by | [API Contract](api-contract.md) | Service responses aggregated into viewModelShape |
| data binds to | [Screen Contract AUI](screen-contract-aui.md) | AUI zones[].dataBinding → viewModelShape fields |
| configured by | Product (BFF Configuration) | Product resolves gating mode and extension routing |
| fed to | [Product](../product/product.md) | BFF Contract drives Product.bffConfiguration |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Conceptual Stack §4.6 | `conceptual-stack.md` | ✅ Defines BFF responsibilities, gating, deployment models |
| Conceptual Stack §4.6.3 | `conceptual-stack.md` | ✅ Feature Spec §5 + §5.2 as BFF specification |
| Artifact Catalog §3.8 | `artifact-catalog.md` | ✅ Field summary |

---

## Gaps

- ❌ No template exists — must be created
- ❌ No schema exists — must be formalized
- ⚠️ Feature Spec §5 + §5.2 contain the data, but the BFF perspective (gating, extension routing, failure modes) is not yet formalized in the template
