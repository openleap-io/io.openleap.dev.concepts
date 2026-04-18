# Incident Management Policy

> **Document Type:** Platform Governance
> **ID:** GOV-DORA-003
> **Version:** 1.0 — 2026-04-15
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** ICT incident management across all OpenLeap platform services
> **DORA Articles:** 17–23 (ICT-related Incident Management)
> **Parent:** GOV-DORA-001 (DORA Compliance Framework)

---

## 1. Purpose

DORA (Digital Operational Resilience Act) Articles 17–23 require financial
entities to establish and implement an ICT-related incident management
process to detect, manage, and notify ICT-related incidents. This policy
defines how ICT incidents across all OpenLeap platform services are
classified, responded to, reported to competent authorities, and learned
from through structured post-mortems.

Every service specification MUST reference this policy for its operational
incident handling. The incident management process defined here applies to
all service tiers (T1–T4) and all environments (production, staging,
pre-production) — though response time targets and escalation paths are
calibrated by severity and service tier.

This policy complements the resilience testing requirements in TPL-RES and
the SLO-driven alerting defined in TPL-SLO. Together, these three
instruments form the operational resilience triad: detect (TPL-SLO),
respond (this policy), and validate (TPL-RES).

---

## 2. Incident Classification

All ICT incidents are classified into one of four severity levels. The
classification determines response time targets, escalation paths,
notification obligations, and post-mortem requirements.

### 2.1 Severity Levels

| Severity | Label | Definition | Examples |
|---|---|---|---|
| **SEV-1** | Critical | Major ICT incident per DORA Art. 18. Core business services unavailable, data breach confirmed, or significant financial loss occurring or imminent. | Production database failure affecting customer-facing services; security breach exposing customer data; complete outage of a T1 service; ransomware or active intrusion detected. |
| **SEV-2** | High | Significant service degradation. Business functions impaired but not fully halted. Impact is contained but customer-visible or operationally disruptive. | Partial outage of a T1/T2 service; significant performance degradation (>10x baseline latency); failed automatic failover requiring manual intervention; data inconsistency in production affecting business logic. |
| **SEV-3** | Medium | Limited service impact. Non-critical functions affected. No direct customer impact or impact is minor and workaround exists. | Non-production environment outage blocking deployments; minor data inconsistencies in non-critical services; degraded monitoring or logging; T3/T4 service outage with no customer impact. |
| **SEV-4** | Low | Minimal or no impact. Cosmetic, informational, or hygiene issues. | Cosmetic UI issues in internal tools; minor logging failures; non-critical alert noise; documentation or configuration drift. |

### 2.2 DORA Notification Mapping

Not all incidents require regulatory notification. DORA Art. 19 mandates
notification only for **major ICT-related incidents** as defined in
Art. 18 and the associated regulatory technical standards (RTS).

| Severity | DORA Major Incident? | Regulatory Notification Required? | Notes |
|---|---|---|---|
| **SEV-1** | Yes | **Yes** — mandatory notification to competent authority | All SEV-1 incidents are presumed major until downgraded. |
| **SEV-2** | Conditional | **Conditional** — required if the incident meets the quantitative thresholds in the RTS (client impact, transaction volume, duration) | The incident commander assesses against RTS thresholds within the triage window. |
| **SEV-3** | No | No | Logged internally. Available for audit. |
| **SEV-4** | No | No | Logged internally. Available for audit. |

### 2.3 Classification Authority

The **on-call engineer** performs the initial classification. The
**incident commander** (team lead or designated senior engineer) confirms
or reclassifies within the triage window. Reclassification MUST be
documented with rationale in the incident ticket. Upgrading severity is
always permitted; downgrading from SEV-1 requires incident commander
approval and documented justification.

---

## 3. Response Time Requirements

Response time targets are measured from the moment the incident is
detected (alert fires or manual report received). All times are maximum
targets — teams SHOULD aim to beat them.

| Phase | SEV-1 | SEV-2 | SEV-3 | SEV-4 |
|---|---|---|---|---|
| **Detection** | < 5 minutes | < 15 minutes | < 1 hour | < 24 hours |
| **Triage** (severity confirmed, incident commander assigned) | < 15 minutes | < 30 minutes | < 4 hours | Next business day |
| **Containment** (impact stopped from spreading) | < 1 hour | < 4 hours | — | — |
| **Resolution** (service restored to normal operation) | < 4 hours | < 24 hours | < 72 hours | Next sprint |
| **Communication** (first stakeholder notification) | Immediate | Within 1 hour | Next business day | Included in periodic status report |

### 3.1 Detection Sources

Incidents may be detected through:

- **Automated alerting** — SLO-based alerts (TPL-SLO), infrastructure
  monitoring, security information and event management (SIEM) systems.
- **Manual reporting** — Customer support tickets, internal reports,
  third-party notifications.
- **Dependency notifications** — Upstream provider incident
  notifications, cloud provider status pages.

Regardless of detection source, the response time clock starts at the
earliest detection timestamp.

### 3.2 SLA Measurement

