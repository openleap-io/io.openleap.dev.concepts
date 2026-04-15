# DevSecOps Pipeline — Stages, Quality Gates & DORA Metrics

> **Document Type:** Platform Governance
> **ID:** GOV-DEVSECOPS-001
> **Version:** 1.0 — 2026-04-15
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** All service and UI repositories in the OpenLeap platform

---

## 1. Purpose

OpenLeap follows a DevSecOps approach: security and quality are integrated into every stage of the CI/CD pipeline, not bolted on at the end. This document defines the normative pipeline stages, quality gates, security scanning requirements, and DORA metrics targets that all repositories must implement.

The pipeline is implemented as reusable GitHub Actions workflows in `io.openleap.ops.workflows`.

---

## 2. Pipeline Stages

Every code change passes through five stages. Each stage has a defined purpose, tools, and quality gate.

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  BUILD   │───►│   TEST   │───►│   SCAN   │───►│ PACKAGE  │───►│  DEPLOY  │
│          │    │          │    │          │    │          │    │          │
│ Compile  │    │ Unit     │    │ SAST     │    │ JAR/npm  │    │ Nexus    │
│ Resolve  │    │ Integr.  │    │ SCA      │    │ Docker   │    │ Docker   │
│ deps     │    │ Coverage │    │ Secrets  │    │ SBOM     │    │ DORA     │
│          │    │          │    │ License  │    │          │    │ event    │
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
```

### 2.1 Build

| Aspect | Specification |
|--------|--------------|
| Purpose | Compile source code, resolve dependencies |
| Tools | Maven (Java), pnpm (Vue/Nuxt) |
| Dependency resolution | Nexus (`maven-releases`, `maven-snapshots`) |
| Failure behavior | Blocks all subsequent stages |

### 2.2 Test

| Aspect | Specification |
|--------|--------------|
| Purpose | Verify correctness and measure coverage |
| Unit tests | Required. JUnit 5 (Java), Vitest (Vue) |
| Integration tests | Required for services with external dependencies (DB, broker) |
| Coverage reporting | JaCoCo (Java), c8/istanbul (Vue). Reports posted to PR |
| Coverage threshold | Informational — no hard gate. Teams set per-repo targets |
| Test containers | TestContainers for PostgreSQL, RabbitMQ in integration tests |

### 2.3 Scan

Security scanning is the core of the DevSecOps approach. All scans run on every PR and every push to main.

| Scan Type | Tool | What It Checks | Blocking Policy |
|-----------|------|----------------|-----------------|
| **SAST (fast)** | Semgrep | Source code vulnerabilities, injection, auth issues | CRITICAL blocks merge, HIGH warns |
| **SAST (deep)** | SonarQube | Code quality, complexity, duplication, deep security | Reports to SonarQube server. Non-blocking in CI |
| **SCA** | Trivy (filesystem) | Known CVEs in dependencies (replaces OWASP dependency-check) | CRITICAL blocks merge, HIGH warns |
| **Secret scan** | Gitleaks | Hardcoded secrets, API keys, tokens, passwords | Always blocks merge |
| **License check** | Trivy (license) | Copyleft or restricted licenses in dependencies | Warning only |
| **SBOM** | Trivy (CycloneDX) | Software Bill of Materials for supply chain transparency | Always generated, uploaded as artifact |
| **Container scan** | Trivy (image) | Vulnerabilities in Docker base image and layers | CRITICAL blocks, HIGH warns |

**Decision D-001:** CRITICAL findings and secret leaks always block merge. HIGH findings produce warning annotations but do not block. This balances security with developer velocity.

**Decision D-002:** Semgrep provides fast SAST feedback in CI (seconds). SonarQube provides deeper analysis on a separate server. Both are used — Semgrep gates the PR, SonarQube provides the dashboard.

### 2.4 Package

| Aspect | Specification |
|--------|--------------|
| Purpose | Produce deployable artifacts |
| Maven artifacts | JAR published to Nexus (`maven-releases` or `maven-snapshots`) |
| npm packages | Published to Nexus (`npm-releases`) for libraries |
| Docker images | Built and pushed to `registry.storage.openleap.io` |
| SBOM | CycloneDX SBOM attached to build artifacts and GitHub Releases |
| Versioning | Semver from git tags. SNAPSHOTs on main. See naming strategy |

### 2.5 Deploy

| Aspect | Specification |
|--------|--------------|
| Purpose | Publish artifacts to registries, record deployment event |
| Maven deploy | `mvn deploy` to Nexus |
| Docker push | `docker push` to `registry.storage.openleap.io` |
| DORA tracking | GitHub Deployment created for every successful deploy |
| PR builds | Build + test + scan only. No deploy |
| Main builds | Deploy SNAPSHOT to Nexus + Docker `latest` |
| Tag builds | Deploy release to Nexus + Docker semver tags |

---

## 3. Quality Gates

### 3.1 PR Quality Gate (must pass before merge)

| Check | Required | Tool |
|-------|----------|------|
| Build succeeds | Yes | Maven / pnpm |
| All tests pass | Yes | JUnit / Vitest |
| No CRITICAL vulnerabilities | Yes | Semgrep + Trivy |
| No leaked secrets | Yes | Gitleaks |
| Coverage report posted | Yes | JaCoCo / c8 |
| SBOM generated | Yes | Trivy |

### 3.2 Main Branch Gate (must pass before deploy)

All PR gates plus:

| Check | Required | Tool |
|-------|----------|------|
| Artifact deployed to Nexus | Yes | Maven deploy |
| Docker image pushed | Yes (if service) | Docker push |
| Docker image scanned | Yes (if service) | Trivy image |
| DORA deployment event recorded | Yes | GitHub Deployments API |

### 3.3 Release Gate (tag builds)

All main branch gates plus:

| Check | Required | Tool |
|-------|----------|------|
| Version set from tag (no SNAPSHOT) | Yes | `versions:set` |
| Deployed to `maven-releases` (not snapshots) | Yes | Maven deploy |
| Docker tagged with semver | Yes | Docker push |
| SBOM attached to GitHub Release | Yes | Trivy + gh release |

---

## 4. DORA Metrics

The four DORA metrics measure software delivery performance. OpenLeap tracks them to drive continuous improvement.

### 4.1 Definitions

| Metric | Definition | How Measured |
|--------|-----------|--------------|
| **Deployment Frequency** | How often code is deployed to production per service | Count of GitHub Deployments with `production` environment per week |
| **Lead Time for Changes** | Time from first commit to production deployment | Diff between first commit timestamp on the branch and deployment timestamp |
| **Change Failure Rate** | Percentage of deployments that cause a failure | Ratio of deployments followed by a rollback commit, hotfix tag, or `incident` issue within 24h |
| **Mean Time to Recovery** | Time from failure detection to resolution | Time between `incident` issue creation and close |

### 4.2 Targets

Following the DORA "Elite" performance tier as aspirational targets:

| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| Deployment Frequency | On demand (multiple/day) | Weekly–monthly | Monthly–quarterly | Quarterly+ |
| Lead Time for Changes | < 1 hour | 1 day – 1 week | 1 week – 1 month | 1 month+ |
| Change Failure Rate | < 5% | 5–10% | 10–15% | 15%+ |
| MTTR | < 1 hour | < 1 day | < 1 week | 1 week+ |

**Initial OpenLeap target: High tier.** Measure first, then improve.

### 4.3 Implementation

DORA metrics are tracked via:
1. **GitHub Deployments API** — every successful deploy creates a deployment record
2. **GitHub Actions job summaries** — weekly DORA dashboard in workflow summary
3. **Incident tracking** — GitHub Issues with `incident` label for MTTR calculation

---

## 5. Environment Promotion

```
Developer workstation
    │ git push
    ▼
