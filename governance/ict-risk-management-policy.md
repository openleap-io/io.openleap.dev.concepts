# ICT Risk Management Policy

> **Document Type:** Platform Governance
> **ID:** GOV-DORA-002
> **Version:** 1.0 — 2026-04-15
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** ICT risk management across all OpenLeap platform services and products
> **DORA Articles:** 5–16 (ICT Risk Management)
> **Parent:** GOV-DORA-001 (DORA Compliance Framework)

---

## 1. Purpose

ICT Risk Management is the **first pillar** of the Digital Operational
Resilience Act (DORA, EU Regulation 2022/2554). Articles 5 through 16
require financial entities — and by extension, their ICT service
providers — to establish a comprehensive framework for identifying,
assessing, treating, and monitoring ICT-related risks.

This policy defines how ICT risks are managed within the OpenLeap
specification lifecycle. It provides:

- A **risk taxonomy** aligned with DORA's scope of ICT risks
- A **risk assessment methodology** with quantitative scoring
- **Treatment options** with documentation requirements
- A **risk register specification** that feeds into TPL-RISK
- **Tier-differentiated risk appetite** aligned with the OpenLeap 4-tier
  service model
- **Specification integration** rules ensuring risk assessments flow into
  concrete specs

Every service, suite, and product specified on the OpenLeap platform
must have documented risk assessments. The depth and review frequency
vary by service tier (see §6), but no service is exempt from risk
identification.

---

## 2. Risk Taxonomy

All ICT risks within the OpenLeap platform are classified into five
categories. Every risk register entry (§5) must be assigned exactly one
primary category.

### 2.1 Infrastructure Risks

Risks arising from the underlying compute, storage, network, and hosting
infrastructure.

| Example | Description |
|---|---|
| Cloud provider outage | Regional or zonal failure in the hosting provider (e.g., AWS, Azure) affecting service availability |
| Network partition | Loss of connectivity between services, zones, or regions causing data inconsistency or unavailability |
| DNS failure | Domain resolution failure affecting service discovery and external access |
| Storage degradation | Disk I/O degradation or storage service throttling affecting database performance |
| Certificate expiry | TLS certificate expiration causing service-to-service or client-to-service communication failure |

### 2.2 Application Risks

Risks arising from software defects, design flaws, or runtime failures
in application code and APIs.

| Example | Description |
|---|---|
| Software bug | Logic errors causing incorrect data processing, financial miscalculation, or data corruption |
| API failure | Service endpoint returning errors, timeouts, or incorrect responses under load |
| Data corruption | Application-level data integrity violation (e.g., partial writes, inconsistent state) |
| Memory leak | Gradual resource exhaustion leading to service degradation or crash |
| Dependency failure | Failure in a library, framework, or internal dependency breaking application functionality |

### 2.3 Data Risks

Risks arising from the loss, exposure, or corruption of data managed by
platform services.

| Example | Description |
|---|---|
| Data breach | Unauthorized access to sensitive or personal data through exploitation or misconfiguration |
| Data loss | Permanent loss of data due to failed backups, accidental deletion, or storage failure |
| Integrity violation | Undetected modification of data (e.g., database tampering, event replay) |
| Privacy violation | Processing of personal data in violation of GDPR or national data protection law |
| Data exfiltration | Unauthorized extraction of data by malicious insider or compromised service |

### 2.4 Third-Party Risks

Risks arising from dependence on external vendors, open-source
components, or ICT service providers.

| Example | Description |
|---|---|
| Vendor failure | Critical vendor ceasing operations, discontinuing a product, or failing to meet SLAs |
| Supply chain compromise | Malicious code injected into a dependency (e.g., compromised npm package, container image) |
| API provider change | Breaking change in an external API that the platform depends on |
| Concentration risk | Over-reliance on a single vendor or provider for critical platform capabilities |
| License risk | Change in open-source license terms affecting the platform's ability to use a component |

### 2.5 Operational Risks

Risks arising from process failures, human error, or capacity
management shortcomings.

| Example | Description |
|---|---|
| Process failure | Deployment procedure error causing downtime or data loss (e.g., skipped migration step) |
| Human error | Misconfiguration of infrastructure, security rules, or application settings |
| Capacity exhaustion | Unexpected load growth exceeding provisioned capacity, causing degradation |
| Knowledge concentration | Critical platform knowledge held by a single individual with no backup |
| Change management failure | Uncoordinated changes across services causing integration failures |

---

## 3. Risk Assessment Methodology

### 3.1 Probability × Impact Matrix

