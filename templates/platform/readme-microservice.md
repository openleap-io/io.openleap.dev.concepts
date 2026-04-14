# {suite}-{domain}-svc — {Service Display Name}

> **Conceptual Stack Layer:** Implementation Artifact (README)
> **Space:** Platform
> **Owner:** Development Team
> **Spec reference:** `io.openleap.dev.spec/{Tier}/{SUITE}/{domain}-service-spec.md`
> **Guidelines reference:** `io.openleap.dev.guidelines`

> **Meta Information**
> - **Suite:** `{suite}` (e.g., fi, co, hr, pps, sd — or t1, t2, t4 for non-T3)
> - **Domain:** `{domain}` (e.g., gl, ar, ap, cfg, bp)
> - **Service ID:** `{suite}-{domain}-svc`
> - **Tier:** T{n} — {tier-name}
> - **Base Package:** `io.openleap.{suite}.{domain}`
> - **API Base Path:** `/api/{suite}/{domain}/v1`
> - **Port:** `{port}`
> - **Status:** [ACTIVE | MAINTENANCE | DEPRECATED]

---

<!-- ═══════════════════════════════════════════════════════════════════
     AUTHORING GUIDE
     ─────────────────────────────────────────────────────────────────
     This template defines the standard README structure for OpenLeap
     microservice repositories. It covers operational and developer-
     facing concerns — the domain specification (business rules,
     domain model, event payloads) lives in io.openleap.dev.spec.

     Principles:
     • Spec referenzieren, nicht duplizieren.
     • §0–§2 = "Time to first success" (understand → build → run).
     • Prefer tables over prose for configuration, endpoints, events.
     • Link to generated sources (OpenAPI, Actuator) instead of
       hardcoding details that go stale.
     • Use HTML comments (like this one) as authoring guidance —
       delete them when filling in the template.
     ═══════════════════════════════════════════════════════════════════ -->

## 0. Service Overview

### 0.1 Purpose

<!-- 2-3 sentences: what this service does, which business domain it owns.
     Always link to the authoritative domain-service specification. -->

{Service Display Name} is the ... service within the {Suite Display Name} suite.
It owns the `{domain}` bounded context and provides ...

