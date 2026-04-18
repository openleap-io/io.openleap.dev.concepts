# Change Management & Deployment Governance

> **Document Type:** Platform Governance
> **ID:** GOV-DORA-004
> **Version:** 1.0 — 2026-04-15
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** Change management and deployment processes for all OpenLeap platform services and products
> **DORA Articles:** 9(4)(e), 9(4)(d) (ICT Change Management)
> **Parent:** GOV-DORA-001 (DORA Compliance Framework)

---

## 1. Purpose

DORA Art. 9(4)(e) requires financial entities to implement policies and
procedures for ICT change management, including changes to software,
hardware, firmware components, systems, or security settings, that ensure
all changes are recorded, tested, assessed, approved, implemented, and
verified in a controlled manner.

This policy defines how changes to OpenLeap platform services and
products are classified, approved, tested, deployed, and audited. It
extends the PR-based change process established in GOV-TPL-001 (Template
Governance — Versioning & Change Management) with DORA-specific
deployment gates, approval requirements, and audit evidence production.

Every deployment to any environment (staging, pre-production, production)
MUST comply with this policy. The policy applies to all change types —
from routine dependency patches to emergency production fixes.

---

## 2. Change Classification

All changes are classified into one of three types. The classification
determines the approval process, deployment gates, and audit
requirements.

### 2.1 Change Types

| Change Type | Risk Level | Definition | Examples |
|---|---|---|---|
| **Standard Change** | Low | Pre-approved, routine change with well-understood risk. Follows an established, repeatable process. | Dependency patch update (non-breaking); configuration value change within documented bounds; documentation update; log level adjustment. |
| **Normal Change** | Medium–High | Requires explicit review and approval. Introduces new functionality, modifies existing behavior, or changes system interfaces. | New feature implementation; API contract change; database schema migration; security configuration change; infrastructure topology change. |
| **Emergency Change** | Variable | Urgent fix required to resolve an active SEV-1 or SEV-2 incident (per GOV-DORA-003). Follows an expedited process with retrospective full review. | Hotfix for production outage; security patch for actively exploited vulnerability; data corruption repair. |

### 2.2 Change Type by Service Tier

