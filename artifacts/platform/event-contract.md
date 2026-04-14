# Artifact: Event Contract

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Service (contract dimension)
> **Source:** Convention + governance rules

---

## Definition

An Event Contract is an AsyncAPI specification paired with JSON Schema definitions that formally defines a domain service's event interface. It is derived from the Domain/Service Spec's `contracts.events` section (outbound and inbound events). The Event Contract specifies the routing key pattern, payload schema, and envelope structure for every domain event the service produces or consumes. It follows the suite's event conventions defined in the Suite Spec.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| format | string | ✓ | AsyncAPI + JSON Schema |
| convention | string | ✓ | File path convention: `contracts/events/<suite>/<domain>/<aggregate>.<event>.schema.json` |
| asyncapi | string | ✓ | AsyncAPI version (e.g., "2.6.0", "3.0.0") |
| info | AsyncAPIInfo | ✓ | Standard AsyncAPI info block: title, version, description |
| channels | ChannelMap | ✓ | Event channels with routing key patterns from Suite Spec eventConventions.routingKeyPattern |
| components | Components | ✓ | Message schemas derived from Domain Spec event payloads |

### Per Event (derived from Domain Spec contracts.events)

#### Outbound Events

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| eventId | string | ✓ | Pattern: `^event:` (from Domain Spec contracts.events.outbound[].eventId) |
| routingKey | string | ✓ | Routing key following suite convention (e.g., `fi.gl.journal-entry.posted`) |
| businessMeaning | string | ✓ | What business fact this event represents |
| whenPublished | string | ✓ | Under what conditions (e.g., "After journal entry is posted and validated") |
| payloadSchema | object | ✓ | JSON Schema for the event payload |
| payloadExample | object | | Example payload for documentation |
| consumers | Consumer[] | | Known consumers: service, handler, purpose |

#### Inbound Events

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| eventId | string | ✓ | Event identifier |
| routingKey | string | ✓ | Routing key this service subscribes to |
| producer | string | ✓ | Producing service ID |
| handler | string | ✓ | Handler function/class in this service |
| purpose | string | ✓ | What this service does when receiving the event |

### Payload Envelope (from Suite Spec eventConventions.payloadEnvelope)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| eventType | string | ✓ | Routing key |
| version | string | ✓ | Event schema version |
| timestamp | string (date-time) | ✓ | When the event occurred |
| source | string | ✓ | Producing service ID |
| tenantId | string | ✓ | Tenant context (multi-tenancy) |
| correlationId | string | ✓ | Request correlation ID for tracing |
| payload | object | ✓ | Event-specific payload (per JSON Schema) |

### Versioning (from Suite Spec eventConventions.versioning)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| strategy | string | ✓ | Versioning strategy (e.g., "header-based", "routing-key-suffix") |
| description | string | | How event versions are managed and evolved |

---

## Constraints

1. Every outbound event in Domain Spec contracts.events.outbound MUST have a corresponding JSON Schema file.
2. Every inbound event in Domain Spec contracts.events.inbound MUST reference a valid outbound event from the producing service.
3. Routing keys MUST follow the suite's routingKeyPattern convention.
4. Payload MUST include the suite's standard envelope fields.
5. Schema files MUST be placed at `contracts/events/<suite>/<domain>/<aggregate>.<event>.schema.json`.
6. Event schemas MUST be backward-compatible within a major version (additive changes only).

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| derived from | [Domain/Service Spec](domain-spec.md) | contracts.events section (outbound + inbound) |
| follows conventions of | [Suite Spec](suite-spec.md) | eventConventions: routingKeyPattern, payloadEnvelope, versioning |
| consumed by | [Workflow Spec](workflow-spec.md) | Workflows triggered by or producing events |
| consumed by | Other services | Integration via events.inbound subscriptions |
| referenced by | [BFF Contract](bff-contract.md) | BFF cachingHints[].invalidationEvent references domain events |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| AsyncAPI Specification | asyncapi.com | ✅ Industry standard |
| JSON Schema | json-schema.org | ✅ Industry standard |
| Domain Spec contracts.events | `service-layer.schema.json` | ✅ Source fields defined |
| Suite Spec eventConventions | `suite-layer.schema.json` | ✅ Routing key pattern, envelope, versioning |
| File convention | Platform governance rules | ✅ `contracts/events/<suite>/<domain>/<aggregate>.<event>.schema.json` |
| Artifact Catalog §3.4 | `concepts/ARTIFACT_CATALOG.md` | ✅ Summary |

---

## Gaps

- ⚠️ No automated derivation tool yet (Domain Spec → AsyncAPI/JSON Schema generation)
- ⚠️ Backward compatibility validation not yet enforced in CI