Pull Request ──► Build + Test + Scan (quality gate)
    │ merge
    ▼
main branch ──► Build + Test + Scan + Deploy SNAPSHOT + Docker :latest
    │ git tag v1.2.0
    ▼
Release ──► Build + Test + Scan + Deploy Release + Docker :1.2.0
    │ (future: automated)
    ▼
Staging ──► Smoke tests + Integration verification
    │ (future: manual approval gate)
    ▼
Production ──► DORA deployment event recorded
```

**Current scope:** PR → main → release (Nexus + Docker). Staging and production environments are future work dependent on Kubernetes deployment automation.

---

## 6. Artifact Provenance & Supply Chain

| Aspect | Current | Target |
|--------|---------|--------|
| SBOM generation | CycloneDX via Trivy | Every build |
| Dependency pinning | Maven BOM (parent POM), pnpm lockfile | Enforced |
| Base image pinning | `amazoncorretto:25-alpine` | Pin to digest |
| Action pinning | Mutable tags (`@v4`) | Pin to SHA (future) |
| Artifact signing | None | cosign (future, SLSA Level 2) |

---

## 7. Incident Response & Rollback

### 7.1 Rollback Procedure

1. Identify the failing version (from DORA deployment tracking)
2. Deploy the previous known-good version: `git tag v1.2.1-rollback` pointing to the previous release commit
3. The tag triggers a release build that deploys the known-good version
4. Create a GitHub Issue with `incident` label for MTTR tracking

### 7.2 Hotfix Path

1. Branch from the release tag: `git checkout -b hotfix/v1.2.1 v1.2.0`
2. Fix the issue, push the branch
3. PR into main (goes through full quality gate)
4. Tag as `v1.2.1`, triggering release deploy
5. Close the incident issue

---

## 8. Decisions

| ID | Decision | Rationale |
|----|----------|-----------|
| D-001 | CRITICAL blocks, HIGH warns | Balances security with developer velocity. Avoids alert fatigue from non-critical findings |
| D-002 | Semgrep (fast) + SonarQube (deep) | Semgrep runs in seconds for CI gating. SonarQube provides dashboards and trend analysis |
| D-003 | Trivy replaces OWASP dependency-check | OWASP plugin has upstream NVD API parsing bugs. Trivy covers CVE scanning and also does SBOM, license, container scanning |
| D-004 | GitHub Deployments API for DORA | Native GitHub integration, no external service needed. Can be extended with dashboards later |
| D-005 | SBOM on every build | Supply chain transparency. CycloneDX format for broad tooling compatibility |

---

## 9. References

- DORA State of DevOps Report: https://dora.dev
- CycloneDX SBOM: https://cyclonedx.org
- Semgrep: https://semgrep.dev
- Trivy: https://trivy.dev
- Gitleaks: https://gitleaks.io
- SLSA Framework: https://slsa.dev
- OpenLeap Workflow Specs: `artifacts/platform/workflow-spec.md`
- OpenLeap Suite Package Blueprint: `governance/suite_package_blueprint.md`
