# DORA Compliance Framework

> **Document Type:** Platform Governance
> **ID:** GOV-DORA-001
> **Version:** 1.0 — 2026-04-15
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** DORA (Digital Operational Resilience Act) compliance across all OpenLeap platform specifications

---

## 1. Purpose

The **Digital Operational Resilience Act (DORA)** — EU Regulation
2022/2554 — establishes a comprehensive framework for the digital
operational resilience of financial entities. It applies directly to
banks, insurance companies, investment firms, payment institutions, and
other regulated financial entities, as well as to **ICT third-party
service providers** that support them.

As a specification framework for software firms building platforms
deployed by and for regulated financial enterprises, OpenLeap must
provide specification structures that produce **audit-ready evidence**.
Every template, every governance document, and every concrete spec
instance must be traceable to the regulatory requirements they satisfy.

DORA is organized around **five pillars**:

1. **ICT Risk Management** (Articles 5–16) — Identifying, protecting
   against, detecting, responding to, and recovering from ICT-related
   risks.
2. **ICT Incident Reporting** (Articles 17–23) — Classifying, reporting,
   and learning from ICT-related incidents.
3. **Digital Operational Resilience Testing** (Articles 24–27) — Testing
   ICT systems through vulnerability assessments and threat-led
   penetration testing.
4. **ICT Third-Party Risk Management** (Articles 28–44) — Managing risks
   arising from ICT third-party service providers, including
   concentration risk.
5. **Information Sharing** (Article 45) — Voluntary sharing of cyber
   threat intelligence among financial entities.

This document is the top-level governance artifact that maps DORA
requirements to OpenLeap specifications and establishes the audit
evidence chain. It serves as the entry point for auditors, compliance
officers, and architects seeking to understand how OpenLeap addresses
each DORA obligation.

---

## 2. DORA Pillar Mapping

The following table maps each DORA pillar to the OpenLeap governance
documents, templates, and artifacts that address it. Subordinate
governance documents (GOV-DORA-002 through GOV-DORA-005) provide
detailed policies; templates embed the required sections directly into
concrete specifications.

| DORA Pillar | Articles | OpenLeap Governance | OpenLeap Templates & Artifacts |
|---|---|---|---|
| **ICT Risk Management** | Art. 5–16 | GOV-DORA-002 (ICT Risk Management Policy) | TPL-RISK (Risk Register), TPL-SVC §9 (Security & Compliance) |
| **ICT Incident Reporting** | Art. 17–23 | GOV-DORA-003 (Incident Response & Reporting Policy) | TPL-IRS (Incident Response Spec), TPL-SLO (SLO Definitions) |
| **Digital Operational Resilience Testing** | Art. 24–27 | — (covered by template sections) | TPL-RES (Resilience Testing Spec) |
| **ICT Third-Party Risk Management** | Art. 28–44 | GOV-DORA-005 (Third-Party Risk Management Policy) | TPL-TPR (Third-Party Risk Register), TPL-PIPE §3 (Supply Chain Controls) |
| **Information Sharing** | Art. 45 | GOV-DORA-003 §4 (Communication Plan) | — (operational process, not spec artifact) |

**Reading guide:** GOV-DORA-003 covers both incident reporting (pillar 2)
and the communication plan that addresses information sharing (pillar 5).
Resilience testing (pillar 3) is primarily addressed through the
TPL-RES template, which defines test plans, scenarios, and acceptance
criteria at the service and suite level.

---

## 3. Compliance Scoring Model

### 3.1 Relationship to Template Compliance (GOV-TPL-001 §6.1)

GOV-TPL-001 defines a **Template Compliance Score** that measures how
closely a concrete spec follows its referenced template version:

```
Template Compliance = (present required sections / total required sections) x 100
```

The DORA Compliance Score extends this concept. While template
compliance measures structural completeness against a template, DORA
compliance measures **regulatory completeness** against DORA
requirements.

### 3.2 DORA Compliance Score

Each specification that contains DORA-relevant sections receives a DORA
Compliance Score:

```
DORA Compliance Score = (present DORA-required sections / total DORA-required sections) x 100
```

A spec can be 100% template-compliant but less than 100% DORA-compliant
if the DORA-required sections exist but lack the depth needed for audit
evidence. Conversely, a spec may be partially template-compliant but
fully DORA-compliant if only the DORA-relevant sections are complete.

