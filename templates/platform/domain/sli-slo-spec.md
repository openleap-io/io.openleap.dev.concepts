<!-- Template Meta
     Template-ID:   TPL-SLO
     Version:       1.0.0
     Last Updated:  2026-04-15
     Changelog:
       1.0.0 (2026-04-15) — Initial versioned baseline.
-->

# SLI/SLO Specification — {SUITE} {DOMAIN} {Service}

> **Conceptual Stack Layer:** Domain / Service
> **Space:** Platform
> **Owner:** SRE / Domain Engineering Team
> **References:** TPL-SVC (companion), GOV-DORA-003 (Incident Management)

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `sli-slo-spec.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections or "fully compliant"}
> - **Author(s):** Name(s)
> - **Status:** [DRAFT | REVIEW | APPROVED | DEPRECATED | RETIRED]
> - **SLO Spec ID:** `SLO-{SUITE}-{DOMAIN}-{NNN}`
> - **Service Reference:** `{service-id}` (from Domain/Service Spec)
> - **Service Tier:** {T1 | T2 | T3 | T4}

---

## §0. Service Identity

<!-- Link this SLI/SLO specification to the companion TPL-SVC domain/service
     specification.  This section establishes context; the detailed service
     description lives in the TPL-SVC document. -->

| Attribute | Value |
|-----------|-------|
| **Service ID** | `{suite}-{domain}-svc` |
| **Companion Spec** | `{service-id}-spec.md` (TPL-SVC) |
| **Service Tier** | {T1 | T2 | T3 | T4} |
| **Business Criticality** | {description of business impact if this service is degraded or unavailable} |
| **Suite** | `{suite}` |
| **Domain** | `{domain}` |

---

## §1. Service Level Indicators (SLIs)

<!-- Define the measurable indicators of service health.  Each SLI MUST have
     a clear definition, measurement method, and "good event" definition. -->

| SLI ID | Metric Name | Definition | Measurement Method | Data Source | Unit | Good Event Definition |
|--------|-------------|------------|--------------------|-------------|------|-----------------------|
| SLI-001 | Availability | Proportion of successful requests out of total requests | `count(status < 500) / count(total)` | {load balancer logs / application metrics} | % | HTTP status < 500 |
| SLI-002 | Latency (p50) | 50th percentile request duration | Histogram bucket analysis | {APM / metrics pipeline} | ms | Response time <= {N}ms |
| SLI-003 | Latency (p95) | 95th percentile request duration | Histogram bucket analysis | {APM / metrics pipeline} | ms | Response time <= {N}ms |
| SLI-004 | Latency (p99) | 99th percentile request duration | Histogram bucket analysis | {APM / metrics pipeline} | ms | Response time <= {N}ms |
| SLI-005 | Error Rate | Proportion of error responses out of total requests | `count(status >= 500) / count(total)` | {load balancer logs / application metrics} | % | HTTP status < 500 |
| SLI-006 | Throughput | Requests processed per second | Counter rate | {metrics pipeline} | req/s | N/A (informational) |

<!-- Add additional SLIs as required for the service. -->

---

## §2. Service Level Objectives (SLOs)

<!-- Define targets for each SLI.  SLOs express the reliability contract with
     consumers of this service. -->

| SLO ID | SLI Reference | Target | Measurement Window | Consequence of Breach |
|--------|--------------|--------|--------------------|-----------------------|
| SLO-001 | SLI-001 (Availability) | >= {99.9}% | 30-day rolling window | {Trigger error budget policy — see §3} |
| SLO-002 | SLI-003 (Latency p95) | <= {N}ms | 30-day rolling window | {Trigger error budget policy — see §3} |
| SLO-003 | SLI-004 (Latency p99) | <= {N}ms | 30-day rolling window | {Trigger error budget policy — see §3} |
| SLO-004 | SLI-005 (Error Rate) | <= {N}% | 30-day rolling window | {Trigger error budget policy — see §3} |

<!-- SLO targets SHOULD be informed by:
     - Business requirements and user expectations
     - Historical performance data
     - Dependency SLOs (a service cannot be more reliable than its dependencies)
     - Service tier (T1 services have stricter targets than T4) -->

---

## §3. Error Budget

<!-- The error budget quantifies how much unreliability is acceptable within
     the SLO target period. -->

### 3.1 Calculation

<!-- Error budget = 1 - SLO target.  For a 99.9% availability SLO over a
     30-day window, the error budget is 0.1% = ~43.2 minutes of downtime. -->

| SLO | Target | Error Budget | Allowed Downtime (30 days) |
|-----|--------|-------------|---------------------------|
| SLO-001 (Availability) | {99.9}% | {0.1}% | ~{43.2} minutes |

### 3.2 Burn Rate Alerting

<!-- Burn rate measures how quickly the error budget is being consumed.
     Fast burns require immediate response; slow burns indicate chronic issues. -->

| Alert Level | Burn Rate | Lookback Window | Response |
|-------------|-----------|-----------------|----------|
| Page (fast burn) | 14.4x | 1 hour | Immediate incident response |
| Page (fast burn) | 6x | 6 hours | Urgent investigation |
| Ticket (slow burn) | 3x | 1 day | Scheduled investigation |
| Ticket (slow burn) | 1x | 3 days | Track in next sprint |

### 3.3 Error Budget Policy

<!-- What happens when the error budget is exhausted? -->

**When error budget is exhausted (0% remaining):**

1. **Freeze non-critical changes** — Only reliability improvements and critical bug fixes may be deployed.
2. **Focus on reliability** — Engineering effort shifts to root-cause analysis and reliability improvements.
3. **Post-mortem required** — Conduct a blameless post-mortem for the budget exhaustion event.
4. **Stakeholder notification** — Notify {stakeholders} of the reliability freeze.

**When error budget drops below {25}%:**

1. **Increased scrutiny** — All deployments require additional review for reliability impact.
2. **Proactive investigation** — Begin investigating top contributors to budget consumption.

---

## §4. Alerting Configuration

<!-- Map SLO breaches and burn rates to alert channels and incident severity
     levels.  Severity levels align with GOV-DORA-003 incident classification. -->

| Alert Name | Condition | Severity | Channel | Escalation |
|------------|-----------|----------|---------|------------|
| `{service}-availability-fast-burn` | 14.4x burn rate over 1h | P1 — Critical | {PagerDuty / OpsGenie} | Immediate — on-call SRE |
| `{service}-availability-slow-burn` | 3x burn rate over 6h | P2 — High | {Slack channel} | Within 1 hour — on-call SRE |
| `{service}-latency-p99-breach` | p99 > {N}ms for > 15 min | P2 — High | {Slack channel} | Within 1 hour — on-call SRE |
| `{service}-error-rate-spike` | Error rate > {N}% for > 5 min | P1 — Critical | {PagerDuty / OpsGenie} | Immediate — on-call SRE |
| `{service}-error-budget-low` | Error budget < {25}% remaining | P3 — Medium | {Slack channel / email} | Next business day — service owner |

<!-- Add additional alerts as required. -->

---

## §5. Dashboard Requirements

<!-- Define what MUST be visible on the service SLO dashboard. -->

### 5.1 Required Visualizations

- **SLI trends** — Time-series graphs for each SLI over the measurement window.
- **SLO compliance** — Current compliance percentage per SLO with target line.
- **Error budget remaining** — Gauge or time-series showing remaining error budget.
- **Burn rate** — Current burn rate with threshold indicators.
- **Deployment markers** — Overlay deployment events on SLI graphs for correlation.

### 5.2 Access Control

| Access Level | Who | Permissions |
|-------------|-----|-------------|
| View | All engineering teams | Read-only |
| Edit | SRE team, service owner | Modify dashboard configuration |
| Admin | SRE lead | Manage alerting rules and thresholds |

### 5.3 Dashboard Location

- **Tool:** {Grafana / Datadog / equivalent}
- **URL:** {dashboard URL}

---

## §6. Review Cadence

<!-- SLOs are living targets that SHOULD be reviewed regularly to ensure they
     remain appropriate. -->

### 6.1 Review Schedule

| Activity | Frequency | Participants |
|----------|-----------|-------------|
| SLO compliance review | Quarterly | Service owner, SRE, product owner |
| SLO target adjustment | Annually (or on significant change) | Service owner, SRE, product owner, stakeholders |
| Error budget review | Monthly | Service owner, SRE |
| Dashboard accuracy check | Quarterly | SRE |

### 6.2 Adjustment Criteria

<!-- When SHOULD SLO targets be changed? -->

- SLO is consistently exceeded by a wide margin (target may be too loose).
- SLO is consistently missed despite reasonable effort (target may be too aggressive).
- Business requirements change (e.g., service tier upgrade).
- Dependency landscape changes (new or removed dependencies).
- User expectations shift (based on feedback or market analysis).

### 6.3 Escalation for Consistently Missed SLOs

<!-- If an SLO is missed for {N} consecutive measurement windows: -->

1. Root-cause analysis MUST be completed.
2. Remediation plan MUST be presented to {stakeholders}.
3. If remediation is infeasible, SLO target MUST be renegotiated with stakeholders.
4. Service tier re-evaluation MAY be triggered.

---

## §7. Change Log

<!-- Track material changes to this specification. -->

| Date | Version | Author | Change Description |
|------|---------|--------|--------------------|
| {YYYY-MM-DD} | 1.0.0 | {author} | Initial version |

---

## Review & Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Author | {name} | {date} | DRAFTED |
| Reviewer (SRE) | {name} | {date} | {APPROVED / CHANGES REQUESTED} |
| Reviewer (Domain Lead) | {name} | {date} | {APPROVED / CHANGES REQUESTED} |
| Approver | {name} | {date} | {APPROVED / REJECTED} |