Response time compliance is measured monthly per service tier.
Non-compliance with SEV-1 or SEV-2 targets triggers a mandatory review
of the incident response process for the affected service.

---

## 4. Escalation Matrix

Escalation paths are determined by the combination of incident severity
and the tier of the affected service (as defined in the service
specification's §10.2 classification).

| Severity | Service Tier | Escalation Path | Escalation Deadline |
|---|---|---|---|
| **SEV-1** | Any tier | On-call engineer → Team lead → Head of Engineering → CISO → Management Board | Within 1 hour of classification |
| **SEV-2** | T1 / T2 | On-call engineer → Team lead → Head of Engineering | Within 4 hours of classification |
| **SEV-2** | T3 / T4 | On-call engineer → Team lead | Within 4 hours of classification |
| **SEV-3** | Any tier | On-call engineer → Team lead | Next business day |
| **SEV-4** | Any tier | Added to team backlog | No escalation required |

### 4.1 Escalation Rules

- Escalation timelines are measured from the moment of severity
  classification (not from detection).
- If the incident commander cannot be reached within the escalation
  deadline, the next level in the escalation path automatically assumes
  the incident commander role.
- SEV-1 incidents MUST have a dedicated incident commander who is not
  simultaneously performing technical remediation.
- All escalation actions MUST be logged in the incident ticket with
  timestamps.

### 4.2 External Escalation

For incidents involving third-party ICT service providers (per DORA
Art. 28–30), the escalation path extends to:

- **Third-party provider contact** — per the contractual incident
  notification clause.
- **Competent authority** — if the incident qualifies as major (see §5).

---

## 5. DORA Notification Requirements

Per DORA Art. 19, financial entities MUST report major ICT-related
incidents to the competent authority. This section defines the
notification timeline, content, and format.

### 5.1 Notification Timeline

| Notification Type | Deadline | Content | Recipient | Format |
|---|---|---|---|---|
| **Initial notification** | Within 4 hours of classifying the incident as major | Incident ID, classification rationale, affected services, estimated impact, containment status, initial root cause hypothesis | Competent authority (via designated reporting channel) | Standardized form per RTS on major incident reporting |
| **Intermediate report** | Within 72 hours of classification | Updated root cause analysis, revised impact assessment, remediation actions taken, timeline of events, status of containment | Competent authority | Standardized form per RTS |
| **Final report** | Within 1 month of resolution | Complete root cause analysis, total impact assessment (clients affected, transactions impacted, financial impact, duration), corrective actions implemented, lessons learned, preventive measures | Competent authority | Standardized form per RTS |

### 5.2 Internal Notification

In addition to regulatory notification, major incidents trigger internal
notifications:

| Stakeholder | Notification Method | Timeline |
|---|---|---|
| Management Board | Direct communication (email + call) | Within 1 hour of SEV-1 classification |
| CISO / DPO | Direct communication | Within 1 hour of SEV-1; within 4 hours of SEV-2 classified as major |
| Affected product teams | Incident channel (chat) | Immediately upon triage |
| Customer-facing teams | Incident summary brief | Within 2 hours of SEV-1; within 4 hours of SEV-2 |

### 5.3 Notification Ownership

The **incident commander** is responsible for ensuring all notifications
are sent within the required deadlines. The CISO is responsible for the
accuracy and completeness of regulatory notifications. Notification
actions and timestamps MUST be recorded in the incident ticket.

---

## 6. Post-Mortem Process

Post-mortem reviews are the primary mechanism for learning from incidents
and preventing recurrence. OpenLeap follows a **blameless post-mortem
culture** — the goal is to understand systemic causes, not to assign
individual blame.

### 6.1 When Post-Mortems Are Required

| Severity | Post-Mortem Required? | Notes |
|---|---|---|
| **SEV-1** | **Yes** — mandatory | No exceptions. |
| **SEV-2** | **Yes** — mandatory | No exceptions. |
| **SEV-3** | Optional | Recommended if the incident revealed a systemic weakness. |
| **SEV-4** | No | Not required. |

### 6.2 Timeline

- Post-mortem document MUST be drafted within **5 business days** of
  incident resolution.
- Post-mortem review meeting MUST be held within **10 business days** of
  incident resolution.
- Corrective actions MUST have assigned owners and target dates before
  the review meeting concludes.

### 6.3 Post-Mortem Structure

Every post-mortem document MUST include the following sections:

1. **Incident Summary** — One-paragraph description of what happened,
   when, and the business impact.
2. **Incident Timeline** — Chronological log of events from detection
   through resolution, including all escalation and communication actions
   with timestamps.
3. **Root Cause Analysis** — Technical analysis of why the incident
   occurred. Use the "5 Whys" or fault-tree method. Distinguish between
   proximate cause (what triggered it) and root cause (why the system
   allowed it to happen).
4. **Impact Assessment** — Quantified impact: duration of outage, number
   of affected clients, transactions impacted, financial impact (if
   measurable), SLO breaches.
5. **Corrective Actions** — Specific, actionable items to prevent
   recurrence. Each action MUST have an owner, a target completion date,
   and a priority. Actions are categorized as:
   - **Immediate** — deployed within 1 week.
   - **Short-term** — completed within 1 month.
   - **Long-term** — completed within 1 quarter.
6. **Follow-Up Tasks** — Tracking items: monitoring improvements, runbook
   updates, alerting changes, documentation updates.

### 6.4 Review Process

- **SEV-1 post-mortems** are reviewed by the responsible team AND the
  Architecture Team. The Architecture Team assesses whether the incident
  reveals platform-level weaknesses that affect other services.
- **SEV-2 post-mortems** are reviewed by the responsible team. The
  Architecture Team is invited but attendance is optional.
- Post-mortem documents are stored in the incident management system and
  linked to the incident ticket.

### 6.5 Corrective Action Tracking

All corrective actions from post-mortems are tracked to completion:

- Actions are entered as work items in the team's backlog with the label
  `post-mortem`.
- The incident commander is responsible for verifying that all actions
  are completed by their target dates.
- Overdue corrective actions are escalated to the team lead and reported
  in the monthly operational resilience review.

---

## 7. Specification Integration

This policy does not exist in isolation. It connects to the specification
framework at multiple points:

| Specification | Integration Point |
|---|---|
| **TPL-IRS** (Incident Response Spec) | Instantiates this policy per suite. Each suite's IRS defines suite-specific incident scenarios, runbooks, and on-call rosters. |
| **TPL-SLO** (Service Level Objectives) | SLO alerting configuration feeds incident detection. SLO breaches are the primary automated trigger for incident creation. |
| **TPL-SVC §10.2** (Service Specification) | Defines per-service availability and recovery time targets. These targets inform the response time requirements in §3 and the escalation matrix in §4. |
| **TPL-RES** (Resilience Testing Spec) | Defines resilience testing (chaos engineering, failover drills, disaster recovery exercises) that validates incident readiness. Test results feed back into incident response improvements. |

### 7.1 Specification Cross-Reference Rules

- Every service specification (TPL-SVC) MUST reference this policy in its
  operational section and declare its service tier for escalation
  purposes.
- Every suite-level incident response specification (TPL-IRS) MUST
  implement the classification, response time, and escalation
  requirements defined here, adapted to the suite's specific services.
- Post-mortem corrective actions that require specification changes MUST
  follow the change management process in GOV-TPL-001.

---

## 8. Evidence and Audit Trail

DORA Art. 17(3) requires financial entities to record all ICT-related
incidents and significant cyber threats. Every incident — regardless of
severity — produces a documented evidence trail.

### 8.1 Required Evidence Per Incident

| Evidence Artifact | SEV-1 | SEV-2 | SEV-3 | SEV-4 |
|---|---|---|---|---|
| Incident ticket (timestamped, with full history) | Required | Required | Required | Required |
| Severity classification rationale | Required | Required | Required | Optional |
| Timeline of actions (detection, triage, escalation, resolution) | Required | Required | Required | Optional |
| Communication log (who was notified, when, via what channel) | Required | Required | Optional | Optional |
| Post-mortem report | Required | Required | Optional | Not required |
| Corrective action tracker | Required | Required | Optional | Not required |
| Regulatory notification records (initial, intermediate, final) | Required (if major) | Required (if major) | Not applicable | Not applicable |
| Root cause analysis | Required | Required | Optional | Not required |

### 8.2 Retention

All incident evidence MUST be retained for a minimum of **5 years** from
the date of incident resolution, per DORA Art. 17(3). This includes:

- Incident tickets and all associated metadata.
- Post-mortem reports and corrective action records.
- Regulatory notification copies and acknowledgments.
- Communication logs and escalation records.

### 8.3 Audit Access

Incident records MUST be available for inspection by:

- Internal audit.
- The competent authority (regulatory supervisors).
- External auditors engaged by the entity or the competent authority.

Records MUST be stored in a tamper-evident system. Modifications to
closed incident records MUST be logged with the identity of the modifier,
the timestamp, and the reason for modification.

---

## 9. Decisions

| ID | Decision | Rationale |
|---|---|---|
| D-001 | Four severity levels (SEV-1 through SEV-4) aligned with DORA major incident criteria. | SEV-1 maps directly to DORA's "major ICT-related incident" definition. SEV-2 provides a conditional threshold for incidents that may qualify as major based on quantitative RTS criteria. SEV-3 and SEV-4 cover operational incidents below the regulatory notification threshold but still require internal tracking. |
| D-002 | Post-mortems mandatory for SEV-1 and SEV-2 only. | SEV-1 and SEV-2 represent incidents with significant business impact where learning and prevention have the highest value. Requiring post-mortems for SEV-3/SEV-4 would create process overhead disproportionate to the learning opportunity. SEV-3 post-mortems remain optional for cases where systemic issues are suspected. |
| D-003 | 5-year retention for all incident records. | Aligns with DORA Art. 17(3) retention requirements. A single retention period across all severities simplifies records management and ensures audit readiness without classification-dependent retention logic. |
