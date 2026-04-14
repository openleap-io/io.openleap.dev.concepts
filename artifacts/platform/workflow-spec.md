# Artifact: Workflow Spec

> **Space:** Platform (Domain Engineering)
> **Conceptual Stack Layer:** Service (non-actor processes)
> **Source:** Agora Spec §5.3.4

---

## Definition

A Workflow Spec defines a non-actor (non-human-driven) process in the platform: batch jobs, sagas, orchestrations, ETL pipelines, scheduled jobs, and system integrations. Unlike Platform-Features (which are user-facing), workflows are background processes that coordinate services, handle compensations, and manage retries. Workflows are owned by a suite and reference the domain services they orchestrate.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Pattern: `wf-{name}` (e.g., wf-month-end-close, wf-stock-reorder) |
| type | enum | ✓ | batch, saga, orchestration, etl, scheduled_job, integration |
| suite | string | ✓ | Owning suite ID |
| trigger | Trigger | ✓ | What starts this workflow |
| steps | Step[] | ✓ | Ordered workflow steps |
| retryPolicy | RetryPolicy | | Global retry policy for the workflow |
| compensationStrategy | enum | | backward_recovery (undo completed steps in reverse), forward_recovery (retry/skip and continue), none |
| referencedServices | ServiceRef[] | ✓ | Domain services this workflow orchestrates |
| observability | Observability | | Metrics and alerting configuration |
| adrs | ADR[] | | Workflow-specific architecture decisions |
| elaraCrossReference | string | | Reference to the Elara Workflow Candidate that originated this spec |

### trigger

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| type | enum | ✓ | cron (time-based), event (domain event), API (REST call), manual (operator action) |
| schedule | string | | Cron expression (for type=cron, e.g., "0 2 * * *") |
| eventRef | string | | Domain event routing key (for type=event) |
| apiEndpoint | string | | REST endpoint (for type=API) |
| description | string | | Human-readable trigger description |

### steps (per step)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| order | integer | ✓ | Sequence position |
| name | string | ✓ | Step name |
| action | string | ✓ | What this step does (e.g., "Lock fiscal period", "Calculate reorder quantities") |
| serviceRef | string | ✓ | Domain service ID that executes this step |
| endpoint | string | | Specific API endpoint or event used |
| compensation | string | | Compensating action if this step must be undone (for saga pattern) |
| retryPolicy | StepRetryPolicy | | Step-specific retry override |
| timeout | string | | Maximum execution time for this step (e.g., "30s", "5m") |
| onFailure | enum | | retry, compensate, skip, abort |

### retryPolicy (global)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| maxAttempts | integer | ✓ | Maximum number of retry attempts |
| backoff | enum | ✓ | fixed, linear, exponential |
| initialInterval | string | | Initial wait between retries (e.g., "1s", "5s") |
| maxInterval | string | | Maximum wait between retries (e.g., "60s", "5m") |

### referencedServices (per service)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| serviceId | string | ✓ | Domain service ID (e.g., fi-gl-svc, pps-im-svc) |
| role | string | ✓ | What role this service plays in the workflow (e.g., "data source", "mutation target", "notification sink") |
| endpoints | string[] | | Specific endpoints used |
| events | string[] | | Events consumed or produced |

### observability

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| metrics | string[] | | Key metrics to track (e.g., "execution_duration_ms", "step_failure_count", "records_processed") |
| alerts | Alert[] | | Alert conditions: condition, severity (info/warning/critical), channel |

---

## Constraints

1. Workflow ID MUST match pattern `wf-{name}`.
2. Every step MUST reference a valid domain service via serviceRef.
3. If compensationStrategy is `backward_recovery`, every step SHOULD define a compensation action.
4. Workflows with type `saga` MUST define compensationStrategy.
5. Workflows follow the same tier governance as services — they may only orchestrate services at the same or lower tier.
6. Workflows are non-actor processes — they do NOT have user-facing screens or features.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| belongs to | [Suite Spec](suite-spec.md) | suite → Suite.metadata.id |
| orchestrates | [Domain/Service Spec](domain-spec.md) | referencedServices[].serviceId → service specs |
| triggers via | [Event Contract](event-contract.md) | trigger.eventRef → domain events |
| calls | [API Contract](api-contract.md) | steps[].endpoint → service API endpoints |
| originated from | [Workflow Candidate](../product/workflow-candidate.md) | elaraCrossReference → candidate discovered in Elara |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| Agora Spec §5.3.4 | `io.openleap.spec/concepts/agora/` | ✅ Defines workflow types and structure |
| Artifact Catalog §3.11 | `concepts/ARTIFACT_CATALOG.md` | ✅ Field summary |
| Conceptual Stack §3.3 | `concepts/CONCEPTUAL_STACK.md` | ✅ Service as deployable unit context |

---

## Gaps

- ❌ No template exists — must be created
- ❌ No schema exists — must be defined in Telos
- ⚠️ Saga compensation patterns need detailed examples
- ⚠️ ETL-specific fields (source/target/transformation) may need extension