### 3.3 DORA-Required Sections per Template

The following sections are considered **DORA-required** — they must
contain substantive, audit-ready content for a spec to achieve full DORA
compliance:

| Template | DORA-Required Sections | Description |
|---|---|---|
| **TPL-SVC** | §9.4 — Risk Register References | Links to RISK entries affecting this service |
| **TPL-SVC** | §9.5 — Compliance Requirements | Regulatory and compliance obligations |
| **TPL-SVC** | §10.5 — Incident Response Hooks | How the service participates in incident detection and response |
| **TPL-SUITE** | §7.5 — Suite Risk Profile | Aggregated risk assessment for the suite |
| **TPL-SUITE** | §7.6 — Resilience Requirements | Resilience testing requirements and recovery targets |
| **TPL-PROD** | §21 — Compliance Posture | Product-level regulatory compliance documentation |

### 3.4 Reporting Format

The DORA Compliance Score is reported alongside the Template Compliance
Score in each spec's meta block:

```markdown
> - **Template Compliance:** ~85% — §11, §12 missing
> - **DORA Compliance:** 100%
```

Or, if DORA sections are incomplete:

```markdown
> - **Template Compliance:** ~90% — §12 missing
> - **DORA Compliance:** ~67% — §9.5 incomplete, §10.5 missing
```

---

## 4. Audit Evidence Catalog

This is the master mapping from DORA requirements to OpenLeap evidence
sources. Auditors should use this table as their starting point when
assessing DORA compliance across the platform.

| DORA Requirement | Article(s) | OpenLeap Evidence Source | Template / Artifact |
|---|---|---|---|
| ICT Risk Management Framework | Art. 6(1)–(3) | Documented risk management policy with identification, protection, detection, response, and recovery procedures | GOV-DORA-002, TPL-RISK |
| ICT Risk Identification | Art. 8 | Risk register entries per service and suite with categorized threats and probability/impact scoring | TPL-RISK, TPL-SVC §9.4 |
| ICT Risk Assessment | Art. 8(3)–(4) | Probability x Impact matrix with tier-differentiated risk appetite and CVSS alignment | GOV-DORA-002 §3, GOV-DORA-002 §6 |
| ICT Risk Treatment | Art. 9 | Documented treatment decisions (mitigate, accept, transfer, avoid) with owners and deadlines | GOV-DORA-002 §4, TPL-RISK |
| ICT Systems Inventory | Art. 8(1) | Service landscape documentation with dependency graphs and data flow mappings | TPL-SUITE §3, TPL-SVC §4 |
| Protection and Prevention | Art. 9(1)–(2) | Security controls documented per service, including authentication, authorization, and encryption | TPL-SVC §9, TPL-SVC §10 |
| Detection | Art. 10 | Monitoring, alerting, and anomaly detection mechanisms per service | TPL-SVC §10.3, TPL-SLO |
| Response and Recovery | Art. 11 | Incident response procedures, recovery time objectives, and business continuity plans | TPL-IRS, TPL-SVC §10.5 |
| Backup and Restoration | Art. 12 | Backup policies, restoration procedures, and tested recovery processes per service | TPL-SVC §10.4, TPL-RES |
| Learning and Evolving | Art. 13 | Post-incident reviews, lessons learned, and control improvement tracking | TPL-IRS §post-mortem, GOV-DORA-003 |
| Communication | Art. 14 | Stakeholder communication plans for incidents and crisis management | GOV-DORA-003 §4 |
| ICT Incident Classification | Art. 18 | Incident severity taxonomy aligned with DORA classification criteria | GOV-DORA-003 §2, TPL-IRS |
| ICT Incident Reporting | Art. 19 | Reporting workflows with initial, intermediate, and final notification stages | GOV-DORA-003 §3, TPL-IRS |
| Resilience Testing Programme | Art. 24–25 | Resilience test plans covering vulnerability assessments and scenario-based testing | TPL-RES |
| Threat-Led Penetration Testing | Art. 26 | TLPT scope, methodology, and results documentation for critical services | TPL-RES §TLPT |
| Third-Party Risk Assessment | Art. 28–29 | Third-party risk register with due diligence assessments and concentration risk analysis | GOV-DORA-005, TPL-TPR |
| Contractual Arrangements | Art. 30 | Key contractual provisions for ICT third-party service providers | GOV-DORA-005 §contractual, TPL-TPR |
| Audit Logging | Art. 12(4), Art. 16 | Audit trail specifications per service with retention policies and tamper-evidence | TPL-SVC §10.2, TPL-PIPE §audit |