Every risk is assessed on two dimensions:

**Probability** — How likely is the risk to materialize within the
assessment period (typically 12 months)?

| Score | Level | Description |
|---|---|---|
| 1 | Rare | May occur only in exceptional circumstances. Less than 5% probability. |
| 2 | Unlikely | Could occur but is not expected. 5–20% probability. |
| 3 | Possible | Might occur at some point. 20–50% probability. |
| 4 | Likely | Will probably occur in most circumstances. 50–80% probability. |
| 5 | Almost Certain | Expected to occur. Greater than 80% probability. |

**Impact** — What is the consequence if the risk materializes?

| Score | Level | Description |
|---|---|---|
| 1 | Negligible | No meaningful effect on operations or users. Resolved within normal operations. |
| 2 | Minor | Limited effect on a single service or small user group. Resolved within hours. |
| 3 | Moderate | Noticeable effect on multiple services or a significant user group. Resolved within one business day. |
| 4 | Major | Significant disruption to a suite or critical service. Financial or regulatory impact. Resolution requires days. |
| 5 | Catastrophic | Widespread platform failure, data breach, or regulatory sanction. Extended recovery. Reputational damage. |

### 3.2 Risk Score Calculation

```
Risk Score = Probability x Impact
```

The resulting score falls into one of four risk levels:

| Risk Score | Risk Level | Color | Required Action |
|---|---|---|---|
| 1–4 | **Low** | Green | Monitor. Document in risk register. No immediate action required. |
| 5–9 | **Medium** | Yellow | Treat within next review cycle. Assign owner. Document treatment plan. |
| 10–16 | **High** | Orange | Treat urgently. Escalate to governance board. Assign owner with deadline. |
| 17–25 | **Critical** | Red | Immediate action required. Escalate to CISO. Daily tracking until resolved or accepted at executive level. |

### 3.3 Risk Matrix Visualization

```
                        IMPACT
              1       2       3       4       5
         ┌───────┬───────┬───────┬───────┬───────┐
    5    │   5   │  10   │  15   │  20   │  25   │
         │  Med  │ High  │ High  │ Crit  │ Crit  │
         ├───────┼───────┼───────┼───────┼───────┤
    4    │   4   │   8   │  12   │  16   │  20   │
P        │  Low  │  Med  │ High  │ High  │ Crit  │
R        ├───────┼───────┼───────┼───────┼───────┤
O   3    │   3   │   6   │   9   │  12   │  15   │
B        │  Low  │  Med  │  Med  │ High  │ High  │
.        ├───────┼───────┼───────┼───────┼───────┤
    2    │   2   │   4   │   6   │   8   │  10   │
         │  Low  │  Low  │  Med  │  Med  │ High  │
         ├───────┼───────┼───────┼───────┼───────┤
    1    │   1   │   2   │   3   │   4   │   5   │
         │  Low  │  Low  │  Low  │  Low  │  Med  │
         └───────┴───────┴───────┴───────┴───────┘
```

### 3.4 CVSS Alignment

When a risk originates from a known vulnerability (e.g., CVE), the CVSS
v3.1 base score is mapped to the Impact dimension to ensure consistency
between vulnerability management and risk management:

| CVSS Score | CVSS Severity | Mapped Impact Level | Impact Score |
|---|---|---|---|
| 0.0 | None | Negligible | 1 |
| 0.1–3.9 | Low | Minor | 2 |
| 4.0–6.9 | Medium | Moderate | 3 |
| 7.0–8.9 | High | Major | 4 |
| 9.0–10.0 | Critical | Catastrophic | 5 |

The probability dimension is assessed independently based on
exploitability, exposure, and threat intelligence — CVSS alone does not
determine the full risk score.

---

## 4. Risk Treatment Options

Every identified risk must be treated using one of four strategies.
The chosen treatment must be documented in the risk register (§5) with
a rationale, an owner, and a deadline.

### 4.1 Mitigate

Implement controls to reduce the probability or impact of the risk.

- **When to use:** The risk is above the acceptable threshold for the
  service tier (§6), and effective controls exist.
- **Documentation:** Specify the control(s) to be implemented, the
  expected residual risk score after mitigation, and the implementation
  deadline.
- **Examples:** Adding circuit breakers to reduce cascade failure impact;
  implementing encryption at rest to reduce data breach impact;
  deploying redundant instances to reduce single-point-of-failure
  probability.

### 4.2 Accept

Acknowledge the risk and choose not to implement additional controls.

- **When to use:** The risk is within the acceptable threshold for the
  service tier, or the cost of mitigation exceeds the risk impact.
