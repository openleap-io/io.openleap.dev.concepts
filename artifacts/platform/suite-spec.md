# Artifact: Suite Spec

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Suite
> **Source:** `suite-layer.schema.json`

---

## Definition

A Suite Spec is the top-level grouping artifact of the Platform Space. A Suite clusters domains (bounded contexts) that share a common Ubiquitous Language (UBL). The UBL IS the suite boundary — when two domains use the same term with the same meaning, they belong to the same suite; when they use the same term with different meanings, they belong to different suites. The Suite Spec defines the vocabulary, domain model, service landscape, integration patterns, and governance for all services within the suite.

---

## Fields

### metadata

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| metadata.id | string | ✓ | Suite ID, pattern: `^[a-z]{2,4}$` (e.g., fi, pps, sd, hr) |
| metadata.name | string | ✓ | Full name of the suite (e.g., "Finance") |
| metadata.description | string | ✓ | Short description, minLength 10 characters |
| metadata.version | string (semver) | ✓ | Spec version, pattern: `^\d+\.\d+\.\d+$` |
| metadata.status | enum | ✓ | draft, active, deprecated |
| metadata.owner | object | | Team (name, email, slack_channel) |
| metadata.boundedContexts | string[] | | Authoritative list of bounded contexts in this suite, pattern: `^bc:` per item. Services reference these via metadata.boundedContextRef |

### purpose

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| purpose.businessPurpose | string | ✓ | What business problem does this suite solve? minLength 50 characters |
| purpose.inScope | string[] | ✓ | What belongs to this suite (minItems 1) |
| purpose.outOfScope | string[] | | What explicitly does NOT belong |
| purpose.targetUsers | TargetUser[] | | Users with role and interest |
| purpose.businessValue | string[] | | Business value statements |

### ubiquitousLanguage (THE central section)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| ubiquitousLanguage | GlossaryEntry[] | ✓ | The domain glossary — THE central section of any suite (minItems 1) |

#### GlossaryEntry

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: `^[a-z]+:glossary:[a-z-]+$` (e.g., fi:glossary:buchung) |
| term | string | ✓ | The term (may contain German/English) |
| aliases | string[] | | Alternative names for the same concept |
| definition | string | ✓ | Precise definition, minLength 20 characters |
| examples | string[] | | Illustrative examples |
| relatedTerms | string[] | | IDs of other glossary entries |
| usedByServices | string[] | | Service IDs that use this term |
| notToConfuseWith | string | | Disambiguation from similar terms in other suites |

### domainModel

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| domainModel.overviewDiagram | string | | Mermaid diagram of the conceptual domain model |
| domainModel.coreConcepts | CoreConcept[] | ✓ | Key concepts: name, owner (service ID), description, glossaryRef (minItems 1) |
| domainModel.sharedKernel | SharedConcept[] | | Concepts shared across services: concept, owner, sharedWith[], mechanism (event/api/shared_db/library), optional typeDefinition |
| domainModel.boundedContextMap | ContextRelationship[] | | DDD context map: upstream, downstream, pattern (conformist/customer_supplier/anticorruption_layer/shared_kernel/published_language/open_host_service/separate_ways), description, sharedArtifacts[]. For intra-suite relationships only; cross-suite goes in externalInterfaces |

### serviceLandscape

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| serviceLandscape.services | ServiceEntry[] | ✓ | Services in this suite: id, name, responsibility, status (planned/development/active/deprecated), boundedContext, specRef (minItems 1) |
| serviceLandscape.responsibilityMatrix | map | | Service → responsibilities mapping |
| serviceLandscape.dependencyDiagram | string | | Mermaid diagram of service dependencies |

### integrationPatterns

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| integrationPatterns.patternDecision | object | ✓ | pattern (event_driven/orchestration/hybrid) + rationale[] |
| integrationPatterns.eventFlows | EventFlow[] | | Named flows: trigger, steps (order, service, action, type, event_or_endpoint), diagram |
| integrationPatterns.syncVsAsync | SyncAsyncDecision[] | | Per integration: type (sync/async) + reason |
| integrationPatterns.errorHandling | ErrorHandling[] | | Per scenario: handling strategy |

