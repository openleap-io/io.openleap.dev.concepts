# L1 Spec 5 — Build & Release

> **Document Type:** Platform Governance — Operating Model Detail (L1)
> **ID:** GOV-OM-L1-05
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — awaiting content
> **Author:** OpenLeap Architecture Team
> **Scope:** Nexus publish rules, Docker image lifecycle, SemVer-bump automation, multi-repo coordination
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Owner (suggested):** Platform Owner

---

## 1. Purpose

Operationalize phase Package of GOV-OM-001 §4.1. Extend existing `governance/devsecops-pipeline.md` and `governance/change-deployment-governance.md` with operating-model RACI. Specify the exact rules governing Nexus publication, Docker image tagging, and coordinated multi-repo releases (for shared libraries and reusable workflows).

---

## 2. Scope

**In scope.**
- Nexus publish rules: coordinates (groupId:artifactId:version), staging, promotion to release
- Docker image tagging: multi-tag rule (x.y.z, x.y, x, latest) and when each is published
- SemVer-bump automation from PR-diff analysis
- Build-Artifact to Spec-Version forward-link enforcement
- Multi-repo coordinated release for shared libs (`parent`, `starter`, cross-domain dependencies)
- Rollback of a released artifact: semantics of "unpublish" in Nexus (generally forbidden) vs. "revoke" (new patch supersedes)

**Out of scope.** Deployment to running systems (→ L1-6); CVE-Fast-Path release (→ L1-8).

---

## 3. Dependencies on L0

- GOV-OM-001 §4.1 — Phase Package; no gate on Implement; gate on Validate
- GOV-OM-001 §5.3 — SemVer rules (Code, Build-Artifact, Image)
- GOV-OM-001 §5.4 — Forward-links on Build-Artifact and Image
- GOV-OM-001 §6.4 — Package-Gate approver (Platform Owner)
- GOV-OM-001 §8 — RACI for Package

---

## 4. Dependencies on other L1 Specs

- L1-2 (Version & Traceability) — SemVer rules operationalized
- L1-3 (Implementation-Lifecycle) — Validate-Report as Package-Gate input

---

## 5. Invariants derived from L0

- No Build-Artifact without passing Acceptance-Gate (§4.1)
- Every Image carries SemVer + sha256 in metadata (§5.3)
- Build-Artifact has forward-link to both Code-Version and Spec-Version (§5.4)

---

## 6. Open Questions

1. Nexus: is "unpublish" permitted under any circumstance, or always forbidden (per SemVer orthodoxy)?
2. Docker registry: which registry and retention policy?
3. Multi-repo release: Maven `release` plugin? Custom orchestrator? GitHub Actions matrix?
4. BOM maintenance for `parent`/`starter` — who bumps, when, via which process?
5. Are snapshot publishes gated differently from release publishes?
6. Is there a staged-release concept (release candidate)?

---

## 7. Artifacts this L1 Registers in custody.md

- No new classes — formalizes existing `Build-Artifact` and `Image` custody entries

---

## 8. Agent Roles this L1 Introduces

- `Build-Agent` — executes the build (may be largely CI-job)
- `Release-Agent` — coordinates multi-repo publish and tag
- `SemVer-Bump-Agent` (possibly shared with L1-2) — proposes version bump on PR

---

## 9. CI Workflows this L1 Commissions

- Possible new: `coordinated-release.yml` — orchestrates multi-repo release
- Possible extension of `maven-build.yml` to enforce Package-Gate Validate-Report linkage
- Possible extension of `docker-build.yml` to emit Build-Artifact custody record

---

## 10. Completion Criteria

Per L1-roadmap.md §5.