- **Documentation:** Document the rationale for acceptance, the
  approving authority (must be at the level defined in §6 for the risk
  level), and the next review date.
- **Approval requirements:**
  - Low risks: Service owner may accept.
  - Medium risks: Suite architect must approve.
  - High risks: Platform Architect and CISO must approve.
  - Critical risks: Executive-level approval required.

### 4.3 Transfer

Shift the risk to a third party through insurance, contractual
arrangements, or outsourcing.

- **When to use:** The risk cannot be cost-effectively mitigated
  internally, and a third party can absorb the impact.
- **Documentation:** Specify the transfer mechanism (insurance policy,
  contractual clause, SLA), the third party, and the residual risk
  retained by the platform.
- **DORA note:** Per Art. 28(1)(a), transferring risk to a third party
  does NOT transfer regulatory responsibility. The financial entity
  remains accountable.

### 4.4 Avoid

Eliminate the source of the risk entirely.

- **When to use:** The risk source can be removed without unacceptable
  impact on business functionality.
- **Documentation:** Specify what is being eliminated (e.g., deprecated
  dependency, removed feature, decommissioned service) and confirm that
  no residual risk remains from the eliminated source.
- **Examples:** Removing a legacy service with known unfixable
  vulnerabilities; replacing an unreliable third-party API with an
  in-house implementation.

---

## 5. Risk Register Specification

### 5.1 Register Structure

Risk register entries follow a standardized schema that feeds into the
TPL-RISK template. Each entry is uniquely identified and traceable to
affected services, suites, and DORA articles.

### 5.2 Entry Schema

| Field | Type | Description |
|---|---|---|
| **ID** | string | Unique identifier. Format: `RISK-{SUITE}-{NNN}` for suite-scoped risks or `RISK-PLAT-{NNN}` for platform-level risks. Example: `RISK-CAT-001`, `RISK-PLAT-012`. |
| **Title** | string | Short, descriptive title. Example: "Catalog service database single point of failure." |
| **Category** | enum | One of: Infrastructure, Application, Data, Third-Party, Operational (see §2). |
| **Description** | text | Detailed description of the risk, including trigger conditions and potential consequences. |
| **Probability** | integer (1–5) | Probability score per §3.1. |
| **Impact** | integer (1–5) | Impact score per §3.1. |
| **Risk Score** | integer (1–25) | Calculated: Probability x Impact. |
| **Risk Level** | enum | Derived from Risk Score: Low (1–4), Medium (5–9), High (10–16), Critical (17–25). |
| **Affected Services** | ref[] | List of service IDs (from TPL-SVC instances) affected by this risk. Example: `[srv.catalog, srv.identity]`. |
| **Affected Tiers** | enum[] | Service tiers affected: T1 (Platform), T2 (Shared Business), T3 (Core Business), T4 (Data/Analytics). |
| **Treatment** | enum | One of: Mitigate, Accept, Transfer, Avoid (see §4). |
| **Treatment Details** | text | Description of the treatment plan, including specific controls, contractual arrangements, or elimination steps. |
| **Controls** | ref[] | List of control IDs or descriptions implemented to mitigate the risk. |
| **Owner** | string | Person or role responsible for the risk and its treatment. |
| **Review Date** | date | Next scheduled review date. Must comply with the cadence in §6. |
| **Status** | enum | One of: `open` (identified, not yet treated), `mitigated` (controls implemented, residual risk accepted), `accepted` (risk accepted without additional controls), `closed` (risk eliminated or no longer applicable). |
| **DORA Articles** | ref[] | Applicable DORA articles. Used for audit traceability. |
| **Created** | date | Date the entry was created. |
| **Last Updated** | date | Date of the most recent update. |

### 5.3 Example Entry

```yaml
id: RISK-CAT-001
title: "Catalog service database single point of failure"
category: Infrastructure
description: >
  The catalog service uses a single PostgreSQL instance without
  read replicas or automatic failover. A database failure would
  make the entire catalog service unavailable, affecting all
  downstream services that depend on product data.
probability: 3  # Possible
impact: 4       # Major
risk_score: 12
risk_level: High
affected_services:
  - srv.catalog
  - srv.search
  - srv.pricing
affected_tiers:
  - T2
treatment: Mitigate
treatment_details: >
  Implement PostgreSQL streaming replication with automatic
  failover using Patroni. Add connection pooling via PgBouncer.
  Target: reduce probability to 1 (Rare), residual score: 4 (Low).
controls:
  - "CTRL-CAT-001: PostgreSQL HA with Patroni"
  - "CTRL-CAT-002: PgBouncer connection pooling"
owner: "Catalog Suite Architect"
review_date: "2026-07-15"
status: open
dora_articles: [Art. 8, Art. 9, Art. 11]
created: "2026-04-15"
last_updated: "2026-04-15"
```

