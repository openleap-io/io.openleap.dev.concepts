# Third-Party ICT Provider & Supply Chain Governance

> **Document Type:** Platform Governance
> **ID:** GOV-DORA-005
> **Version:** 1.0 — 2026-04-15
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** Third-party ICT service provider risk management and supply chain security
> **DORA Articles:** 28–44 (ICT Third-Party Risk Management)
> **Parent:** GOV-DORA-001 (DORA Compliance Framework)

---

## 1. Purpose

DORA requires financial entities to manage risks arising from ICT
third-party service providers throughout the entire lifecycle of the
relationship. This includes due diligence before engagement, ongoing
monitoring, and orderly exit planning.

This policy defines how third-party providers and software dependencies
are assessed, monitored, and documented within the OpenLeap
specification framework. It covers both external service providers
(cloud, identity, infrastructure) and software supply chain concerns
(open-source libraries, transitive dependencies).

---

## 2. Third-Party Classification

All ICT third-party providers (TPPs) MUST be classified into one of two
categories. Classification determines assessment frequency, contractual
depth, and monitoring intensity.

### 2.1 Classification Categories

| Category | Definition | Examples |
|---|---|---|
| **Critical ICT TPP** | Provider whose failure would materially impact business operations, data integrity, or regulatory compliance | Cloud providers (AWS, Azure, GCP), core infrastructure services, identity providers, payment processors |
| **Non-critical ICT TPP** | Provider whose failure has limited or contained impact on business operations | Analytics tools, optional integrations, development tooling, non-essential monitoring |

### 2.2 Classification Criteria

| Criterion | Critical Indicator |
|---|---|
| **Business impact** | Failure causes service outage or degraded core functionality |
| **Data sensitivity** | Processes or stores personal data, financial data, or regulatory data |
| **Replaceability** | No viable alternative available within acceptable timeframe |
| **Regulatory exposure** | Failure would trigger regulatory notification obligations |
| **User impact** | Affects end-user-facing services directly |
| **Financial impact** | Failure results in direct financial loss or penalties |

A provider is classified as **Critical** if it meets two or more
critical indicators. Classification MUST be reviewed annually or when
the provider relationship changes materially.

---

## 3. Risk Assessment Requirements

### 3.1 Due Diligence Checklist

Before engaging any ICT third-party provider, the following MUST be
assessed:

| Area | Assessment Items |
|---|---|
| **Security certifications** | ISO 27001, SOC 2 Type II, or equivalent |
| **Data processing locations** | Jurisdictions where data is processed and stored; EU adequacy decisions |
| **Sub-outsourcing chain** | Identification of material sub-contractors; notification obligations |
| **Business continuity** | BCP/DR capabilities, RTO/RPO commitments, tested recovery procedures |
| **Incident response** | Incident detection, notification timelines, cooperation commitments |
| **Financial stability** | Financial health indicators, insurance coverage, credit assessments |
| **Regulatory compliance** | Compliance with applicable regulations (DORA, GDPR, sector-specific) |
| **Exit provisions** | Data portability, transition support, contract termination terms |

### 3.2 Assessment Frequency

| Classification | Full Assessment | Interim Review |
|---|---|---|
| **Critical ICT TPP** | Annually | Quarterly KPI monitoring |
| **Non-critical ICT TPP** | Biennially | Annual KPI monitoring |

Assessment results MUST be documented in the TPL-TPR (Third-Party Risk
Assessment) template for each provider.

---

## 4. Contractual Requirements

DORA Art. 30 mandates specific contractual provisions for ICT
third-party service arrangements. Every contract with an ICT TPP MUST
include the following clauses.

### 4.1 Mandated Clauses Checklist