Service tier (as defined in the service specification's §10.2) determines
additional constraints on change processing:

| Change Type | T1 (Critical) | T2 (Important) | T3 (Standard) | T4 (Internal) |
|---|---|---|---|---|
| **Standard** | Single reviewer + all automated gates | Single reviewer + all automated gates | Single reviewer + automated gates | Single reviewer + automated gates |
| **Normal** | 2+ reviewers + security review + architecture review | 2+ reviewers + security review | 2+ reviewers + security review for security-related changes | 2+ reviewers |
| **Emergency** | 1 reviewer (retrospective full review within 48 hours) | 1 reviewer (retrospective full review within 48 hours) | 1 reviewer (retrospective full review within 48 hours) | 1 reviewer (retrospective review within 48 hours) |

### 2.3 Classification Authority

The **PR author** proposes the change type by applying the appropriate
label (`change:standard`, `change:normal`, `change:emergency`) to the
pull request. The **first reviewer** confirms or reclassifies the change
type before approving. Misclassification (e.g., labeling a schema
migration as a standard change) is a review finding that MUST be
corrected before merge.

---

## 3. Approval Requirements

Approval requirements are determined by the combination of change type
and service tier. All approvals are recorded as PR review approvals in
the version control system.

| Change Type | Service Tier | Minimum Reviewers | Security Review Required | Architecture Review Required |
|---|---|---|---|---|
| **Standard** | Any | 1 | No | No |
| **Normal** | T1 | 2 | Yes — mandatory | Yes — mandatory |
| **Normal** | T2 | 2 | Yes — mandatory | No |
| **Normal** | T3 | 2 | Yes — for security-related changes only | No |
| **Normal** | T4 | 2 | No | No |
| **Emergency** | Any | 1 (expedited) | Retrospective within 48 hours | Retrospective within 48 hours (T1 only) |

### 3.1 Reviewer Qualifications

- **Standard changes:** Any team member with repository write access.
- **Normal changes:** At least one reviewer MUST be a senior engineer or
  team lead with domain knowledge of the affected service.
- **Security reviews:** Performed by a member of the security team or a
  designated security champion within the engineering team.
- **Architecture reviews:** Performed by a member of the Architecture
  Team. Required for T1 normal changes and for any change that modifies
  cross-service interfaces, shared libraries, or platform-level
  infrastructure.

### 3.2 Approval Records

Every approval MUST be traceable:

- Reviewer identity (authenticated via version control system).
- Timestamp of approval.
- Review scope (full review, security-focused review, architecture
  review).
- Any conditions attached to the approval (e.g., "approved pending CI
  green").

---

## 4. Specification-to-Implementation Traceability

DORA Art. 9(4)(d) requires traceability of ICT changes. In the OpenLeap
context, this means maintaining a clear chain from specification changes
through implementation changes to deployment records.

### 4.1 PR Reference Requirements

Every code change pull request MUST reference the specification section
it implements or modifies. The reference is included in the PR
description using the following format:

```
Implements: TPL-SVC §6.3 (API contract for principal creation)
Modifies: TPL-SVC §10.2 (updated availability target from 99.9% to 99.95%)
```

If the change does not correspond to a specification section (e.g., a
pure refactoring or infrastructure change), the PR MUST state:

```
Spec impact: None (internal refactoring, no behavioral change)
```

### 4.2 Specification Version Tracking

Per GOV-TPL-001 §6.3, the implementation repository tracks the
specification version it implements. When a specification change is
merged, the corresponding implementation repository MUST be updated to
reference the new specification version. The implementation status
registry (`impl-status.json`) tracks the synchronization state between
specifications and implementations.

### 4.3 Breaking Specification Changes

Breaking specification changes (MAJOR template version bumps per
GOV-TPL-001 §4.1) require coordinated implementation updates:

- The specification PR MUST include a migration guide.
- Implementation PRs MUST be created within the deadline specified in
  GOV-TPL-001 §7.2 (1 quarter for MAJOR changes).
- The implementation PR MUST reference the specification change PR.
- Deployment of the implementation change follows the normal change
  process defined in this policy.

### 4.4 Traceability Chain

The complete traceability chain for any change is:

```
Specification change PR → Implementation change PR → Build artifact → Deployment record
```

Each link in this chain is auditable: PR numbers, commit SHAs, artifact
versions, and deployment timestamps are recorded and retained per §8.

---

## 5. Deployment Gate Requirements

Every deployment passes through a series of automated gates. These gates
implement the technical controls required by DORA Art. 9(4)(e) for
change assessment and testing. The gates are specified in detail by
TPL-PIPE (Pipeline Specification) and enforced by the CI/CD platform.

### 5.1 Gate Definitions

| Gate | Description | Failure Action |
|---|---|---|
| **Build** | Compilation, dependency resolution, artifact packaging. | Block — deployment cannot proceed without a successful build. |
| **Unit / Integration Tests** | Automated test execution. Minimum coverage thresholds enforced per service specification. | Block — below-threshold coverage or test failures prevent deployment. |
| **SAST** (Static Application Security Testing) | Source code analysis for security vulnerabilities. | Block — critical or high findings block deployment. Medium findings generate warnings. |
| **SCA** (Software Composition Analysis) | Dependency vulnerability scanning against CVE databases. | Block — CVSS > 9.0 blocks unconditionally. CVSS > 7.0 blocks unless a documented risk acceptance exists. |
| **Secrets Detection** | Scan for hardcoded secrets, API keys, credentials, and tokens in the codebase. | Block — any detected secret blocks deployment unconditionally. |
| **Container Scan** (if applicable) | Container image vulnerability scanning for services deployed as containers. | Block — critical vulnerabilities block deployment. |
| **SBOM Generation** | Software Bill of Materials generation in CycloneDX or SPDX format. Stored alongside build artifacts. | Block — deployment cannot proceed without a generated SBOM. |
| **Policy Checks** | OPA/Conftest rules validation: minimum review count, no unauthorized open ports, resource limits defined, required labels present. | Block — policy violations block deployment. |
| **IaC Scan** (if applicable) | Infrastructure-as-Code security scanning (Terraform, Helm, Kubernetes manifests). | Block — critical misconfigurations block deployment. |

### 5.2 Gates by Change Type

Not all gates apply equally to all change types. Emergency changes use a
reduced gate set to enable rapid deployment while maintaining minimum
security controls.

| Gate | Standard Change | Normal Change | Emergency Change |
|---|---|---|---|
| Build | Required | Required | Required |
| Unit / Integration Tests | Required | Required | Required (existing tests only — new tests added retrospectively) |
| SAST | Required | Required | Required |
| SCA | Required | Required | Required |
| Secrets Detection | Required | Required | Required |
| Container Scan | If applicable | If applicable | Deferred (run retrospectively within 48 hours) |
| SBOM Generation | Required | Required | Required |
| Policy Checks | Required | Required | Deferred (run retrospectively within 48 hours) |
| IaC Scan | If applicable | If applicable | Deferred (run retrospectively within 48 hours) |

### 5.3 Gate Override Process

In exceptional circumstances, a gate may be overridden. Gate overrides
are permitted ONLY under the following conditions:

- The override is approved by the Head of Engineering or CISO.
- The override rationale is documented in the PR.
- A follow-up ticket is created to resolve the underlying issue within
  a defined deadline.
- The override is logged in the deployment audit record.

Gate overrides for secrets detection are NEVER permitted.

---

## 6. Rollback and Recovery

Every deployment to production MUST have a documented rollback procedure.
The ability to revert a change quickly is a core operational resilience
requirement under DORA.

### 6.1 Rollback Requirements

| Requirement | T1 (Critical) | T2 (Important) | T3 (Standard) | T4 (Internal) |
|---|---|---|---|---|
| Documented rollback procedure | Required | Required | Required | Recommended |
| Maximum rollback time target | < 15 minutes | < 30 minutes | < 1 hour | < 4 hours |
| Deployment strategy | Blue-green or canary | Blue-green or canary | Rolling update | Rolling update |
| Automated rollback trigger | Required (on health check failure) | Required (on health check failure) | Recommended | Optional |

### 6.2 Database Migration Rollback

Database schema migrations present unique rollback challenges. The
following rules apply:

- All schema migrations MUST be **reversible** (include a corresponding
  rollback migration) OR have a documented **forward-fix plan** that
  describes how to resolve issues without reverting the schema.
- Destructive migrations (column drops, table drops) MUST be deployed in
  two phases: (1) stop using the column/table, (2) remove it in a
  subsequent release after confirming no dependencies remain.
- Data migrations that transform existing data MUST be tested against a
  production-representative dataset in pre-production before deployment.

### 6.3 Rollback Verification

After any rollback:

- The service MUST pass its health check and smoke test suite.
- The rollback event MUST be recorded in the deployment log with
  timestamp, reason, and the version reverted to.
- If the rollback was triggered by an incident, the incident ticket
  (per GOV-DORA-003) MUST reference the rollback deployment record.

---

## 7. Emergency Change Process

Emergency changes follow an expedited process to enable rapid response to
active incidents. The process trades some upfront rigor for speed, with
mandatory retrospective review to close the gap.

### 7.1 Process Flow

```
1. Active incident (SEV-1 or SEV-2) triggers need for code change
       │
       ▼
2. Engineer creates PR with label `change:emergency`
   └── References the incident ticket
       │
       ▼
3. Single reviewer approves (expedited review)
   └── Reviewer confirms: fix addresses the incident, no obvious regressions
       │
       ▼
4. Expedited deployment gates run:
   └── Build + Unit Tests + SAST + SCA + Secrets Detection + SBOM
   └── Container Scan, Policy Checks, IaC Scan: deferred
       │
       ▼
5. Deploy to production
       │
       ▼
6. Post-deployment verification
   └── Health checks pass
   └── Incident impact confirmed resolved or mitigated
       │
       ▼
7. Within 48 hours: retrospective full review
   └── Second reviewer completes full code review
   └── Deferred gates (Container Scan, Policy Checks, IaC Scan) run
   └── Any findings from deferred gates create follow-up tickets
       │
       ▼
8. Incident post-mortem (per GOV-DORA-003 §6) references the
   emergency change PR and deployment record
```

### 7.2 Retrospective Review Requirements

The retrospective full review within 48 hours MUST include:

- A second reviewer completing a full code review (not just a rubber
  stamp — the same rigor as a normal change review).
- Execution of all deferred deployment gates.
- Documentation of any findings and creation of follow-up work items.
- Confirmation that the emergency change meets all normal change
  standards, or a remediation plan if it does not.

### 7.3 Emergency Change Abuse Prevention

Emergency change classification is reserved for active SEV-1 and SEV-2
incidents only. The following controls prevent misuse:

- Every emergency change MUST reference an active incident ticket.
- The monthly operational resilience review includes a count of emergency
  changes. A pattern of frequent emergency changes indicates systemic
  issues that require process improvement.
- Emergency changes that are later found to not correspond to a genuine
  SEV-1/SEV-2 incident are flagged as process violations and reviewed
  with the team.

---

## 8. Audit Evidence

Every deployment produces a set of evidence artifacts that satisfy DORA
Art. 9(4)(e) traceability requirements. These artifacts are stored
alongside build artifacts and are available for internal and external
audit.

### 8.1 Required Evidence Per Deployment

| Evidence Artifact | Standard | Normal | Emergency |
|---|---|---|---|
| Pull request with approval records | Required | Required | Required (retrospective full review within 48 hours) |
| Security scan reports (SAST, SCA) | Required | Required | Required |
| SBOM (CycloneDX or SPDX) | Required | Required | Required |
| Deployment timestamp and version | Required | Required | Required |
| Rollback plan reference | Required | Required | Required |
| Container scan report (if applicable) | Required | Required | Retrospective within 48 hours |
| Policy check results | Required | Required | Retrospective within 48 hours |
| Incident ticket reference | Not applicable | Not applicable | Required |
| Retrospective review record | Not applicable | Not applicable | Required |

### 8.2 Retention

All deployment evidence MUST be retained for a minimum of **5 years**
from the date of deployment. This includes:

- PR metadata: author, reviewers, approval timestamps, merge timestamp,
  commit SHA.
- Security scan reports: full output of SAST, SCA, secrets detection,
  container scan, and IaC scan.
- SBOM: the complete software bill of materials for the deployed
  artifact.
- Deployment records: environment, timestamp, version, deployer identity,
  deployment method, rollback plan.

### 8.3 Evidence Storage

Evidence is stored alongside build artifacts in the artifact repository.
The artifact repository MUST:

- Support immutable storage (artifacts cannot be modified after storage).
- Provide access control (only authorized personnel can retrieve
  evidence).
- Support retention policies (automated enforcement of the 5-year
  minimum).
- Be available for audit access by internal audit, competent
  authorities, and external auditors.

---

## 9. Decisions

| ID | Decision | Rationale |
|---|---|---|
| D-001 | Three change types: standard, normal, and emergency. | Covers the full spectrum from routine low-risk changes to urgent incident fixes. Standard changes avoid unnecessary process overhead for well-understood, pre-approved changes. Emergency changes enable rapid incident response without bypassing all controls. |
| D-002 | Emergency changes receive a retrospective full review within 48 hours. | Balances the need for rapid deployment during incidents with the DORA requirement for controlled change management. The 48-hour window ensures that expedited changes are reviewed while context is fresh, without blocking incident resolution. |
| D-003 | CVSS > 9.0 blocks deployment unconditionally. | Critical vulnerabilities (CVSS 9.0–10.0) represent actively exploitable, high-impact risks. No risk acceptance is appropriate for this severity. CVSS 7.0–8.9 (high) may be accepted with documented rationale and a remediation timeline, recognizing that some high-severity CVEs may have mitigating factors in the deployment context. |
| D-004 | SBOM generation is mandatory for all deployments. | DORA requires entities to maintain an inventory of ICT assets and their dependencies. The SBOM provides a machine-readable dependency inventory per deployment, enabling vulnerability tracking, license compliance, and supply chain risk assessment. CycloneDX and SPDX are both accepted as industry-standard formats. |