**Usage note:** Each row in this table represents an audit checkpoint.
During a DORA compliance assessment, the auditor should verify that the
referenced evidence source exists, contains substantive content (not
placeholder text), and is current (reviewed within the applicable cadence
defined in §6).

---

## 5. Roles and Responsibilities

DORA compliance is a cross-cutting concern that spans organizational
roles. The following table maps DORA responsibilities to OpenLeap
platform roles.

| Role | DORA Responsibility | OpenLeap Responsibility |
|---|---|---|
| **CISO** | Owns the ICT risk management framework. Accountable for the overall risk posture and regulatory reporting. | Approves GOV-DORA-002. Reviews platform-level risk register (RISK-PLAT-*) entries. Signs off on annual DORA compliance assessment. |
| **DPO** | Ensures alignment between DORA requirements and data protection obligations (GDPR, national data laws). | Reviews data-related risk register entries. Validates that TPL-SVC §9.5 addresses data protection where applicable. |
| **Security Champion** | Implements security controls at the service level. First responder for security-related incidents. | Authors TPL-SVC §9 (Security & Compliance) and §10.5 (Incident Response Hooks). Maintains per-service risk references. |
| **Platform Architect** | Designs resilient platform architecture. Ensures templates embed DORA requirements structurally. | Maintains DORA governance documents (GOV-DORA-001 through GOV-DORA-005). Ensures template compliance and resilience design across tiers. |
| **Product Owner** | Accountable for product-level compliance posture and risk acceptance decisions. | Authors TPL-PROD §21 (Compliance Posture). Approves risk acceptance decisions for product-scoped risks. |

---

## 6. Review Cadence

DORA compliance is not a one-time exercise. The following cadence
ensures continuous compliance and audit readiness.

| Frequency | Activity | Owner | Output |
|---|---|---|---|
| **Annual** | Full DORA compliance assessment across all pillars. Gap analysis against current regulation and RTS/ITS. | Platform Architect + CISO | Compliance assessment report. Updated gap register. |
| **Quarterly** | Evidence collection sweep. Verify that all specs referenced in §4 contain current, substantive content. | Platform Architect | Evidence completeness matrix. DORA Compliance Scores per spec. |
| **Monthly** | Risk register review. Update probability/impact scores. Review treatment progress and deadlines. | CISO + Security Champions | Updated risk register. Escalation of overdue treatments. |
| **Per-Release** | Pipeline verification. Confirm that CI/CD pipelines enforce security controls, dependency scanning, and audit logging. | DevOps + Security Champion | Pipeline compliance checklist. TPL-PIPE §3 verification. |

### 6.1 Trigger-Based Reviews

In addition to the scheduled cadence, a DORA compliance review is
triggered by:

- Deployment of a new T1 or T2 service
- Major version change to any DORA governance document
- Security incident classified as severity 1 or 2
- Onboarding of a new ICT third-party service provider
- Regulatory update to DORA or its delegated acts (RTS/ITS)

---

## 7. Integration with Existing Governance

### 7.1 GOV-TPL-001 — Template Governance

This framework extends GOV-TPL-001 in two ways:

1. **Template versioning:** When DORA-required sections are added to a
   template, the template receives a MINOR version bump (new optional
   section) or MAJOR bump (new mandatory section) per GOV-TPL-001 §4.1.
   Decision D-002 in this document constrains this to MINOR bumps to
   avoid forced migration.

2. **Compliance scoring:** The DORA Compliance Score (§3) is a
   second dimension alongside the Template Compliance Score defined in
   GOV-TPL-001 §6.1. Both scores appear in the spec meta block.

### 7.2 GOV-BFF-001 — BFF Guideline

The BFF guideline defines field-level security patterns (authentication
token propagation, scope-based field filtering, audit header forwarding).
These patterns contribute directly to DORA evidence for:

- **Access management** (Art. 9) — field-level authorization as a
  protection control
- **Audit logging** (Art. 12(4), Art. 16) — audit header propagation
  through the BFF layer ensures end-to-end traceability