### eventConventions

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| eventConventions.routingKeyPattern | object | ✓ | Pattern string + segments[] (name, description, examples) |
| eventConventions.payloadEnvelope | object | ✓ | Standard envelope structure for all events in this suite |
| eventConventions.versioning | object | | Strategy + description |
| eventConventions.eventCatalog | EventCatalogEntry[] | | Per event: routingKey, producer, consumers[], description |

### adrs

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| adrs | SuiteADR[] | | Suite-level architecture decisions: id (pattern: `^ADR-[A-Z]+-\d{3}$`), title, status (proposed/accepted/deprecated/superseded), scope, context, decision, rationale[], consequences (positive/negative), affectedServices[], supersedes/supersededBy |

### crossCuttingConcerns

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| crossCuttingConcerns.compliance | ComplianceRequirement[] | | regulation, requirement, implementation |
| crossCuttingConcerns.security | object | | authentication, authorization, dataClassification |
| crossCuttingConcerns.multiTenancy | object | | model (single_tenant/shared_schema/schema_per_tenant/db_per_tenant), isolation, tenantIdPropagation, rules[] |
| crossCuttingConcerns.audit | object | | requirements[], retention[] (entity, period, legalBasis, actionAfterExpiry: delete/anonymize/archive) |

### externalInterfaces

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| externalInterfaces.outbound | ExternalInterface[] | | Interfaces to other suites: targetSuite, interfaceType (event/api), interfaceName, description |
| externalInterfaces.inbound | ExternalInterface[] | | Interfaces from other suites: targetSuite, interfaceType, interfaceName, description |
| externalInterfaces.externalContextMapping | ContextRelationship[] | | DDD context mapping for CROSS-SUITE boundaries (same structure as domainModel.boundedContextMap) |

### roadmap

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| roadmap | RoadmapPhase[] | | Phases: phase, timeframe, items[] |

---

## Constraints

1. The Ubiquitous Language (ubiquitousLanguage) is THE central section — it defines the suite boundary. Every domain within the suite MUST share core terms with consistent semantics (Conceptual Stack §3.1).
2. metadata.id MUST match pattern `^[a-z]{2,4}$`.
3. metadata.description MUST be at least 10 characters.
4. purpose.businessPurpose MUST be at least 50 characters.
5. Every GlossaryEntry.id MUST match pattern `^[a-z]+:glossary:[a-z-]+$`.
6. Every GlossaryEntry.definition MUST be at least 20 characters.
7. When a term crosses a suite boundary, it MUST be explicitly translated (Anti-Corruption Layer) — documented in externalInterfaces.externalContextMapping.
8. Required top-level sections: metadata, purpose, ubiquitousLanguage, domainModel, serviceLandscape, integrationPatterns, eventConventions.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| contains | [Domain/Service Spec](domain-spec.md) | serviceLandscape.services[].specRef |
| contains | [Platform-Feature Spec](platform-feature-spec.md) | Features owned by this suite (via feature.suite field) |
| contains | [Suite Feature Catalog](suite-feature-catalog.md) | The catalog listing all features of this suite |
| contains | [Workflow Spec](workflow-spec.md) | Workflows owned by this suite (via workflow.suite field) |
| referenced by | [Platform Catalog UVL](platform-catalog-uvl.md) | Suite catalog imported into platform root |
| fed by | [Functional Area](../product/functional-area.md) | Product functional areas map to suites |
| fed by | [Glossary Term](../product/glossary-term.md) | Product glossary terms feed suite UBL |
| fed by | [External Interface](../product/external-interface.md) | Product external interfaces → suite external interfaces |
| fed by | [Decision](../product/decision.md) | Product decisions trace to suite ADRs |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Suite Layer Schema | `io.openleap.telos/schemas/suite-layer.schema.json` | ✅ Comprehensive (649 lines), authoritative field definitions |
| Suite Spec Template | `templates/SUITE_SPEC_TEMPLATE.md` | ⚠️ Sparse — needs major rework to match schema |
| Conceptual Stack §3.1 | `conceptual-stack.md` | ✅ Defines suite = UBL boundary |
| Artifact Catalog §3.1 | `artifact-catalog.md` | ✅ Summary of suite fields and relationships |

---

## Gaps

- ⚠️ Suite Spec Template needs major rework to fully match the schema
- The schema is comprehensive; the template lags behind it
