<!-- Template Meta
     Template-ID:   TPL-PIPE
     Version:       1.0.0
     Last Updated:  2026-04-15
     Changelog:
       1.0.0 (2026-04-15) — Initial versioned baseline.
-->

# DevSecOps Pipeline Specification — {Scope}

> **Conceptual Stack Layer:** Governance
> **Space:** Platform
> **Owner:** DevOps / Security Team
> **References:** GOV-DORA-004 (Change Management & Deployment Governance)

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `devsecops-pipeline-spec.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections or "fully compliant"}
> - **Author(s):** Name(s)
> - **Status:** [DRAFT | REVIEW | APPROVED | DEPRECATED | RETIRED]
> - **Pipeline ID:** `PIPE-{SUITE}-{NNN}` or `PIPE-PLAT-{NNN}`
> - **Scope:** {platform | suite | service}

---

## §0. Pipeline Identity

<!-- Define the scope and applicability of this pipeline specification.
     Which repositories does it cover?  Which environments does it target? -->

### 0.1 Scope

<!-- {platform | suite | service} — a platform-level pipeline applies to all
     repositories; a suite-level pipeline applies to all services within a suite;
     a service-level pipeline applies to a single service. -->

### 0.2 Applicable Repositories

<!-- List the repositories governed by this pipeline specification. -->

| Repository | Description | Branch Strategy |
|------------|-------------|-----------------|
| `{repo}`   | {desc}      | {trunk-based / gitflow / ...} |

### 0.3 Target Environments

<!-- List the environments this pipeline deploys to. -->

| Environment | Purpose | Deployment Method | Approval Required |
|-------------|---------|-------------------|-------------------|
| `dev`       | Development / integration | Automatic | No |
| `staging`   | Pre-production validation  | Automatic | No |
| `production`| Live traffic               | Gated     | Yes |

---

## §1. Pipeline Stages

<!-- Ordered list of pipeline stages.  Each stage produces artifacts and may
     include a quality gate (automated or manual). -->

| Stage # | Name                    | Description | Trigger | Artifacts Produced | Gate Type |
|---------|-------------------------|-------------|---------|--------------------|-----------|
| 1       | Code Commit             | {description} | Push / PR | Commit SHA | automated |
| 2       | Build                   | {description} | Stage 1 pass | Build artifact, container image | automated |
| 3       | Unit Tests              | {description} | Stage 2 pass | Test report, coverage report | automated |
| 4       | Integration Tests       | {description} | Stage 3 pass | Test report | automated |
| 5       | SAST                    | {description} | Stage 2 pass | SAST findings report | automated |
| 6       | SCA                     | {description} | Stage 2 pass | Dependency vulnerability report | automated |
| 7       | Secrets Detection       | {description} | Stage 1 pass | Secrets scan report | automated |
| 8       | Container Scan          | {description} | Stage 2 pass | Container vulnerability report | automated |
| 9       | IaC Scan                | {description} | Stage 2 pass | IaC compliance report | automated |
| 10      | SBOM Generation         | {description} | Stage 2 pass | SBOM (CycloneDX / SPDX) | automated |
| 11      | Policy Checks           | {description} | Stages 3-10 pass | Policy evaluation report | automated |
| 12      | Staging Deployment      | {description} | Stage 11 pass | Deployment manifest | automated |
| 13      | Smoke Tests             | {description} | Stage 12 pass | Smoke test report | automated |
| 14      | Production Deployment   | {description} | Stage 13 pass + approval | Deployment manifest | manual |

---

## §2. Security Gates

<!-- Per-gate configuration.  Each security tool MUST have defined pass criteria
     and a fail action.  CVSS thresholds determine whether a finding blocks
     the pipeline. -->

| Gate | Tool | Configuration | Pass Criteria | Fail Action | CVSS Threshold |
|------|------|---------------|---------------|-------------|----------------|
| SAST | SonarQube / Semgrep | {config reference} | Zero Critical / High findings | Block pipeline | >= {threshold} |
| SCA  | Snyk / Dependabot | {config reference} | No known Critical CVEs in dependencies | Block pipeline | >= {threshold} |
| Secrets Detection | detect-secrets / gitleaks | {config reference} | Zero secrets detected | Block pipeline | N/A |
| Container Scan | Trivy | {config reference} | No Critical / High CVEs in base image | Block pipeline | >= {threshold} |
| IaC Scan | Checkov / tfsec | {config reference} | No High-severity misconfigurations | Block pipeline | N/A |

<!-- Add additional gates as required. -->

---

## §3. SBOM Generation

<!-- Reference: GOV-DORA-005 §5 — Software Bill of Materials requirements. -->

| Attribute | Value |
|-----------|-------|
| **Format** | {CycloneDX | SPDX} |
| **Generation Tool** | {tool name and version} |
| **Included Components** | {application dependencies, OS packages, container base image} |
| **Storage Location** | {artifact registry path / S3 bucket / ...} |
| **Retention Period** | {N years — minimum 5 years per DORA} |

<!-- The SBOM MUST be generated on every production build and stored alongside
     the deployment artifact for audit purposes. -->

---

## §4. Policy as Code

<!-- OPA / Conftest rules enforced as automated gates.  Every rule MUST have
     a defined exception process. -->

| Policy ID | Rule | Enforcement | Exception Process |
|-----------|------|-------------|-------------------|
| POL-001   | Minimum review count >= {N} before merge | Block merge | Documented exception via {process} |
| POL-002   | No CVEs above CVSS {threshold} | Block deployment | Risk-accepted exception via {process} |
| POL-003   | No secrets in code | Block commit / merge | N/A — no exceptions |
| POL-004   | No open ports (0.0.0.0/0) in IaC | Block deployment | Documented exception via {process} |
| POL-005   | Container image from approved registry only | Block deployment | Documented exception via {process} |

<!-- Add additional policies as required.  Policies are stored in {repository}
     and versioned alongside application code. -->

---

## §5. Approval Gates

<!-- Maps change type and service tier to required approvals.
     Reference: GOV-DORA-004 §3 — Change Management. -->

| Change Type | Service Tier T1 | Service Tier T2 | Service Tier T3 | Service Tier T4 |
|-------------|-----------------|-----------------|-----------------|-----------------|
| Standard (pre-approved) | {approvals} | {approvals} | {approvals} | {approvals} |
| Normal | {approvals} | {approvals} | {approvals} | {approvals} |
| Emergency | {approvals} | {approvals} | {approvals} | {approvals} |

<!-- Emergency changes MUST still produce all audit artifacts; approval may be
     obtained retrospectively within {N} business days. -->

---

## §6. Audit Artifacts

<!-- Every pipeline stage MUST produce auditable evidence.  Artifacts are
     retained per organizational and regulatory retention policies. -->

| Stage | Artifact | Format | Retention | Purpose |
|-------|----------|--------|-----------|---------|
| Code Commit | Commit metadata, PR review record | JSON / Markdown | {N years} | Change traceability |
| Build | Build log, container image digest | Text / SHA256 | {N years} | Reproducibility |
| Unit Tests | Test report, coverage report | JUnit XML / HTML | {N years} | Quality evidence |
| Integration Tests | Test report | JUnit XML / HTML | {N years} | Integration evidence |
| SAST | Findings report | SARIF / JSON | {N years} | Security evidence |
| SCA | Vulnerability report | JSON | {N years} | Dependency security evidence |
| Secrets Detection | Scan report | JSON | {N years} | Secrets compliance evidence |
| Container Scan | Vulnerability report | JSON | {N years} | Container security evidence |
| IaC Scan | Compliance report | JSON | {N years} | Infrastructure compliance evidence |
| SBOM Generation | SBOM | CycloneDX / SPDX | {N years} | Software composition evidence |
| Policy Checks | Policy evaluation report | JSON | {N years} | Governance compliance evidence |
| Staging Deployment | Deployment manifest, smoke test results | YAML / JSON | {N years} | Deployment evidence |
| Production Deployment | Deployment manifest, approval record | YAML / JSON | {N years} | Production change evidence |

---

## §7. Compliance Thresholds

<!-- Thresholds that trigger automated actions when breached. -->

| Metric | Threshold | Action on Breach |
|--------|-----------|------------------|
| Code coverage minimum | >= {N}% | Block merge |
| SAST findings (Critical) | 0 | Block deployment |
| SAST findings (High) | <= {N} | Block deployment |
| SCA vulnerabilities (Critical) | 0 | Block deployment |
| SCA vulnerabilities (High) | <= {N} | Warn / block per policy |
| Secrets detected | 0 | Block commit |
| Policy violations | 0 | Block deployment |

---

## §8. Change Log

<!-- Track material changes to this specification. -->

| Date | Version | Author | Change Description |
|------|---------|--------|--------------------|
| {YYYY-MM-DD} | 1.0.0 | {author} | Initial version |

---

## Review & Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Author | {name} | {date} | DRAFTED |
| Reviewer (DevOps) | {name} | {date} | {APPROVED / CHANGES REQUESTED} |
| Reviewer (Security) | {name} | {date} | {APPROVED / CHANGES REQUESTED} |
| Approver | {name} | {date} | {APPROVED / REJECTED} |