| Clause | DORA Reference | Description | Compliance Status |
|---|---|---|---|
| Service level descriptions | Art. 30(2)(a) | Clear, quantitative SLAs with measurable targets | {COMPLIANT / GAP / N/A} |
| Data processing locations | Art. 30(2)(b) | Specification of data processing and storage locations | {COMPLIANT / GAP / N/A} |
| Data security measures | Art. 30(2)(c) | Technical and organizational security measures | {COMPLIANT / GAP / N/A} |
| Availability guarantees | Art. 30(2)(d) | Uptime commitments and performance benchmarks | {COMPLIANT / GAP / N/A} |
| Audit rights | Art. 30(2)(e) | Right to audit or inspect the provider, including on-site | {COMPLIANT / GAP / N/A} |
| Incident notification | Art. 30(2)(f) | Obligation to report incidents within defined timeframes | {COMPLIANT / GAP / N/A} |
| Business continuity | Art. 30(2)(g) | BCP/DR provisions and testing obligations | {COMPLIANT / GAP / N/A} |
| Exit strategy provisions | Art. 30(2)(h) | Termination rights, transition periods, data return | {COMPLIANT / GAP / N/A} |
| Sub-outsourcing limitations | Art. 30(2)(i) | Prior consent for sub-outsourcing; chain transparency | {COMPLIANT / GAP / N/A} |
| Cooperation with authorities | Art. 30(3) | Provider cooperation with regulatory authorities | {COMPLIANT / GAP / N/A} |

### 4.2 Contract Review

All ICT TPP contracts MUST be reviewed:
- At renewal for continued compliance
- When DORA or related regulations change
- When the provider's classification changes (e.g., non-critical to critical)

---

## 5. SBOM Requirements

Every deployed service within the OpenLeap platform MUST maintain a
current Software Bill of Materials (SBOM).

### 5.1 SBOM Format

| Attribute | Requirement |
|---|---|
| **Format** | CycloneDX (preferred) or SPDX |
| **Content** | All direct and transitive dependencies with versions, licenses, and known vulnerabilities |
| **Generation** | Automated in CI/CD pipeline (ref TPL-PIPE §3) |
| **Storage** | Alongside build artifacts; retained for **5 years** |
| **Update frequency** | Every build |

### 5.2 SBOM Content Requirements

Each SBOM entry MUST include:
- Component name and version
- Package URL (purl) identifier
- License identifier (SPDX expression)
- Hash/checksum for integrity verification
- Known vulnerabilities at time of generation (CVE IDs)
- Supplier / author information (where available)

### 5.3 SBOM Lifecycle

1. **Generation** — Automated during CI/CD build (ref TPL-PIPE §3)
2. **Validation** — SBOM completeness check as pipeline gate
3. **Storage** — Published alongside the build artifact
4. **Monitoring** — Continuous scanning against updated vulnerability databases
5. **Retention** — Minimum 5 years for regulatory traceability

---

## 6. Dependency Monitoring

### 6.1 Continuous Vulnerability Scanning

All service dependencies MUST be continuously monitored for known
vulnerabilities using automated scanning tools (Dependabot, Snyk, or
equivalent).

### 6.2 CVE Response Process

| Severity | CVSS Score | Response Time | Action |
|---|---|---|---|
| **Critical** | >= 9.0 | Within **24 hours** | Immediate patching or mitigation; incident notification if exploited |
| **High** | 7.0–8.9 | Within **7 days** | Priority patching; risk assessment if deferral needed |
| **Medium** | 4.0–6.9 | Within **30 days** | Scheduled patching in next maintenance window |
| **Low** | < 4.0 | **Next release cycle** | Addressed in regular dependency updates |

### 6.3 Automated Alerts

- New CVE disclosures in any dependency MUST trigger automated alerts
  to the responsible service team
- Critical and High CVEs MUST also notify the Security Team
- Alert channels: team Slack channel, email, and ticketing system

### 6.4 Dependency Update Policy

- Dependencies SHOULD be kept within one major version of the latest
  stable release
- End-of-life dependencies MUST be replaced within 90 days of EOL
  announcement
- Dependency updates MUST pass the full CI/CD pipeline before deployment

---

## 7. Specification Integration

This governance document connects to the specification framework at
multiple points:

