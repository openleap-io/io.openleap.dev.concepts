<!-- Template Meta
     Template-ID:   TPL-IRS
     Version:       1.0.0
     Last Updated:  2026-04-15
     Changelog:
       1.0.0 (2026-04-15) — Initial versioned baseline.
-->

# Incident Response Specification — {Scope}

> **Conceptual Stack Layer:** Governance
> **Space:** Platform
> **Owner:** Security / Architecture Team
> **References:** GOV-DORA-003 (Incident Management Policy)
> **Belongs to:** Suite Spec (Cross-Cutting Concerns) or Platform-wide

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `incident-response-spec.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections}
> - **Author(s):** {Names}
> - **Status:** [DRAFT | REVIEW | APPROVED]
> - **Scope:** {suite-level | platform-wide}
> - **Incident Response ID:** {IRS-{SUITE}-{NNN} or IRS-PLAT-{NNN}}
> - **Review Cadence:** {Quarterly | Annual}

---

## 0. Scope and Applicability

<!-- Define what services and suites this incident response specification
     covers. Identify the service tier context (T1/T2/T3) and any
     scope exclusions. -->

### 0.1 Covered Services

<!-- List the services, suites, or platform components covered by
     this IRS. Reference service specs where applicable. -->

| Service ID | Service Name | Tier | Suite |
|---|---|---|---|
| {service-id} | {Service Name} | {T1/T2/T3} | {suite} |

### 0.2 Scope Boundaries

- **In Scope:** {list of covered systems, environments, data types}
- **Out of Scope:** {list of exclusions — e.g., physical security, non-ICT incidents}

### 0.3 Service Tier Context

<!-- How service tiers affect incident response:
     T1 (Critical): fastest response, full escalation
     T2 (Standard): standard response timelines
     T3 (Non-critical): best-effort response -->

---

## 1. Classification Matrix

<!-- Map incident characteristics to severity levels. An incident is
     classified at the HIGHEST severity triggered by any single column. -->

| Criterion | SEV-1 (Critical) | SEV-2 (High) | SEV-3 (Medium) | SEV-4 (Low) |
|---|---|---|---|---|
| **Service Impact** | Complete outage of T1 service or multiple T2 services | Degraded T1 service or complete outage of single T2 service | Degraded T2 service or outage of T3 service | Minor degradation of non-critical service |
| **Data Impact** | Confirmed data breach of sensitive/personal data | Potential data exposure; no confirmed exfiltration | Data integrity issue; no confidentiality breach | Minor data quality issue; no security impact |
| **User Impact** | All users unable to perform core business functions | Significant subset of users affected; workaround unavailable | Limited user impact; workaround available | Minimal user impact; cosmetic or non-blocking |
| **Financial Impact** | Direct financial loss > {threshold} or regulatory penalties | Material financial impact or SLA breach with penalties | Moderate cost impact; internal budget implications | Negligible financial impact |
| **Regulatory Impact** | Mandatory regulatory notification required | Potential regulatory interest; proactive notification advisable | Internal compliance finding; no external notification | No regulatory relevance |

---

## 2. Response Playbooks

### 2.1 SEV-1 — Critical Incident Playbook

**Detection:**
- Automated monitoring alert (infrastructure, APM, security)
- Customer-reported complete service outage
- Security tool detection of active breach

**Triage (within 15 minutes):**
- [ ] Confirm incident severity classification
- [ ] Identify affected services and service tiers
- [ ] Assign Incident Commander
- [ ] Open incident channel (dedicated Slack channel / bridge call)
- [ ] Begin incident timeline log

**Containment (within 1 hour):**
- [ ] Isolate affected systems if security incident
- [ ] Activate failover / disaster recovery if available
- [ ] Block attack vectors if applicable
- [ ] Preserve forensic evidence (logs, snapshots)

**Eradication:**
- [ ] Identify root cause
- [ ] Remove threat actor / malicious components
- [ ] Patch exploited vulnerability
- [ ] Verify eradication completeness

**Recovery:**
- [ ] Restore services from known-good state
- [ ] Verify data integrity
- [ ] Gradual traffic restoration with monitoring
- [ ] Confirm service health metrics return to baseline

**Communication Template:**
> **Incident:** {ID} — {Title}
> **Severity:** SEV-1
> **Status:** {Investigating | Identified | Monitoring | Resolved}
> **Impact:** {Description of user/business impact}
> **Current Action:** {What is being done now}
> **Next Update:** {Time of next scheduled update}

### 2.2 SEV-2 — High Severity Playbook

**Detection:**
- Automated monitoring alert (degraded service metrics)
- Internal or customer-reported significant degradation
- Security alert requiring investigation

**Triage (within 30 minutes):**
- [ ] Confirm incident severity classification
- [ ] Identify affected services and scope
- [ ] Assign Incident Lead
- [ ] Notify on-call team

**Containment (within 2 hours):**
- [ ] Isolate affected components
- [ ] Apply temporary mitigation or workaround
- [ ] Preserve relevant logs and diagnostic data

**Eradication:**
- [ ] Identify and address root cause
- [ ] Apply permanent fix or schedule fix deployment
- [ ] Verify fix effectiveness

**Recovery:**
- [ ] Restore full service functionality
- [ ] Verify all affected users can access services
- [ ] Monitor for recurrence over 24 hours

### 2.3 SEV-3 — Medium Severity Playbook

**Detection:**
- Automated monitoring alert (non-critical threshold breach)
- Internal report of limited service degradation

**Triage (within 2 hours):**
- [ ] Confirm severity classification
- [ ] Assign responsible engineer
- [ ] Document workaround if available

**Containment:**
- [ ] Apply workaround to reduce impact
- [ ] Schedule fix in next maintenance window or sprint

**Eradication & Recovery:**
- [ ] Deploy fix through standard release process
- [ ] Verify resolution and close incident

### 2.4 SEV-4 — Low Severity Playbook

**Detection:**
- Monitoring alert or user report of minor issue

**Triage (within 1 business day):**
- [ ] Confirm severity classification
- [ ] Log incident for tracking

**Resolution:**
- [ ] Address in next regular release cycle
- [ ] Close incident after deployment and verification

---

## 3. Escalation Matrix

<!-- Defines who is notified and what actions are triggered based on
     severity and elapsed time. -->

| Severity | T+0 (Detection) | T+15 min | T+1 hour | T+4 hours | T+24 hours |
|---|---|---|---|---|---|
| **SEV-1** | On-call engineer, Incident Commander, Security Team | Engineering Manager, CTO | VP Engineering, CEO, Legal | Regulatory notification assessment (see §5) | Post-mortem initiated |
| **SEV-2** | On-call engineer, Team Lead | Engineering Manager | VP Engineering | CTO (if unresolved) | Post-mortem initiated |
| **SEV-3** | On-call engineer | Team Lead (if needed) | — | Engineering Manager (if unresolved) | — |
| **SEV-4** | Assigned engineer | — | — | — | — |

### 3.1 Escalation Contacts

| Role | Primary Contact | Backup Contact | Contact Method |
|---|---|---|---|
| **Incident Commander** | {Name} | {Name} | {Phone / Slack} |
| **Security Team Lead** | {Name} | {Name} | {Phone / Slack} |
| **Engineering Manager** | {Name} | {Name} | {Phone / Slack} |
| **CTO** | {Name} | {Name} | {Phone} |
| **Legal / Compliance** | {Name} | {Name} | {Phone / Email} |

---

## 4. Communication Plan

### 4.1 Internal Stakeholders

| Audience | When | How | Content |
|---|---|---|---|
| Incident response team | Immediately on detection | Slack incident channel, bridge call | Full technical details |
| Engineering leadership | Per escalation matrix (§3) | Slack, phone | Impact summary, ETA, actions needed |
| Executive team | SEV-1/SEV-2 per escalation matrix | Email, phone | Business impact, customer exposure, regulatory implications |
| All engineering staff | SEV-1 resolution or major learnings | Email, all-hands | Summary and lessons learned |

### 4.2 External Parties

| Audience | When | How | Content |
|---|---|---|---|
| Affected customers | SEV-1/SEV-2 with customer impact | Status page, email | Impact description, workaround, ETA |
| Partners / integrators | When partner-facing services affected | Email, partner portal | Technical impact, API status, timeline |
| Regulatory authorities | Per DORA notification requirements (§5) | Formal notification channels | Structured incident report |

### 4.3 Public Communication

<!-- Applicable for SEV-1 incidents with broad customer impact. -->

- **Status page** updated within 30 minutes of SEV-1 detection
- **Social media** response prepared for customer-facing outages
- **All external communications** reviewed by Legal / Compliance before publication

---

## 5. DORA Regulatory Notification

### 5.1 Trigger Criteria

Regulatory notification is REQUIRED when an ICT-related incident meets
any of the following criteria (DORA Art. 19):
- Affects critical or important functions
- Results in unauthorized access to data
- Causes financial loss above regulatory threshold
- Impacts a significant number of users or counterparties

### 5.2 Notification Timeline

| Notification | Deadline | Content Requirements |
|---|---|---|
| **Initial notification** | Within **4 hours** of classification as major | Incident type, affected services, initial impact assessment, detection time |
| **Intermediate report** | Within **72 hours** | Updated impact, root cause analysis (preliminary), remediation status, threat indicators |
| **Final report** | Within **1 month** | Complete root cause analysis, total impact assessment, remediation actions completed, lessons learned, preventive measures |

### 5.3 Report Content Requirements

Each notification MUST include:
- Unique incident identifier
- Date and time of detection
- Date and time of classification
- Description of the incident
- Criteria triggering the notification
- Impact on financial services
- Impact on clients and financial counterparties
- Cross-border impact (if applicable)
- Actions taken and planned

### 5.4 Authority Contacts

| Authority | Contact Method | Applicable When |
|---|---|---|
| {National competent authority} | {Portal / Email / Form} | {All major ICT incidents} |
| {Data protection authority} | {Portal / Email} | {Personal data breaches (GDPR Art. 33)} |
| {Sectoral regulator} | {Portal / Email} | {Sector-specific notification requirements} |

---

## 6. Post-Mortem Template

<!-- Every SEV-1 and SEV-2 incident MUST have a post-mortem completed
     within 5 business days of resolution. SEV-3 post-mortems are
     recommended but optional. -->

### 6.1 Incident Summary

| Field | Value |
|---|---|
| **Incident ID** | {INC-YYYY-NNN} |
| **Title** | {Concise incident title} |
| **Severity** | {SEV-1 / SEV-2 / SEV-3 / SEV-4} |
| **Duration** | {Start time} — {End time} ({total duration}) |
| **Incident Commander** | {Name} |
| **Post-Mortem Author** | {Name} |
| **Post-Mortem Date** | {YYYY-MM-DD} |

### 6.2 Timeline

| Time (UTC) | Event |
|---|---|
| {HH:MM} | {Detection / alert triggered} |
| {HH:MM} | {Triage / classification} |
| {HH:MM} | {Key action taken} |
| {HH:MM} | {Resolution / service restored} |

### 6.3 Root Cause

<!-- Describe the root cause. Use the "5 Whys" or equivalent
     root cause analysis technique. -->

### 6.4 Impact Assessment

| Dimension | Impact |
|---|---|
| **Users affected** | {Number / percentage} |
| **Data impact** | {None / integrity / confidentiality / availability} |
| **Financial impact** | {Estimated cost} |
| **Reputation impact** | {None / limited / significant} |
| **Regulatory impact** | {None / notification filed / investigation} |

### 6.5 Corrective Actions

| # | Action | Owner | Deadline | Status |
|---|---|---|---|---|
| 1 | {Corrective action description} | {Owner} | {YYYY-MM-DD} | {PLANNED / IN PROGRESS / DONE} |
| 2 | {Corrective action description} | {Owner} | {YYYY-MM-DD} | {PLANNED / IN PROGRESS / DONE} |

### 6.6 Lessons Learned

<!-- What went well? What could be improved? What was surprising? -->

- **What went well:** {list}
- **What could be improved:** {list}
- **Surprises:** {list}

### 6.7 Follow-up Tasks

| Task | Owner | Deadline | Tracking Reference |
|---|---|---|---|
| {Follow-up task} | {Owner} | {YYYY-MM-DD} | {Ticket / ADR / Risk ID} |

---

## 7. Testing and Exercises

<!-- Incident response capabilities MUST be regularly tested to ensure
     readiness. DORA Art. 25 requires regular testing of ICT tools,
     systems, and processes. -->

### 7.1 Exercise Schedule

| Exercise Type | Frequency | Description |
|---|---|---|
| **Tabletop exercise** | Quarterly | Walk-through of incident scenarios with response teams |
| **Simulated incident drill** | Semi-annually | Live simulation of incident detection, triage, and response |
| **Full failover test** | Annually | End-to-end disaster recovery and business continuity test |
| **Communication drill** | Annually | Test of notification chains and regulatory reporting process |

### 7.2 Exercise Register

| Exercise Type | Frequency | Last Conducted | Next Scheduled | Findings |
|---|---|---|---|---|
| Tabletop exercise | Quarterly | {YYYY-MM-DD} | {YYYY-MM-DD} | {Summary of findings and actions} |
| Simulated incident drill | Semi-annually | {YYYY-MM-DD} | {YYYY-MM-DD} | {Summary of findings and actions} |
| Full failover test | Annually | {YYYY-MM-DD} | {YYYY-MM-DD} | {Summary of findings and actions} |
| Communication drill | Annually | {YYYY-MM-DD} | {YYYY-MM-DD} | {Summary of findings and actions} |

### 7.3 Playbook Review

All playbooks (§2) MUST be reviewed and updated:
- After every SEV-1 or SEV-2 incident (incorporate lessons learned)
- After every tabletop exercise or drill
- At least annually, even without triggering events

---

## 8. Change Log

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
