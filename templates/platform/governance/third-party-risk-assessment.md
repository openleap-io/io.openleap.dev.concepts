<!-- Template Meta
     Template-ID:   TPL-TPR
     Version:       1.0.0
     Last Updated:  2026-04-15
     Changelog:
       1.0.0 (2026-04-15) — Initial versioned baseline.
-->

# Third-Party Risk Assessment — {Provider Name}

> **Conceptual Stack Layer:** Governance
> **Space:** Platform
> **Owner:** Security / Procurement Team
> **References:** GOV-DORA-005 (Third-Party Governance)

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `third-party-risk-assessment.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections or "fully compliant"}
> - **Author(s):** Name(s)
> - **Status:** [DRAFT | REVIEW | APPROVED | DEPRECATED | RETIRED]
> - **Assessment ID:** `TPR-{PROVIDER}-{NNN}`
> - **Classification:** {critical | non-critical}

---

## §0. Provider Identity

<!-- Basic identification of the third-party provider and the business
     relationship. -->

| Attribute | Value |
|-----------|-------|
| **Provider Name** | {provider name} |
| **Service Provided** | {description of service} |
| **Contract Start Date** | {YYYY-MM-DD} |
| **Contract End Date** | {YYYY-MM-DD} |
| **Contract Renewal** | {automatic / manual — next review date} |
| **Business Criticality** | {critical / non-critical} |
| **Primary Contact** | {name, email} |

### 0.1 Services Consuming This Provider

<!-- Which internal services depend on this third party? -->

| Service / Suite | Dependency Type | Impact if Unavailable |
|-----------------|----------------|-----------------------|
| `{service-id}` | {runtime / build-time / data} | {description of impact} |

---

## §1. Service Description

<!-- Detailed description of what the provider delivers and the data
     relationship. -->

### 1.1 Service Scope

<!-- What does the provider provide?  Be specific about functionality,
     capacity, and boundaries. -->

### 1.2 Service Level Agreements

| SLA Metric | Target | Measurement | Penalty |
|------------|--------|-------------|---------|
| Availability | {target}% | {method} | {penalty clause} |
| Latency | {target} | {method} | {penalty clause} |
| Support response | {target} | {method} | {penalty clause} |

### 1.3 Data Processing

| Attribute | Value |
|-----------|-------|
| **Data Processed / Stored** | {description of data} |
| **Data Classification** | {public / internal / confidential / restricted} |
| **Processing Locations** | {country / region} |
| **Data Residency Requirements** | {EU-only / specific jurisdiction / none} |
| **Encryption in Transit** | {TLS version / protocol} |
| **Encryption at Rest** | {algorithm / key management} |

---

## §2. Risk Assessment

<!-- Evaluate the provider's security posture and assign a risk rating. -->

### 2.1 Certifications and Audits

| Certification | Held | Valid Until | Scope | Notes |
|---------------|------|------------|-------|-------|
| ISO 27001 | {Yes / No} | {date} | {scope} | {notes} |
| SOC 2 Type II | {Yes / No} | {date} | {scope} | {notes} |
| SOC 1 Type II | {Yes / No} | {date} | {scope} | {notes} |
| ISO 22301 | {Yes / No} | {date} | {scope} | {notes} |
| CSA STAR | {Yes / No} | {date} | {scope} | {notes} |

### 2.2 Security Posture

| Area | Assessment | Evidence |
|------|-----------|----------|
| Last independent audit date | {date} | {report reference} |
| Security incident history (last 3 years) | {summary} | {disclosure / report} |
| Vulnerability management practice | {description} | {policy reference} |
| Penetration testing frequency | {frequency} | {report reference} |
| Business continuity / DR capability | {description} | {plan reference} |

### 2.3 Risk Rating

| Risk Category | Rating | Justification |
|---------------|--------|---------------|
| Security risk | {Low / Medium / High / Critical} | {justification} |
| Operational risk | {Low / Medium / High / Critical} | {justification} |
| Compliance risk | {Low / Medium / High / Critical} | {justification} |
| **Overall risk** | {Low / Medium / High / Critical} | {justification} |

---

## §3. Contractual Review

<!-- DORA Art. 30 checklist — verify that the contract contains all required
     provisions for ICT third-party service arrangements. -->

| Clause | DORA Reference | Present in Contract | Notes |
|--------|---------------|---------------------|-------|
| Service levels and performance targets | Art. 30(2)(a) | {Yes / No / Partial} | {notes} |
| Data processing locations | Art. 30(2)(a) | {Yes / No / Partial} | {notes} |
| Security measures | Art. 30(2)(b) | {Yes / No / Partial} | {notes} |
| Audit rights | Art. 30(2)(c) | {Yes / No / Partial} | {notes} |
| Incident notification obligations | Art. 30(2)(d) | {Yes / No / Partial} | {notes} |
| Sub-outsourcing provisions | Art. 30(2)(e) | {Yes / No / Partial} | {notes} |
| Exit provisions | Art. 30(2)(f) | {Yes / No / Partial} | {notes} |
| Data return and deletion | Art. 30(2)(f) | {Yes / No / Partial} | {notes} |

<!-- Any clause marked "No" or "Partial" MUST have a remediation action
     tracked in the notes column or a separate action item. -->

---

## §4. Concentration Risk

<!-- Assess dependency concentration on this provider. -->

| Metric | Value |
|--------|-------|
| **Number of services depending on this provider** | {N} |
| **Percentage of critical path** | {N}% |
| **Alternative providers identified** | {Yes / No — list if yes} |
| **Switching feasibility** | {Easy / Moderate / Difficult / Infeasible} |
| **Estimated transition time** | {duration} |

### 4.1 Dependency Analysis

<!-- Describe the nature of the dependency.  Is the provider easily replaceable?
     Are there proprietary lock-in factors? -->

### 4.2 Mitigation Measures

<!-- What measures are in place to reduce concentration risk? -->

- {Measure 1: e.g., Multi-provider strategy for critical capability}
- {Measure 2: e.g., Abstraction layer to enable provider switching}

---

## §5. Exit Strategy

<!-- Define the plan for transitioning away from this provider if required. -->

### 5.1 Data Portability Assessment

| Aspect | Assessment |
|--------|-----------|
| Data export format available | {Yes / No — format details} |
| API for bulk data extraction | {Yes / No — API details} |
| Proprietary data formats | {Yes / No — impact} |
| Data portability score | {High / Medium / Low} |

### 5.2 Alternative Providers

| Provider | Capability Match | Evaluation Status | Migration Effort |
|----------|-----------------|-------------------|-----------------|
| {provider} | {High / Medium / Low} | {Evaluated / Shortlisted / Not evaluated} | {estimate} |

### 5.3 Transition Plan

| Phase | Activity | Duration | Owner |
|-------|----------|----------|-------|
| 1 | {Notification and planning} | {duration} | {owner} |
| 2 | {Data migration} | {duration} | {owner} |
| 3 | {Service cutover} | {duration} | {owner} |
| 4 | {Validation and decommission} | {duration} | {owner} |

### 5.4 Data Deletion Confirmation

<!-- Process for confirming that the provider has deleted all data after
     transition. -->

- **Deletion request process:** {process}
- **Confirmation mechanism:** {certificate of destruction / audit / ...}
- **Timeline:** {within N days of contract termination}

---

## §6. Monitoring Plan

<!-- Ongoing assessment and continuous monitoring of the provider. -->

### 6.1 Continuous Monitoring

| Mechanism | Frequency | Owner | Notes |
|-----------|-----------|-------|-------|
| SLA performance review | {monthly / quarterly} | {team} | {notes} |
| Security posture reassessment | {annually / bi-annually} | {team} | {notes} |
| Certification renewal tracking | {as needed} | {team} | {notes} |
| Incident feed monitoring | {continuous} | {team} | {notes} |

### 6.2 Re-Assessment Triggers

<!-- Events that trigger an immediate reassessment outside the regular
     review schedule. -->

- Material security incident at the provider
- Change in provider ownership or management
- Significant service outage exceeding SLA
- Change in data processing locations
- Sub-outsourcing change affecting critical services
- Regulatory change affecting the arrangement

### 6.3 Escalation Criteria

<!-- When does a monitoring finding escalate to management? -->

| Severity | Criteria | Escalation Path |
|----------|----------|-----------------|
| Critical | {criteria} | {immediate escalation to CISO / management} |
| High | {criteria} | {escalation within N business days} |
| Medium | {criteria} | {tracked in regular review cycle} |

### 6.4 Review Schedule

| Review Type | Frequency | Next Scheduled | Owner |
|-------------|-----------|----------------|-------|
| Operational review | {monthly / quarterly} | {date} | {team} |
| Full risk reassessment | {annually} | {date} | {team} |
| Contract review | {annually / at renewal} | {date} | {team} |

---

## §7. SBOM Integration

<!-- How this provider's components appear in service SBOMs and how
     dependency vulnerabilities are tracked. -->

| Aspect | Details |
|--------|---------|
| **Provider components in SBOM** | {description of how provider libraries / components are tracked} |
| **Dependency tracking method** | {automated scanning / manual inventory / ...} |
| **Vulnerability feed integration** | {provider security advisories / CVE feeds / ...} |
| **Update policy** | {how quickly provider-originated vulnerabilities must be patched} |

---

## §8. Change Log

<!-- Track material changes to this assessment. -->

| Date | Version | Author | Change Description |
|------|---------|--------|--------------------|
| {YYYY-MM-DD} | 1.0.0 | {author} | Initial assessment |

---

## Review & Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Author | {name} | {date} | DRAFTED |
| Reviewer (Security) | {name} | {date} | {APPROVED / CHANGES REQUESTED} |
| Reviewer (Procurement) | {name} | {date} | {APPROVED / CHANGES REQUESTED} |
| Approver | {name} | {date} | {APPROVED / REJECTED} |
