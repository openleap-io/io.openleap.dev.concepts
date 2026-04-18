<!-- Template Meta
     Template-ID:   TPL-RISK
     Version:       1.0.0
     Last Updated:  2026-04-15
     Changelog:
       1.0.0 (2026-04-15) — Initial versioned baseline.
-->

# ICT Risk Register — {Scope}

> **Conceptual Stack Layer:** Governance
> **Space:** Platform
> **Owner:** Security / Architecture Team
> **References:** GOV-DORA-002 (ICT Risk Management Policy)
> **Belongs to:** Suite Spec (Cross-Cutting Concerns) or Platform-wide

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `risk-register.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections}
> - **Author(s):** {Names}
> - **Status:** [DRAFT | REVIEW | APPROVED]
> - **Scope:** {suite-level | platform-wide}
> - **Risk Register ID:** {RISK-{SUITE}-REG or RISK-PLAT-REG}
> - **Review Cadence:** {Monthly | Quarterly}

---

## 0. Risk Context

<!-- Describe the business context and regulatory drivers that inform
     this risk register. Define the scope boundaries — what systems,
     services, or suites are covered. -->

### 0.1 Business Context

<!-- What is the operational environment? What business objectives
     does this risk register protect? -->

### 0.2 Regulatory Drivers

<!-- DORA, GDPR, sector-specific regulations, internal policies.
     Reference applicable governance documents (GOV-DORA-001, GOV-DORA-002). -->

### 0.3 Scope Boundaries

<!-- Which services, suites, or platform components are covered by
     this risk register? What is explicitly out of scope? -->

- **In Scope:** {list of services, suites, or "platform-wide"}
- **Out of Scope:** {list of exclusions}

---

## 1. Risk Register

<!-- Central risk register table. Each risk receives a unique ID.
     Probability and Impact are scored 1–5. Risk Score = Probability × Impact.
     Status: OPEN | MITIGATING | ACCEPTED | CLOSED. -->

| ID | Title | Category | Description | Probability | Impact | Risk Score | Affected Services | Affected Tiers | Treatment | Controls | Owner | Review Date | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| {RISK-001} | {Risk title} | {Operational / Security / Compliance / Third-Party / Technical} | {Brief description of the risk scenario and its potential consequences} | {1–5} | {1–5} | {P×I} | {service-ids} | {T1/T2/T3} | {Mitigate / Accept / Transfer / Avoid} | {Control references} | {Owner name} | {YYYY-MM-DD} | {OPEN} |
| {RISK-002} | {Risk title} | {Category} | {Description} | {1–5} | {1–5} | {P×I} | {service-ids} | {T1/T2/T3} | {Treatment} | {Controls} | {Owner} | {YYYY-MM-DD} | {OPEN} |
| {RISK-003} | {Risk title} | {Category} | {Description} | {1–5} | {1–5} | {P×I} | {service-ids} | {T1/T2/T3} | {Treatment} | {Controls} | {Owner} | {YYYY-MM-DD} | {OPEN} |

### 1.1 Risk Categories

| Category | Description |
|---|---|
| **Operational** | Risks to business processes, service availability, or data integrity |
| **Security** | Risks from cyber threats, unauthorized access, or data breaches |
| **Compliance** | Risks from regulatory non-compliance or audit findings |
| **Third-Party** | Risks from ICT third-party providers (ref GOV-DORA-005) |
| **Technical** | Risks from technology debt, architecture decisions, or infrastructure |

### 1.2 Scoring Definitions

**Probability Scale:**

| Score | Label | Definition |
|---|---|---|
| 1 | Rare | Less than once per year |
| 2 | Unlikely | Once per year |
| 3 | Possible | Once per quarter |
| 4 | Likely | Once per month |
| 5 | Almost Certain | Once per week or more |

**Impact Scale:**

| Score | Label | Definition |
|---|---|---|
| 1 | Negligible | No measurable business impact |
| 2 | Minor | Limited impact, resolved within normal operations |
| 3 | Moderate | Noticeable impact requiring management attention |
| 4 | Major | Significant business disruption or financial loss |
| 5 | Severe | Critical business failure, regulatory action, or reputational damage |

---

## 2. Risk Treatment Plans

<!-- For each risk scored as High (>=12) or Critical (>=20),
     document a detailed treatment plan below. -->

### 2.1 Treatment Plan: {RISK-ID} — {Risk Title}

| Attribute | Detail |
|---|---|
| **Risk Score** | {P×I} |
| **Treatment Strategy** | {Mitigate / Transfer / Avoid} |

**Mitigation Actions:**

| # | Action | Responsible | Deadline | Status |
|---|---|---|---|---|
| 1 | {Describe mitigation action} | {Owner} | {YYYY-MM-DD} | {PLANNED / IN PROGRESS / DONE} |
| 2 | {Describe mitigation action} | {Owner} | {YYYY-MM-DD} | {PLANNED / IN PROGRESS / DONE} |

**Success Criteria:**
<!-- Define measurable criteria that indicate the risk has been
     reduced to an acceptable level. -->
- {Criterion 1}
- {Criterion 2}

**Verification Method:**
<!-- How will the effectiveness of the treatment be verified? -->
- {Verification approach}

---

## 3. Risk Heatmap

<!-- Probability × Impact matrix (5×5). Place risk IDs in the
     appropriate cell. Color coding: Green (1–4), Yellow (5–9),
     Orange (10–14), Red (15–25). -->

|  | **Impact 1** (Negligible) | **Impact 2** (Minor) | **Impact 3** (Moderate) | **Impact 4** (Major) | **Impact 5** (Severe) |
|---|---|---|---|---|---|
| **Probability 5** (Almost Certain) | 5 | 10 | 15 | 20 | 25 |
| **Probability 4** (Likely) | 4 | 8 | 12 | 16 | 20 |
| **Probability 3** (Possible) | 3 | 6 | 9 | 12 | 15 |
| **Probability 2** (Unlikely) | 2 | 4 | 6 | 8 | 10 |
| **Probability 1** (Rare) | 1 | 2 | 3 | 4 | 5 |

**Risk Placement:**
<!-- Map risk IDs to cells, e.g.:
     - RISK-001: Probability 3 × Impact 4 = 12 (Orange)
     - RISK-002: Probability 2 × Impact 3 = 6 (Yellow)
-->

| Risk Zone | Score Range | Risk IDs |
|---|---|---|
| **Critical** (Red) | 15–25 | {list risk IDs} |
| **High** (Orange) | 10–14 | {list risk IDs} |
| **Medium** (Yellow) | 5–9 | {list risk IDs} |
| **Low** (Green) | 1–4 | {list risk IDs} |

---

## 4. Residual Risk Assessment

<!-- After controls are applied, reassess each risk to determine the
     residual risk level. -->

| Risk ID | Original Score | Residual Probability | Residual Impact | Residual Score | Acceptance Rationale |
|---|---|---|---|---|---|
| {RISK-001} | {P×I} | {1–5} | {1–5} | {P×I} | {Why the residual risk is acceptable, or reference to further treatment} |
| {RISK-002} | {P×I} | {1–5} | {1–5} | {P×I} | {Rationale} |
| {RISK-003} | {P×I} | {1–5} | {1–5} | {P×I} | {Rationale} |

### 4.1 Risk Acceptance Criteria

Risks MAY be formally accepted when:
- Residual score is **Low** (1–4) after controls
- Residual score is **Medium** (5–9) with documented business justification
  and compensating controls
- The cost of further mitigation exceeds the expected loss

Risks with residual score **High** (>=10) MUST NOT be accepted without
explicit approval from the Risk Committee or equivalent authority.

---

## 5. Change Log

| Version | Date | Author | Changes |
|---|---|---|---|
| {0.1} | {YYYY-MM-DD} | {Author} | {Initial draft} |

---

## Review & Approval

| Role | Name | Date | Signature |
|---|---|---|---|
| **Author** | {Name} | {YYYY-MM-DD} | |
| **Reviewer** | {Name} | {YYYY-MM-DD} | |
| **Approver** | {Name} | {YYYY-MM-DD} | |

**Next Review Date:** {YYYY-MM-DD}