### 5.4 Register Location

- **Suite-level registers** are maintained in each suite's specification
  repository under `risk/risk-register.yaml`.
- **Platform-level register** is maintained in the platform hub
  repository under `governance/risk/platform-risk-register.yaml`.
- Both registers use the same schema and are aggregated for reporting.

---

## 6. Risk Appetite per Service Tier

Risk appetite varies by service tier. Services closer to the platform
core (T1) have stricter risk tolerances than peripheral services (T4).
This reflects the blast radius — a T1 failure affects all tenants and
suites, while a T4 failure is typically scoped to analytics or reporting.

| Tier | Description | Max Residual Risk | Tolerance | Mandatory Controls | Review Frequency |
|---|---|---|---|---|---|
| **T1 — Platform** | Core infrastructure, identity, API gateway, shared runtime | Medium (score ≤ 9) | **Zero tolerance** for Critical risks. All High risks must be mitigated to Medium or below. | All High+ risks require documented mitigation controls. Annual penetration testing. Continuous monitoring. | Monthly |
| **T2 — Shared Business** | Shared business services used across multiple suites (e.g., notifications, document management) | Medium (score ≤ 9) | **Low tolerance**. Critical risks must be mitigated immediately. High risks must have treatment plans within 30 days. | Documented mitigation for all High+ risks. Quarterly vulnerability scanning. | Quarterly |
| **T3 — Core Business** | Suite-specific business services (e.g., catalog, pricing, inventory) | High (score ≤ 16) with documented acceptance | **Standard tolerance**. Critical risks must be escalated. High risks may be accepted with documented rationale and executive approval. | Documented mitigation for all Critical risks. Semi-annual vulnerability scanning. | Semi-annual |
| **T4 — Data/Analytics** | Data pipelines, analytics, reporting, ML/AI services | High (score ≤ 16) with documented acceptance | **Standard tolerance**. Same as T3, but acceptance thresholds apply to data integrity risks specifically. | Documented mitigation for all Critical risks. Annual vulnerability scanning. | Annual |

### 6.1 Escalation Rules

- A risk that exceeds the **Max Residual Risk** for its tier MUST be
  escalated to the governance board.
- A risk affecting multiple tiers is assessed at the **highest
  (strictest) tier** it touches.
- Cross-suite risks (affecting more than one suite) are elevated to the
  platform-level register (RISK-PLAT-*) regardless of their individual
  tier classification.

---

## 7. Specification Integration

Risk assessments are not standalone documents — they flow into concrete
specifications. This section defines how risk register entries connect
to template sections.

### 7.1 TPL-SVC §9.4 — Risk Register References

Every service specification must include a section referencing risk
register entries that affect the service. The format:

```markdown
### 9.4 Risk Register References

| Risk ID | Title | Risk Level | Treatment | Status |
|---|---|---|---|---|
| RISK-CAT-001 | Catalog DB single point of failure | High | Mitigate | Open |
| RISK-PLAT-003 | Cloud provider regional outage | High | Mitigate | Mitigated |
```

This section is **DORA-required** per GOV-DORA-001 §3.3.

### 7.2 TPL-SUITE §7.5 — Suite Risk Profile

Suite specifications aggregate risks from all services in the suite. The
suite risk profile provides:

- Total count of risks by level (Critical, High, Medium, Low)
- Top 5 risks by risk score
- Trend compared to previous assessment (improving, stable, degrading)
- Cross-suite risks affecting this suite

### 7.3 TPL-PROD §21 — Compliance Posture

Product specifications document the compliance posture from a product
perspective:

- Aggregated risk profile across all included suites
- Product-specific risks (e.g., regulatory risks in a specific market)
- Compliance gaps and remediation plans
- DORA Compliance Score for the product (aggregated from suite scores)

### 7.4 Cross-Referencing

Risk register entries MUST cross-reference affected service IDs using
the `affected_services` field. This enables:

- **Impact analysis:** When a risk is identified, all affected services
  are immediately known.
- **Completeness checking:** A service with no risk references in §9.4
  either has no risks (unlikely) or has incomplete documentation
  (likely).
- **Audit traceability:** Auditors can navigate from risk register →
  service spec → implementation.

---

## 8. Review and Reporting

### 8.1 Review Schedule

