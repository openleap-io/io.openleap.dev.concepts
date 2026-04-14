# Artifact: API Contract

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Service (contract dimension)
> **Source:** Convention + governance rules

---

## Definition

An API Contract is an OpenAPI specification that formally defines a domain service's REST API. It is derived from the Domain/Service Spec's `contracts.restApi` section and serves as the machine-readable contract between the service and its consumers (other services, BFF instances, external integrations). The API Contract is the authoritative definition of what a service exposes over HTTP — request/response schemas, authentication, error codes, and vendor extensions for governance.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| format | string | ✓ | OpenAPI 3.x YAML |
| convention | string | ✓ | File path convention: `contracts/http/<suite>/<domain>/openapi.yaml` |
| openapi | string | ✓ | OpenAPI version (e.g., "3.0.3", "3.1.0") |
| info | OpenAPIInfo | ✓ | Standard OpenAPI info block: title, version, description, contact |
| servers | Server[] | ✓ | API server URLs with basePath from Domain Spec metadata.apiBasePath |
| paths | PathMap | ✓ | All endpoints from Domain Spec contracts.restApi.endpoints — path, method, summary, parameters, requestBody, responses, security |
| components | Components | ✓ | Schemas (from domain model aggregates/VOs), securitySchemes, responses |
| security | SecurityRequirement[] | ✓ | Global security from Domain Spec contracts.restApi.authScheme |

### Required Vendor Extensions

| Extension | Type | Required | Description |
|-----------|------|----------|-------------|
| x-owner | string | ✓ | Owning team (from Domain Spec metadata.team.name) |
| x-suite | string | ✓ | Suite ID (from Domain Spec metadata.suite) |
| x-domain | string | ✓ | Domain ID (from Domain Spec metadata.domain) |
| x-service | string | ✓ | Service ID (from Domain Spec metadata.id) |

### Derivation from Domain/Service Spec

| Domain Spec Section | OpenAPI Element |
|---------------------|----------------|
| metadata.apiBasePath | servers[].url |
| contracts.restApi.authScheme | security, components.securitySchemes |
| contracts.restApi.endpoints[].method + path | paths.{path}.{method} |
| contracts.restApi.endpoints[].summary | operation.summary |
| contracts.restApi.endpoints[].requestBody | operation.requestBody |
| contracts.restApi.endpoints[].response | operation.responses |
| contracts.restApi.endpoints[].errorResponses[] | operation.responses (4xx/5xx) |
| contracts.restApi.endpoints[].roleRequired | operation.security or x-role-required |
| domainModel.aggregates[].root.attributes | components.schemas |
| domainModel.valueObjects | components.schemas (embedded) |
| domainModel.enumerations | components.schemas (enum types) |
| domainModel.sharedTypes | components.schemas (reusable) |

---

## Constraints

1. Every endpoint in Domain Spec contracts.restApi.endpoints MUST appear in the OpenAPI paths.
2. MUST include vendor extensions: x-owner, x-suite, x-domain, x-service on the info object.
3. File MUST be placed at `contracts/http/<suite>/<domain>/openapi.yaml`.
4. API base path MUST follow convention: `/api/<suite>/<domain>/v1`.
5. Request/response schemas MUST be derived from the domain model (aggregates, entities, value objects, enumerations).
6. Error responses MUST include the error codes defined in Domain Spec businessRules.catalog[].errorCode.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derived from | [Domain/Service Spec](domain-spec.md) | contracts.restApi section + domainModel for schemas |
| consumed by | [BFF Contract](bff-contract.md) | BFF calls endpoints defined here |
| consumed by | [Workflow Spec](workflow-spec.md) | Workflows call endpoints defined here |
| consumed by | Other services | Integration via outboundDependencies |
| referenced by | [Platform-Feature Spec](platform-feature-spec.md) | §5 backendDependencies reference these endpoints |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| OpenAPI Specification | openapis.org | ✅ Industry standard |
| Domain Spec contracts.restApi | `service-layer.schema.json` | ✅ Source fields defined |
| File convention | Platform governance rules | ✅ `contracts/http/<suite>/<domain>/openapi.yaml` |
| Artifact Catalog §3.3 | `artifact-catalog.md` | ✅ Summary |

---

## Gaps

- ⚠️ No automated derivation tool yet (Domain Spec → OpenAPI generation)
- ⚠️ Vendor extension validation not yet enforced in CI
