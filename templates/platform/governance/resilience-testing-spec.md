<!-- Template Meta
     Template-ID:   TPL-RES
     Version:       1.0.0
     Last Updated:  2026-04-15
     Changelog:
       1.0.0 (2026-04-15) — Initial versioned baseline.
-->

# Resilience Testing Specification — {Scope}

> **Conceptual Stack Layer:** Governance
> **Space:** Platform
> **Owner:** Platform Engineering / SRE Team
> **References:** GOV-DORA-001, TPL-SLO, TPL-SVC §10.2

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `resilience-testing-spec.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections or "fully compliant"}
> - **Author(s):** Name(s)
> - **Status:** [DRAFT | REVIEW | APPROVED | DEPRECATED | RETIRED]
> - **Resilience Spec ID:** `RES-{SUITE}-{NNN}` or `RES-PLAT-{NNN}`
> - **Scope:** {platform | suite | service}

---

## §0. Scope

<!-- Define what this resilience testing specification covers: which services
     or suites, the service tier context, and the testing objectives. -->

### 0.1 Services in Scope

<!-- List the services / suites covered by this specification. -->

| Service / Suite | Service Tier | Owner | Notes |
|-----------------|-------------|-------|-------|
| `{service-id}` | {T1 | T2 | T3 | T4} | {team} | {notes} |

### 0.2 Testing Objectives

<!-- What does resilience testing aim to prove for the services in scope? -->

- {Objective 1: e.g., Verify RTO/RPO targets are achievable}
- {Objective 2: e.g., Validate failover mechanisms under realistic conditions}
- {Objective 3: e.g., Identify single points of failure}

---

## §1. Recovery Objectives

<!-- Define recovery targets per service tier.  T1 services (business-critical)
     MUST have the strictest targets. -->

| Service | Tier | RTO | RPO | MTTR Target | Availability Target |
|---------|------|-----|-----|-------------|---------------------|
| `{service-id}` | T1 | {minutes} | {minutes} | {minutes} | {99.9%+} |
| `{service-id}` | T2 | {minutes} | {minutes} | {minutes} | {99.5%+} |
| `{service-id}` | T3 | {hours}   | {hours}   | {hours}   | {99.0%+} |
| `{service-id}` | T4 | {hours}   | {hours}   | {hours}   | {95.0%+} |

<!-- Targets MUST align with the SLO definitions in the companion TPL-SLO
     specification for each service. -->

---

## §2. Failover Test Scenarios

<!-- Define specific failover scenarios to be tested regularly. -->

| Scenario ID | Name | Trigger Condition | Expected Behavior | Acceptance Criteria | Frequency | Last Tested | Result |
|-------------|------|-------------------|--------------------|--------------------|-----------|-------------|--------|
| FO-001 | Database failover | Primary DB instance failure | Automatic promotion of replica; application reconnects within RTO | Downtime < {N} seconds; zero data loss | {quarterly} | {date} | {PASS / FAIL / NOT TESTED} |
| FO-002 | Service instance failure | Pod / instance crash | Orchestrator restarts instance; traffic rerouted to healthy instances | No user-visible errors; recovery within {N} seconds | {quarterly} | {date} | {PASS / FAIL / NOT TESTED} |
| FO-003 | Message broker outage | Broker cluster unavailable | Producers buffer or retry; consumers resume processing after recovery | No message loss; processing resumes within {N} minutes | {quarterly} | {date} | {PASS / FAIL / NOT TESTED} |
| FO-004 | DNS failure | DNS resolution failure | Clients fall back to cached entries or alternative resolution | Service degradation within acceptable bounds | {annually} | {date} | {PASS / FAIL / NOT TESTED} |

<!-- Add additional scenarios as required. -->

---

## §3. Chaos Engineering

<!-- Structured chaos experiments to proactively discover weaknesses. -->

### 3.1 Approach

- **Steady-State Hypothesis:** Define normal operating conditions before each experiment.
- **Experiment Design:** Target a specific failure mode with controlled blast radius.
- **Blast Radius Control:** Limit experiments to non-production or canary environments initially; expand to production only with safeguards.
- **Abort Conditions:** Define conditions under which the experiment MUST be terminated immediately.

### 3.2 Experiment Catalog

| Experiment ID | Target | Fault Injected | Blast Radius | Steady State Hypothesis | Abort Condition |
|---------------|--------|----------------|--------------|-------------------------|-----------------|
| CE-001 | {service / component} | {fault type} | {scope} | {hypothesis} | {condition} |

### 3.3 Tools

<!-- Select and document the chaos engineering tooling. -->

- **Primary Tool:** {Chaos Monkey / Litmus / Gremlin / equivalent}
- **Integration:** {How the tool integrates with the platform — Kubernetes operator, CI/CD hook, etc.}

---

## §4. Load and Stress Testing

<!-- Establish baseline performance and identify breaking points. -->

### 4.1 Baseline Performance

<!-- Document current baseline metrics under normal load. -->

| Metric | Baseline Value | Measurement Date |
|--------|---------------|------------------|
| Throughput (requests/sec) | {value} | {date} |
| Latency p50 | {value} | {date} |
| Latency p95 | {value} | {date} |
| Latency p99 | {value} | {date} |
| Error rate | {value} | {date} |

### 4.2 Test Scenarios

| Test Type | Load Profile | Duration | Success Criteria | Last Run | Result |
|-----------|-------------|----------|------------------|----------|--------|
| Load test | {N} concurrent users / {N} req/sec | {duration} | Latency p99 < {N}ms; error rate < {N}% | {date} | {PASS / FAIL / NOT TESTED} |
| Stress test | {N}x baseline load, ramping | {duration} | Graceful degradation; no cascading failures | {date} | {PASS / FAIL / NOT TESTED} |
| Soak test | {N} sustained load | {duration} | No memory leaks; stable latency over time | {date} | {PASS / FAIL / NOT TESTED} |
| Spike test | {N}x sudden burst | {duration} | Auto-scaling triggers within {N} seconds | {date} | {PASS / FAIL / NOT TESTED} |

### 4.3 Tools

- **Primary Tool:** {k6 / JMeter / Gatling / equivalent}
- **Execution Environment:** {dedicated load test cluster / CI/CD pipeline / ...}

---

## §5. Backup and Recovery Testing

<!-- Verify that backup and restore procedures meet RTO/RPO targets. -->

| Service | Backup Strategy | Backup Frequency | Retention Period | Restore Procedure | Restore Test Frequency | Restore Time Target | Last Tested | Result |
|---------|----------------|------------------|------------------|--------------------|----------------------|---------------------|-------------|--------|
| `{service-id}` | {full / incremental / differential} | {frequency} | {period} | {procedure reference} | {frequency} | {must be within RTO} | {date} | {PASS / FAIL / NOT TESTED} |

<!-- Restore time targets MUST be within the RTO defined in §1 for each
     service's tier. -->

---

## §6. DORA Threat-Led Penetration Testing (TLPT)

<!-- TLPT is required only for certain financial entities under DORA Art. 26-27.
     If not applicable, state "Not applicable — entity is not in scope for
     TLPT" and skip the subsections. -->

### 6.1 Applicability

<!-- Is this entity subject to TLPT requirements under DORA Art. 26-27? -->

{Applicable / Not applicable} — {justification}

### 6.2 Methodology

<!-- TIBER-EU or equivalent threat-intelligence-based methodology. -->

- **Framework:** {TIBER-EU / equivalent}
- **Testing Provider Requirements:** {independent, certified, no conflict of interest}
- **Scope Determination:** {critical functions and systems identified per DORA Art. 26(2)}

### 6.3 Reporting Requirements

<!-- How TLPT results are documented and shared with competent authorities. -->

- **Report Format:** {format}
- **Distribution:** {internal stakeholders, competent authority}
- **Remediation Tracking:** {process for tracking and closing findings}

---

## §7. Annual Test Schedule

<!-- Calendar-style overview of all resilience testing activities. -->

| Quarter | Test Type | Scope | Owner | Status |
|---------|-----------|-------|-------|--------|
| Q1 | {Failover tests} | {services / suites} | {team} | {PLANNED / COMPLETED / DEFERRED} |
| Q1 | {Backup restore tests} | {services / suites} | {team} | {PLANNED / COMPLETED / DEFERRED} |
| Q2 | {Load / stress tests} | {services / suites} | {team} | {PLANNED / COMPLETED / DEFERRED} |
| Q2 | {Chaos experiments} | {services / suites} | {team} | {PLANNED / COMPLETED / DEFERRED} |
| Q3 | {Failover tests} | {services / suites} | {team} | {PLANNED / COMPLETED / DEFERRED} |
| Q3 | {TLPT (if applicable)} | {critical functions} | {team / provider} | {PLANNED / COMPLETED / DEFERRED} |
| Q4 | {Load / stress tests} | {services / suites} | {team} | {PLANNED / COMPLETED / DEFERRED} |
| Q4 | {Full DR exercise} | {platform} | {team} | {PLANNED / COMPLETED / DEFERRED} |

---

## §8. Reporting and Evidence

<!-- How test results are documented, retained, and distributed. -->

### 8.1 Test Report Format

<!-- Standardized report structure for all resilience tests. -->

- Executive summary
- Test scope and objectives
- Test methodology and environment
- Results and findings
- Deviations from expected behavior
- Corrective actions required
- Sign-off

### 8.2 Evidence Retention

- **Retention Period:** 5 years (minimum, per DORA requirements)
- **Storage Location:** {location}
- **Access Control:** {who can access test evidence}

### 8.3 Distribution

<!-- Who receives test reports and when. -->

| Stakeholder | Report Type | Frequency |
|-------------|------------|-----------|
| {SRE / Platform Team} | Full report | After each test |
| {Management} | Executive summary | Quarterly |
| {Compliance / Audit} | Full report | On request / annually |

### 8.4 Corrective Action Tracking

<!-- How findings from resilience tests are tracked to resolution. -->

- **Tracking Tool:** {Jira / equivalent}
- **SLA for Remediation:** Critical — {N} days; High — {N} days; Medium — {N} days
- **Escalation Path:** {escalation process}

---

## §9. Change Log

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
| Reviewer (Security) | {name} | {date} | {APPROVED / CHANGES REQUESTED} |
| Approver | {name} | {date} | {APPROVED / REJECTED} |