| Activity | Frequency | Responsible | Output |
|---|---|---|---|
| Risk register review | Monthly | CISO + Security Champions | Updated risk scores, treatment progress, new entries |
| Full risk assessment | Annually | Platform Architect + CISO | Complete re-assessment of all risks across all tiers |
| Risk report to governance board | Quarterly | Platform Architect | Summary report: risk counts by level, trends, overdue treatments |
| Specification compliance check | Quarterly | Platform Architect | Verification that spec risk references (§7) are current |

### 8.2 Trigger-Based Re-Assessment

The following events trigger an immediate risk re-assessment for the
affected scope:

| Trigger | Scope | Responsible |
|---|---|---|
| **New service deployment** | All risks affecting the new service and its dependencies | Service owner + Suite architect |
| **Major version change** | All risks affecting the changed service | Service owner |
| **Security incident** | All risks in the affected risk category across the affected tier | CISO + Security Champion |
| **Third-party change** | All Third-Party category risks involving the changed provider | Platform Architect |
| **Regulatory update** | All risks, if the update affects DORA Articles 5–16 | CISO + Platform Architect |

### 8.3 Reporting Format

Quarterly risk reports to the governance board include:

1. **Executive summary** — Overall risk posture (improving, stable,
   degrading) with key metrics.
2. **Risk heat map** — Visual matrix showing risk distribution by
   category and tier.
3. **Top 10 risks** — Highest-scoring risks with treatment status.
4. **Overdue treatments** — Risks past their treatment deadline.
5. **New and closed risks** — Changes since the previous report.
6. **DORA compliance status** — Alignment with Art. 6(5) reporting
   requirements.

---

## 9. Decisions

| ID | Decision | Rationale |
|---|---|---|
| D-001 | Risk register is scoped **per-suite** with a dedicated **platform-level** register for cross-cutting risks (RISK-PLAT-* entries). | Aligns with the OpenLeap 4-tier service model. Suite teams own their domain risks. Platform-level risks (infrastructure, shared services) are managed centrally. Aggregation for product-level reporting uses the suite registers as inputs. |
| D-002 | **CVSS alignment** for mapping vulnerability scores to the impact dimension of the risk matrix. | Ensures consistency between vulnerability management tooling (which produces CVSS scores) and the risk management framework. Avoids duplicate assessment effort. CVSS provides the impact baseline; probability is assessed independently. |
| D-003 | **Tier-differentiated risk appetite** with stricter thresholds for T1/T2 services. | Reflects the blast radius principle: platform-core failures affect all tenants and suites, while peripheral services have contained impact. Avoids over-engineering T4 services while ensuring T1 services meet the highest resilience standards. Consistent with DORA's proportionality principle (Art. 4). |

---

## 10. Open Questions

| ID | Question | Impact | Owner | Needed by |
|---|---|---|---|---|
| Q-001 | Should the risk register be stored as YAML, JSON, or in a dedicated GRC tool? | Affects tooling, CI integration, and audit workflow | Platform Architect | Phase 2 rollout |
| Q-002 | How are inherited risks from the hosting platform (e.g., cloud provider shared responsibility model) represented? | Affects completeness of Infrastructure risk category | CISO | Next quarterly review |
| Q-003 | Should risk register entries be version-controlled alongside specs or in a separate governance repository? | Affects change management and audit trail | Architecture Team | Governance repository design |

---

## Appendix: Quick Reference Card

```
RISK IDENTIFIED? → Follow this flow:

  1. CATEGORIZE
     → Infrastructure / Application / Data / Third-Party / Operational

  2. ASSESS
     → Probability (1-5) x Impact (1-5) = Risk Score (1-25)
     → For CVE-based risks: map CVSS to Impact per §3.4

  3. CLASSIFY
     → Low (1-4) / Medium (5-9) / High (10-16) / Critical (17-25)

  4. CHECK TIER APPETITE (§6)
     → T1: Max residual Medium. Zero tolerance Critical.
     → T2: Max residual Medium. Low tolerance.
     → T3: Max residual High (with acceptance).
     → T4: Max residual High (with acceptance).

  5. TREAT
     → Mitigate / Accept / Transfer / Avoid
     → Document in risk register with owner + deadline

  6. REGISTER
     → Suite risk: RISK-{SUITE}-{NNN}
     → Platform risk: RISK-PLAT-{NNN}
     → Update TPL-SVC §9.4 for affected services

  7. REVIEW
     → Monthly: risk register review
     → Quarterly: report to governance board
     → Annually: full reassessment
```