> **Specification:** [`{suite}-{domain}-service-spec.md`](https://github.com/openleap-io/io.openleap.dev.spec/blob/main/{Tier}/{SUITE}/{domain}/{file}.md)

### 0.2 Key Capabilities

<!-- 3-7 bullet points — high-level capabilities, not an API inventory. -->

- ...
- ...
- ...

### 0.3 Architecture Context

<!-- Where this service sits in the four-tier model, which suite it belongs to,
     key upstream / downstream dependencies. A small Mermaid diagram is optional. -->

| Aspect | Value |
|--------|-------|
| Tier | T{n} — {tier-name} |
| Suite | `{suite}` — {Suite Display Name} |
| Upstream | {services this service depends on} |
| Downstream | {services that depend on this service} |

---

## 1. Prerequisites

### 1.1 Runtime Dependencies

| Dependency | Version | Notes |
|------------|---------|-------|
| Java | 25+ | |
| Maven | 3.9+ | |
| Docker | 24+ | For local infrastructure |
| PostgreSQL | 17+ | |
| RabbitMQ | 4.x | |

<!-- Add or remove rows as needed (e.g., Keycloak, Redis, MinIO). -->

### 1.2 OpenLeap Dependencies

| Dependency | Version | Purpose |
|------------|---------|---------|
| `io.openleap.parent` | `{version}` | Parent POM |
| `io.openleap.starter` | `{version}` | CQRS/Event starter |

<!-- List additional shared libraries if any (e.g., io.openleap.common.*). -->

### 1.3 Environment Setup

<!-- How to bring up required infrastructure locally.
     Prefer a single command (e.g., docker compose up). -->

```bash
docker compose up -d
```

<!-- If there is a shared infra repo, link it here. -->

---

## 2. Getting Started

### 2.1 Clone & Build

```bash
git clone https://github.com/openleap-io/io.openleap.{suite}.{domain}.git
cd io.openleap.{suite}.{domain}
mvn clean verify
```

<!-- Note any build quirks (e.g., required Maven profiles, code generation steps). -->

### 2.2 Run Locally

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=local
```

<!-- Or: how to run from IDE, Docker, etc. -->

### 2.3 Verify

```bash
curl http://localhost:{port}/actuator/health
```

Expected response:
```json
{"status": "UP"}
```

### 2.4 Run Tests

```bash
# Unit tests
mvn test

# Integration tests (requires running infrastructure)
mvn verify -Pintegration
```

<!-- Document any special test setup (test containers, test profiles, seed data). -->

---

## 3. Configuration

### 3.1 Spring Profiles

| Profile | Purpose | Activates |
|---------|---------|-----------|
| `local` | Local development | Embedded defaults, verbose logging |
| `dev` | Development environment | Dev database, dev broker |
| `staging` | Pre-production | Staging infra, reduced logging |
| `prod` | Production | Full security, structured logging |

### 3.2 Key Configuration Properties

<!-- Only list properties a developer needs to know about or customize.
     For the full list, refer to Spring Boot Actuator /configprops. -->

| Property | Description | Default | Required |
|----------|-------------|---------|----------|
| `spring.datasource.url` | PostgreSQL JDBC URL | `jdbc:postgresql://localhost:5432/{domain}` | ✅ |
| `spring.rabbitmq.host` | RabbitMQ host | `localhost` | ✅ |
| ... | ... | ... | ... |

### 3.3 Environment Variables

<!-- Environment variables that override properties — especially secrets. -->

| Variable | Maps to | Description |
|----------|---------|-------------|
| `DATASOURCE_URL` | `spring.datasource.url` | Database connection URL |
| `DATASOURCE_USERNAME` | `spring.datasource.username` | Database user |
| `DATASOURCE_PASSWORD` | `spring.datasource.password` | Database password |
| `RABBITMQ_HOST` | `spring.rabbitmq.host` | Message broker host |
| ... | ... | ... |

### 3.4 Feature Flags

<!-- If the service uses feature flags or toggles, list them here. Remove this
     subsection if not applicable. -->

| Flag | Description | Default |
|------|-------------|---------|
| ... | ... | ... |

---

## 4. Project Structure

### 4.1 Module Layout

<!-- Describe the DDD package structure. Link to dev.guidelines for the full ontology. -->

```
src/main/java/io/openleap/{suite}/{domain}/
├── api/                 # REST controllers, DTOs
├── command/             # Command definitions and handlers
│   ├── handler/
│   └── ...
├── query/               # Query models and projections
├── domain/              # Aggregates, entities, value objects
│   ├── aggregate/
│   ├── event/
│   └── ...
├── infrastructure/      # Persistence, messaging, external clients
│   ├── persistence/
│   ├── messaging/
│   └── ...
└── config/              # Spring configuration
```

> **Full architecture details:** See [OpenLeap Development Guidelines](https://github.com/openleap-io/io.openleap.dev.guidelines)

### 4.2 Key Packages

| Package | Purpose |
|---------|---------|
| `*.api` | REST controllers, request/response DTOs |
| `*.command` | Command records and handler classes |
| `*.query` | Read-model projections and query handlers |
| `*.domain.aggregate` | Aggregate roots with business invariants |
| `*.domain.event` | Domain event definitions |
| `*.infrastructure.persistence` | JPA repositories, entity mappings |
| `*.infrastructure.messaging` | Event publishers and consumers |

### 4.3 Important Files

| File | Purpose |
|------|---------|
| `src/main/resources/application.yml` | Default configuration |
| `src/main/resources/application-local.yml` | Local development overrides |
| `src/main/resources/db/migration/` | Flyway migration scripts |
| `pom.xml` | Maven build configuration |
| `docker-compose.yml` | Local infrastructure stack |

---

## 5. API Overview

### 5.1 REST Endpoints

<!-- Summary table of main endpoints. Link to OpenAPI for full details. -->

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/api/{suite}/{domain}/v1/...` | ... |
| `POST` | `/api/{suite}/{domain}/v1/...` | ... |
| ... | ... | ... |

### 5.2 Command Ingress

<!-- Per ADR-004 (Hybrid Ingress): which commands are accepted via REST vs. messaging? -->

| Command | REST | Messaging | Notes |
|---------|------|-----------|-------|
| `Create{Aggregate}` | ✅ | ❌ | |
| ... | ... | ... | ... |

### 5.3 OpenAPI Documentation

<!-- How to access the generated OpenAPI spec. -->

- **Swagger UI:** `http://localhost:{port}/swagger-ui.html`
- **OpenAPI JSON:** `http://localhost:{port}/v3/api-docs`

---

## 6. Events & Messaging

### 6.1 Published Events

<!-- Events this service publishes via the transactional outbox (ADR-013). -->

| Event | Routing Key | Trigger |
|-------|-------------|---------|
| `{Aggregate}Created` | `{suite}.{domain}.{aggregate}.created` | ... |
| `{Aggregate}Updated` | `{suite}.{domain}.{aggregate}.updated` | ... |
| ... | ... | ... |

### 6.2 Consumed Events

| Event | Source Service | Action |
|-------|---------------|--------|
| ... | `{source-suite}-{source-domain}-svc` | ... |

### 6.3 Exchange & Queue Configuration

| Setting | Value |
|---------|-------|
| Exchange | `{suite}.{domain}.events` (topic) |
| Routing key pattern | `{suite}.{domain}.{aggregate}.{action}` |
| Dead-letter exchange | `{suite}.{domain}.events.dlx` |

### 6.4 Outbox Pattern

Events are published via the **transactional outbox** pattern (ADR-013) to guarantee at-least-once delivery (ADR-014). The outbox table is polled by the OpenLeap Starter and published to RabbitMQ automatically.

> **Implementation details:** See [ADR-013](https://github.com/openleap-io/io.openleap.dev.guidelines) in Development Guidelines.

---

## 7. Database

<!-- Remove this section if the service is stateless (no database). -->

### 7.1 Schema Overview

<!-- Brief description of main tables/schemas.
     Link to the spec's §8 Data Model for the full ERD — do not duplicate it here. -->

> **Data model:** See [Domain Service Spec §8](https://github.com/openleap-io/io.openleap.dev.spec/blob/main/{Tier}/{SUITE}/{domain}/{file}.md#8-data-model)

### 7.2 Migrations

| Setting | Value |
|---------|-------|
| Tool | Flyway |
| Location | `src/main/resources/db/migration/` |
| Naming | `V{version}__{description}.sql` |

<!-- How to create a new migration: -->

```bash
# Create a new migration file
touch src/main/resources/db/migration/V$(date +%Y%m%d%H%M)__description.sql
```

### 7.3 Seed Data

<!-- How to load reference/test data for local development. -->

```bash
# If seed data scripts exist:
psql -U {user} -d {domain} -f src/main/resources/db/seed/local-data.sql
```

---

## 8. Operations

### 8.1 Health & Readiness

| Endpoint | Purpose |
|----------|---------|
| `/actuator/health` | Liveness probe |
| `/actuator/health/readiness` | Readiness probe (includes DB, broker) |

### 8.2 Metrics

<!-- Key custom metrics exposed via Prometheus. -->

| Metric | Type | Description |
|--------|------|-------------|
| `{domain}_commands_total` | Counter | Total commands processed |
| `{domain}_events_published_total` | Counter | Total events published |
| ... | ... | ... |

- **Prometheus endpoint:** `/actuator/prometheus`
- **Dashboard:** {Link to Grafana dashboard if applicable}

### 8.3 Logging

| Setting | Value |
|---------|-------|
| Format | Structured JSON (production), plain text (local) |
| Correlation | `X-Correlation-ID` header propagated through MDC |
| Default level | `INFO` |

<!-- How to change log level at runtime: -->

```bash
curl -X POST http://localhost:{port}/actuator/loggers/{package} \
  -H 'Content-Type: application/json' \
  -d '{"configuredLevel": "DEBUG"}'
```

### 8.4 Deployment

| Setting | Value |
|---------|-------|
| Container image | `ghcr.io/openleap-io/{suite}-{domain}-svc:{version}` |
| Helm chart | {Link to Helm chart or infra repo} |
| CI/CD pipeline | {Link to GitHub Actions workflow} |

---

## 9. Troubleshooting

### 9.1 Common Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| Service fails to start | Database not reachable | Verify `DATASOURCE_URL`, ensure PostgreSQL is running |
| Events not published | RabbitMQ not reachable | Verify `RABBITMQ_HOST`, ensure RabbitMQ is running |
| 401 on API calls | Missing/invalid JWT | Check Keycloak token, verify `spring.security.oauth2` config |
| ... | ... | ... |

### 9.2 Debugging Tips

<!-- Practical tips for debugging common problems in this service. -->

- **Inspect outbox table:** `SELECT * FROM outbox_events WHERE processed = false ORDER BY created_at;`
- **Enable debug logging:** Set `logging.level.io.openleap.{suite}.{domain}=DEBUG` in `application-local.yml`
- **Replay failed events:** {describe how, if applicable}

---

## 10. Related Resources

### 10.1 Specification

- [Domain Service Spec](https://github.com/openleap-io/io.openleap.dev.spec/blob/main/{Tier}/{SUITE}/{domain}/{file}.md) — Authoritative domain model, business rules, event contracts

### 10.2 Guidelines

- [Development Guidelines](https://github.com/openleap-io/io.openleap.dev.guidelines) — Architecture patterns, ontology rules, ADR catalog
- [Frontend Guidelines](https://github.com/openleap-io/io.openleap.ui.guidelines) — Vue 3 / Nuxt 4 application guidelines

### 10.3 Related Services

<!-- Services that this service directly depends on or is depended upon. -->

| Service | Relationship | Description |
|---------|-------------|-------------|
| `{other-suite}-{other-domain}-svc` | upstream | ... |
| ... | downstream | ... |

### 10.4 Team & Ownership

| | |
|---|---|
| **Team** | {team-name} |
| **Email** | {team-email} |
| **Slack** | `#{slack-channel}` |