| Template / Spec | Integration Point | Purpose |
|---|---|---|
| **TPL-TPR** (Third-Party Risk Assessment) | Instantiated per provider | Documents due diligence, risk assessment, and ongoing monitoring per provider |
| **TPL-SVC §9.5** | SBOM requirements per service | Each service spec references its SBOM location and generation process |
| **TPL-PIPE §3** | SBOM generation in pipeline | Pipeline template includes SBOM generation and validation steps |
| **Product specs** | External interfaces section | Product-level specs reference assessed providers for external integrations |
| **TPL-RISK** | Risk register entries | Third-party risks are captured in the ICT risk register |

### 7.1 Specification Author Obligations

When writing or updating a service specification:
- List all third-party dependencies in the external interfaces section
- Reference the corresponding TPL-TPR assessment for each critical provider
- Include the SBOM generation step in the deployment pipeline section
- Document any concentration risks (see §8)

---

## 8. Concentration Risk

### 8.1 Assessment Requirements

Financial entities MUST avoid over-reliance on a single ICT third-party
provider. For each critical provider, assess:

| Question | Assessment |
|---|---|
| What percentage of critical services depend on this single provider? | {percentage} |
| Is there a viable alternative provider? | {yes/no — details} |
| What is the estimated switching cost (time, effort, financial)? | {estimate} |
| What is the maximum tolerable outage duration? | {duration} |
| Does the provider also serve as TPP for competitors? | {yes/no} |

### 8.2 Mitigation Strategies

- **Multi-cloud / multi-provider** strategies for critical infrastructure
- **Abstraction layers** to reduce provider lock-in (e.g., Kubernetes
  for compute, standard APIs for identity)
- **Regular testing** of failover to alternative providers
- **Contractual protections** ensuring data portability

### 8.3 Documentation

Concentration risk per critical provider MUST be documented in the
TPL-TPR §4 (Concentration Risk Assessment) section.

---

## 9. Exit Strategy Requirements

Every critical third-party relationship MUST have a documented exit plan
covering the following areas.

### 9.1 Exit Plan Components

| Component | Requirements |
|---|---|
| **Data portability** | Documented export formats, export mechanisms, data completeness verification |
| **Transition timeline** | Realistic timeline for migration to an alternative provider |
| **Alternative providers** | At least one identified alternative with preliminary feasibility assessment |
| **Contractual provisions** | Orderly termination clauses, transition support obligations, data return/deletion |
| **Knowledge transfer** | Documentation of provider-specific configurations, customizations, integrations |
| **Testing** | Exit plan tested at least annually for critical providers |

### 9.2 Trigger Conditions

An exit plan MUST be activated when:
- Provider fails to meet SLA thresholds for two consecutive review periods
- Provider experiences a material security incident affecting the entity
- Provider enters insolvency or material financial distress
- Regulatory authority mandates termination of the arrangement
- Strategic decision to change providers

---

## 10. Decisions

| ID | Decision | Rationale |
|---|---|---|
| D-001 | CycloneDX as preferred SBOM format | Widely adopted in financial services, superior vulnerability correlation, strong tooling ecosystem. SPDX accepted as alternative for compatibility. |
| D-002 | Critical / non-critical binary classification for ICT TPPs | Aligns with DORA Art. 28 risk-based approach. Simpler than multi-tier classification while meeting regulatory requirements. Additional granularity can be captured within the TPL-TPR assessment. |
| D-003 | CVE response times differentiated by CVSS severity | Balances urgency with operational feasibility. Critical (24h) ensures rapid response to actively exploitable vulnerabilities; graduated timelines prevent alert fatigue for lower-severity issues. |

---

## 11. Open Questions

| ID | Question | Impact | Owner | Needed by |
|---|---|---|---|---|
| Q-001 | Which SBOM scanning tool will be standardized across the platform? | Affects pipeline template (TPL-PIPE) and cost | DevOps / Security | Phase 2 |
| Q-002 | Should the TPL-TPR assessment template support automated ingestion from provider security portals? | Reduces manual effort for ongoing monitoring | Architecture Team | Phase 3 |
| Q-003 | How will concentration risk thresholds be defined (e.g., max % of services on a single provider)? | Affects strategic architecture decisions | Architecture Team / Risk | Phase 2 |
