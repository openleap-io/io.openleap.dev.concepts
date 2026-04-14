# Artifact: Domain / Service Spec

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Domain + Service
> **Source:** `service-layer.schema.json`

---

## Definition

A Domain/Service Spec defines a single bounded context (Evans, 2003) and its implementing microservice. One domain equals one service (1:1 mapping). It owns aggregates (consistency boundaries), invariants (always-true business rules), domain events (facts about what happened), a REST API, and an event contract. The service has its own data store, deployment lifecycle, and API namespace.

---

## Fields

### metadata

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| metadata.id | string | ✓ | Service ID, pattern: `^[a-z]+-[a-z]+-svc$` (e.g., fi-gl-svc) |
| metadata.name | string | ✓ | Human-readable service name |
| metadata.suite | string | ✓ | Parent suite ID (e.g., fi, pps, hr) |
| metadata.domain | string | ✓ | Domain within suite (e.g., gl, ar, ap) |
| metadata.version | string (semver) | ✓ | Spec version, pattern: `^\d+\.\d+\.\d+$` |
| metadata.status | enum | | draft, review, active, deprecated, retired |
| metadata.boundedContextRef | string | | Reference to a bounded context defined in the parent suite, pattern: `^bc:` |
| metadata.apiBasePath | string | | API base path (e.g., /api/fi/gl/v1) |
| metadata.team | object | | Owning team: name, email, slackChannel |
| metadata.repository | string (uri) | | Source repository URL |
| metadata.tags | string[] | | Classification tags |

### scope

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| scope.purpose | string | ✓ | What this service does, minLength 20 characters |
| scope.inScope | string[] | | What belongs to this service |
| scope.outOfScope | string[] | | What explicitly does NOT belong |
| scope.relatedDocuments | RelatedDoc[] | | Related documents: name, path, type |

### businessContext

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| businessContext.problemsSolved | ProblemSolution[] | | Per problem: problem, solution, businessValue |
| businessContext.businessValue | string[] | | Value propositions |
| businessContext.stakeholders | Stakeholder[] | | Per stakeholder: role, interest |

### domainModel

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| domainModel.overview | string | | Mermaid diagram or prose description |
| domainModel.aggregates | Aggregate[] | ✓ | Consistency boundaries (minItems 1) |
| domainModel.enumerations | Enumeration[] | | Named enumerations referenced by attributes via enumRef |
| domainModel.sharedTypes | SharedType[] | | Reusable value objects across aggregates (e.g., Money, AuditInfo) |

#### Aggregate (detailed)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: `^agg:` (e.g., agg:journal-entry) |
| name | string | ✓ | PascalCase aggregate name (e.g., JournalEntry) |
| description | string | | What this aggregate represents |
| businessPurpose | string | | Why this aggregate exists from a business perspective |
| root | AggregateRoot | ✓ | The single entry point for all mutations |
| entities | DomainEntity[] | | Child entities within the aggregate boundary |
| valueObjects | ValueObject[] | | Value objects local to this aggregate |

#### AggregateRoot (embedded in Aggregate)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| attributes | Attribute[] | ✓ | Root attributes (minItems 1). Each: name (camelCase), type (string/integer/number/boolean/array/object), format (uuid/date/date-time/decimal/iso-4217/...), required, description, example, enumRef, typeRef, constraints |
| lifecycleStates | LifecycleStates | | State machine: initialState, states[], terminalStates[], transitions[] (from, to, trigger, guard) |
| invariants | Invariant[] | | Business rules that must always hold: ruleId (pattern: `^BR-`), description |
| domainEvents | string[] | | Events this root can emit (references to contracts.events.outbound routing keys) |

#### DomainEntity (embedded in Aggregate)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: `^ent:` (e.g., ent:posting-line) |
| name | string | ✓ | PascalCase entity name |
| relationship | enum | ✓ | one_to_one, one_to_many, many_to_many (from root to this entity) |
| description | string | | Entity description |
| businessPurpose | string | | Business rationale |
| attributes | Attribute[] | ✓ | Entity attributes (minItems 1) |
| lifecycleStates | LifecycleStates | | State machine definition |
| invariants | Invariant[] | | Entity-level invariants |
| constraints | AttributeConstraints | | Collection-level constraints (e.g., minItems for posting lines) |

#### ValueObject (embedded in Aggregate)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: `^vo:` (e.g., vo:money) |
| name | string | ✓ | PascalCase value object name |
| description | string | | Value object description |
| attributes | Attribute[] | ✓ | Attributes (minItems 1) |
| validationRules | string[] | | Self-validation rules (e.g., "amount must be non-negative") |

### businessRules

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| businessRules.catalog | BusinessRule[] | ✓ | Rules catalog |

#### BusinessRule

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: `^BR-` (e.g., BR-GL-001) |
| name | string | ✓ | Rule name |
| description | string | ✓ | What the rule enforces |
| rationale | string | | Why this rule exists |
| scope | string | | Which aggregate(s) or operation(s) the rule applies to |
| enforcement | string | | How/where enforced (e.g., "domain layer", "API validation") |
| errorCode | string | | Error code when rule is violated |
| errorMessage | string | | Human-readable error message |