### 7.3 Subordinate DORA Governance Documents

This document (GOV-DORA-001) is the parent of a family of DORA-specific
governance documents:

| Document | ID | Scope |
|---|---|---|
| ICT Risk Management Policy | GOV-DORA-002 | Risk identification, assessment, treatment, monitoring (Art. 5–16) |
| Incident Response & Reporting Policy | GOV-DORA-003 | Incident classification, reporting workflows, communication plan (Art. 17–23, Art. 45) |
| Resilience Testing Policy | GOV-DORA-004 | Testing programme, TLPT requirements, scenario definitions (Art. 24–27) |
| Third-Party Risk Management Policy | GOV-DORA-005 | Vendor assessment, contractual requirements, concentration risk (Art. 28–44) |

Each subordinate document references this framework as its parent and
must be consistent with the pillar mapping in §2 and the evidence
catalog in §4.

---

## 8. Decisions

| ID | Decision | Rationale |
|---|---|---|
| D-001 | DORA artifacts are **Platform Space** — cross-cutting governance that applies to all suites and products, not scoped to any single product. | DORA obligations apply to the financial entity and its ICT providers holistically. Per-product scoping would create gaps and duplication. Platform-level governance ensures consistent coverage. |
| D-002 | Existing templates receive **MINOR version bumps only** when DORA sections are added. No forced migration. | DORA sections are added as new optional sections, not restructuring of existing content. Per GOV-TPL-001 §4.1, this is a MINOR change. Existing specs remain valid but are flagged as "incomplete" until DORA sections are populated. |
| D-003 | DORA processes (risk assessment, incident response, resilience testing) are formalized as **workflow specs** using TPL-WF. | Workflow specs provide the structured format needed to document process steps, decision points, and escalation paths. This makes DORA processes machine-readable and consistent with the rest of the specification framework. |
| D-004 | Risk register is scoped **per-suite** with a dedicated **platform-level** register for cross-cutting risks. | Aligns with the 4-tier service model. Suite-level registers (RISK-{SUITE}-{NNN}) capture domain-specific risks. Platform-level entries (RISK-PLAT-{NNN}) capture infrastructure, shared-service, and cross-cutting risks. Product-level compliance posture (TPL-PROD §21) aggregates from suite registers. |
| D-005 | All DORA governance documents are written in **English** as the framework language. | OpenLeap is an English-language specification framework. Regulatory references use official English translations of DORA. Translation to national languages is the responsibility of the deploying financial entity, not the specification framework. |

---

## 9. Open Questions

| ID | Question | Impact | Owner | Needed by |
|---|---|---|---|---|
| Q-001 | Should DORA Compliance Scores be enforced by CI (fail build on score below threshold)? | Affects enforcement model and developer workflow | Platform Architect | Phase 2 rollout |
| Q-002 | How do RTS/ITS delegated acts (expected updates) affect the evidence catalog? | May require additional evidence rows in §4 | CISO + Platform Architect | Next annual review |
| Q-003 | Should TPL-RES be a standalone template or integrated into TPL-SVC as additional sections? | Affects template registry and spec authoring workflow | Architecture Team | Template v1.2 planning |

---

## Appendix: Quick Reference Card

```
DORA COMPLIANCE CHECK → For any spec:

  1. Is the spec's template DORA-relevant?
     → Check §3.3 for DORA-required sections per template.

  2. Are the DORA-required sections present and substantive?
     → Calculate DORA Compliance Score per §3.2.

  3. Do risk register references exist?
     → Verify RISK-{SUITE}-{NNN} or RISK-PLAT-{NNN} entries are current.

  4. Is evidence current?
     → Check review dates against cadence in §6.

AUDITOR ENTRY POINT:
  → Start with §4 (Audit Evidence Catalog).
  → Each row points to the spec section or governance doc that provides evidence.
  → Verify substantive content, not just structural presence.

PILLAR QUICK MAP:
  Risk Management   → GOV-DORA-002 + TPL-RISK + TPL-SVC §9
  Incident Reporting → GOV-DORA-003 + TPL-IRS + TPL-SLO
  Resilience Testing → TPL-RES
  Third-Party Risk   → GOV-DORA-005 + TPL-TPR + TPL-PIPE §3
  Information Sharing → GOV-DORA-003 §4
```