### contracts

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| contracts.restApi.basePath | string | | API base path |
| contracts.restApi.authScheme | string | | Authentication scheme |
| contracts.restApi.endpoints | Endpoint[] | | REST endpoints: id (pattern: `^endpoint:`), method (GET/POST/PUT/PATCH/DELETE), path, summary, description, roleRequired, validationRules[], eventsPublished[], requestBody, response, errorResponses[] |
| contracts.events.outbound | OutboundEvent[] | | Published events: eventId (pattern: `^event:`), routingKey, businessMeaning, whenPublished, payloadSchema, payloadExample, consumers[] |
| contracts.events.inbound | InboundEvent[] | | Consumed events: eventId, routingKey, producer, handler, purpose |

### integration

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| integration.patternDecision | object | | pattern (event_driven/orchestration/hybrid) + rationale |
| integration.outboundDependencies | OutboundDep[] | | Per dependency: service, integrationType (sync_api/async_event/library), criticality (critical/high/medium/low), endpointsUsed[], eventsConsumed[] |
| integration.followsSuitePattern | boolean | | Whether this service follows the suite's integration pattern decision |

### dataModel

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| dataModel.storageTechnology | string | | Database technology (e.g., PostgreSQL) |
| dataModel.tables | Table[] | | Physical tables: name, description, columns[] (name, type, nullable, primaryKey, foreignKey, description), indexes[] |
| dataModel.referenceData | ReferenceData[] | | Static/seed data: table, data[] |

### security

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| security.dataClassification | enum | | public, internal, confidential, restricted |
| security.accessControl | AccessControlEntry[] | | Per role: permissions[], description |
| security.compliance | string[] | | Compliance requirements |
| security.suiteComplianceRefs | string[] | | References to suite-level compliance policies |

### Remaining top-level fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| suiteAdrRefs | string[] | | References to suite-level ADRs (pattern: `^ADR-[A-Z]+-\d{3}$`) — avoids duplication |
| adrs | ADR[] | | Service-specific architecture decisions: id (pattern: `^ADR-\d{3}$`), title, status, context, decision, rationale, alternativesConsidered[], consequences, supersedes/supersededBy |
| glossary | GlossaryEntry[] | | Service-local terms: term, definition, aliases[] |
| changelog | ChangelogEntry[] | | Version history: version, date, author, changes[] |

---

## Constraints

1. One domain = one service (1:1). If a domain is too large, split it — do not add a second service.
2. metadata.id MUST match pattern `^[a-z]+-[a-z]+-svc$`.
3. scope.purpose MUST be at least 20 characters.
4. Every aggregate MUST have an id matching `^agg:` and a name in PascalCase.
5. Every business rule id MUST match `^BR-`.
6. Every endpoint id MUST match `^endpoint:`.
7. Every outbound event eventId MUST match `^event:`.
8. Required top-level sections: metadata, scope, domainModel (with aggregates), businessRules (with catalog), contracts.
9. Own data store: PostgreSQL schema `<suite>_<domain>`.
10. Own API namespace: `/api/<suite>/<domain>/v1`.
11. Own event exchange: `<suite>.<domain>.events`.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| belongs to | [Suite Spec](suite-spec.md) | metadata.suite → Suite.metadata.id |
| produces | [API Contract](api-contract.md) | contracts.restApi → OpenAPI YAML |
| produces | [Event Contract](event-contract.md) | contracts.events → AsyncAPI / JSON Schema |
| referenced by | [Platform-Feature Spec](platform-feature-spec.md) | Feature Spec §5 backendDependencies reference service endpoints |
| referenced by | [BFF Contract](bff-contract.md) | BFF aggregates calls to this service |
| referenced by | [Workflow Spec](workflow-spec.md) | Workflow steps reference service endpoints and events |
| fed by | [Business Object](../product/business-object.md) | Objects become aggregate candidates |
| fed by | [Business Event](../product/business-event.md) | Events become domain events |
| fed by | [Business Rule](../product/business-rule.md) | Rules become service-level business rules |
| fed by | [Quality Requirement](../product/quality-requirement.md) | QRs become service-level NFRs |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Service Layer Schema | `io.openleap.telos/schemas/service-layer.schema.json` | ✅ Comprehensive (775 lines), authoritative field definitions |
| Domain Spec Template | `templates/DOMAIN_SPEC_TEMPLATE.md` | ✅ Solid, needs minor Conceptual Stack alignment |
| Conceptual Stack §3.2–§3.3 | `conceptual-stack.md` | ✅ Defines domain = bounded context, service = deployable unit |
| Artifact Catalog §3.2 | `artifact-catalog.md` | ✅ Summary of domain/service fields |

---

## Gaps

- ⚠️ Domain Spec Template needs minor alignment to Conceptual Stack (extension points, tier governance)
- Schema and template are otherwise solid
