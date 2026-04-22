# Artifact Custody Catalog

> **Document Type:** Platform Governance — Operating Model Registry
> **ID:** GOV-OM-002
> **Version:** 1.0 — 2026-04-22
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** Canonical location and stewardship of every artifact class referenced in GOV-OM-001
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Related:** `artifact-catalog.md` (authoritative content-artifact definitions)

---

## 1. Purpose

This registry answers the question "where does artifact of class X live, and who may change it?" for every artifact class that the Operating Doctrine (GOV-OM-001) relies on.

Per GOV-OM-001 §5.5:
- Every artifact class MUST have an entry here.
- The Architect is responsible for keeping this file current.
- New artifact classes MAY NOT be produced operationally before their entry exists.
- Vendored copies in consuming repositories are permitted when the entry marks them `vendored-ok`, subject to CI hash-verify via the `spec-integrity-check` reusable workflow.

---

## 2. Entry Schema

Each entry is a row in §3 or §4 with these fields:

| Field | Meaning |
|---|---|
| **Class** | The artifact-class name as used in GOV-OM-001 and artifact-catalog.md |
| **Canonical Location** | Repository + folder pattern (or single file) |
| **Content Authority** | Document or folder defining what this artifact IS |
| **Versioning** | Scheme (per GOV-OM-001 §5.3) |
| **Vendored Copy** | `forbidden`, `allowed-with-hash-verify`, `vendored-only` (never referenced) |
| **Stand-in** (Team-Mode) | Named in L1 or left TBD |
| **Defined In** | L0 (this doctrine) or L1 Spec N |

---

## 3. Content Artifacts (Specs, Contracts, Models)

Content-artifact definitions are authoritative in `artifact-catalog.md`. The custody table below confirms location and versioning only.

| Class | Canonical Location | Content Authority | Versioning | Vendored Copy |
|---|---|---|---|---|
| **Product** (= Elara Project) | `io.openleap.dev.spec/products/<product>/product.md` (in-Elara) **OR** `io.openleap.prod.<product>/product-spec.md` (prod repo convention) | artifact-catalog.md §2.1 | SemVer | forbidden (single canonical per product) |
| **Suite-Spec** | `io.openleap.dev.spec/T3_Domains/<SUITE>/suite-spec.md` | templates/platform/suite-spec.md | SemVer | allowed-with-hash-verify |
| **Domain-Spec / Service-Spec** | `io.openleap.dev.spec/T*/<module>/domain-spec.md` | templates/platform/domain-service-spec.md | SemVer | allowed-with-hash-verify |
| **Feature-Spec (Platform-Feature)** | `io.openleap.dev.spec/T*/<module>/features/F-*.md` | templates/platform/feature-spec.md | SemVer | allowed-with-hash-verify |
| **Feature-Composition** | `io.openleap.dev.spec/T*/<module>/features/` | templates/platform/feature-composition-spec.md | SemVer | allowed-with-hash-verify |
| **UVL (Variability Model)** | `io.openleap.dev.spec/T*/<module>/uvl/` and `io.openleap.dev.spec/products/<product>/selection.uvl` | templates/platform/uvl/ | SemVer | forbidden (single canonical) |
| **API-Contract (OpenAPI)** | `io.openleap.dev.spec/T*/<module>/contracts/openapi/` | artifact-catalog.md §3.x | SemVer synchronized with Spec | allowed-with-hash-verify |
| **Event-Contract (AsyncAPI)** | `io.openleap.dev.spec/T*/<module>/contracts/asyncapi/` | artifact-catalog.md §3.x | SemVer synchronized with Spec | allowed-with-hash-verify |
| **Screen-Contract (AUI)** | `io.openleap.dev.spec/T*/<module>/features/F-*.aui.yaml` | templates/platform/yaml/ | SemVer synchronized with Feature | allowed-with-hash-verify |
| **BFF-Contract** | `io.openleap.dev.spec/T*/<module>/features/F-*.bff` | artifact-catalog.md §3.x | SemVer synchronized with Feature | allowed-with-hash-verify |
| **Workflow-Spec** | `io.openleap.dev.spec/T*/<module>/workflows/` | templates/platform/workflow-spec.md | SemVer | allowed-with-hash-verify |
| **ADR (Architecture Decision Record)** | In spec folder as `<module>/spec/ADR/ADR-nnn-*.md` **or** in `dev.guidelines/ADR/` for cross-cutting | dev.concepts/governance/template-governance.md | Append-only with `supersedes` | allowed-with-hash-verify |
| **Business-Object, Persona, BusinessProcess, BusinessCapability, …** | `io.openleap.dev.spec/products/<product>/` (per Product); platform variants in Telos | artifact-catalog.md §2.x | SemVer | forbidden |
| **Feature-Mapping, Feature-Selection, Feature-Resolution, BFFConfig** | `io.openleap.prod.<product>/` or in-Elara product folder | artifact-catalog.md §2.x | SemVer synchronized with Product | forbidden |

---

## 4. Operational Artifacts (introduced by GOV-OM-001)

These artifact classes exist because the Operating Doctrine requires them. Several entries mark **tool TBD** — the specific tool choice is deferred to the cited L1 spec.

| Class | Canonical Location | Content Authority | Versioning | Vendored Copy | Defined In |
|---|---|---|---|---|---|
| **Intent** | `io.openleap.dev.spec/T*/<module>/intents/INT-nnn-*.md` | templates/operating-model/intent-template.md | Immutable monotonic ID | forbidden | L0 |
| **Task-List** | In corresponding Spec or as `<spec>/tasks.md` | L1 Spec 1 | Append-only | forbidden | L1 Spec 1 |
| **Test-Plan** | `<spec-folder>/test-plan.md` or in Feature-Spec section | templates/platform/feature-spec.md §Tests | Append-only | forbidden | L1 Spec 4 |
| **Test-Result** | CI artifact; linked via job URL + retention | L1 Spec 4 | Immutable per test run | not-applicable | L1 Spec 4 |
| **Validate-Report** | `<service-repo>/validate-reports/<build-id>.json` or equivalent ledger | L1 Spec 4 | Immutable monotonic ID | not-applicable | L1 Spec 4 |
| **Code** (commit) | Service/UI/BFF repository (per `artifact-catalog.md` mapping) | Per-repo conventions (Java: Spring Boot, UI: Vue 3/Nuxt 4) | SemVer per CHANGELOG + required `speckref` trailer | not-applicable (code is produced, not vendored) | L0 |
| **Build-Artifact** | Nexus (`io.openleap:*`) | governance/devsecops-pipeline.md | SemVer (= Code) + Commit-SHA metadata | not-applicable | L0 + L1 Spec 5 |
| **Image** (Docker) | Container Registry (tag per docker-build.yml) | governance/devsecops-pipeline.md | SemVer + `sha256:…` | not-applicable | L0 + L1 Spec 5 |
| **Deployment-Record** | **Tool TBD** — L1 Spec 6. Initial placeholder: `io.openleap.k8s/deployments/ledger/` (candidate). | L1 Spec 6 | Immutable monotonic ID | forbidden | L1 Spec 6 |
| **Issue** | **Tool TBD** — L1 Spec 7. Initial placeholder: GitHub Issues per repository with tier-tag propagation. | L1 Spec 7 | Immutable monotonic ID | forbidden | L1 Spec 7 |
| **Incident** | Referenced by GOV-DORA-003 §4 (severity classification). **Ledger tool TBD** — L1 Spec 7. | governance/incident-management-policy.md + L1 Spec 7 | Immutable monotonic ID | forbidden | L1 Spec 7 |
| **CVE-Record** | **Tool TBD** — L1 Spec 8. Initial placeholder: `io.openleap.dev.hub/cve-ledger/` (candidate). | templates/operating-model/cve-record-template.md + L1 Spec 8 | Immutable monotonic ID | forbidden | L1 Spec 8 |
| **Metric-Snapshot** | DORA: via `dora-metrics.yml` export. SPLE-specific: **Tool TBD** — L1 Spec 2. | governance/dora-compliance-framework.md + L1 Spec 2 | Immutable per snapshot timestamp | not-applicable | L1 Spec 2 |
| **Improvement-Proposal** | `io.openleap.dev.concepts/operating-model/improvements/IMP-nnn-*.md` | templates/operating-model/improvement-proposal-template.md | Immutable monotonic ID | forbidden | L0 |
| **Amendment-ADR** (L0 amendments) | `io.openleap.dev.concepts/operating-model/amendments/ADR-OM-nnn-*.md` | GOV-OM-001 §13 | Append-only with `supersedes` | forbidden | L0 |
| **Agent-Role Entry** | `io.openleap.dev.concepts/operating-model/agent-roles.md` (accumulating table) | GOV-OM-001 §7.3 + originating L1 spec | Append-only | forbidden | L0 + per L1 |

---

## 5. Custody Conventions

### 5.1 Vendored Copy Discipline

When an entry marks `allowed-with-hash-verify`, a consuming repository MAY maintain a copy in `<repo>/spec/<artifact-path>` under these conditions:

1. The vendored folder contains a `spec-ref.yaml` with:
   ```yaml
   spec-id: <artifact-class>:<stable-id>
   spec-version: <semver>
   canonical: <URL to canonical source>
   hash: sha256:<hex>
   verified-at: <ISO-8601 timestamp>
   ```
2. The `spec-integrity-check` reusable workflow (in `io.openleap.ops.workflows`) MUST be part of the repository's CI.
3. Hash mismatch is a build failure. Diff resolution: update the vendored copy to the canonical version, OR open an Intent for the vendor to change the canonical.

### 5.2 Forbidden Vendoring

Artifacts marked `forbidden` are single-canonical by design. Examples:
- **Product** (one product = one source)
- **UVL** (variability resolution must not fork)
- **Deployment-Record / Issue / Incident / CVE-Record** (ledger artifacts must be monotonic across the org)

### 5.3 Stand-in Ownership (Team-Mode)

When a named Architect or Platform Owner is unavailable, the stand-in structure is recorded per role in the `standins/` subfolder (to be created in L1 Spec 1). Until then, Solo-Mode applies (GOV-OM-001 §6.7): unavailable approver means the gate waits.

### 5.4 Amendments

Changes to this registry follow GOV-OM-001 §13 (Meta-Gate). Architect drafts; Architect + Product Owner approve (Solo: two explicit self-signs; Team: two distinct persons).

---

## 6. Open Items

Items requiring L1 Spec decisions before their entry above becomes operative:

| Open Item | Blocked on | Placeholder Location |
|---|---|---|
| Issue ledger tool | L1 Spec 7 | GitHub Issues per repo |
| Incident ledger tool | L1 Spec 7 | GOV-DORA-003 flow |
| Deployment-Record ledger | L1 Spec 6 | `io.openleap.k8s/deployments/ledger/` (candidate) |
| CVE-Record ledger | L1 Spec 8 | `io.openleap.dev.hub/cve-ledger/` (candidate) |
| SPLE-specific metrics store | L1 Spec 2 | none |
| Task-List format / tool | L1 Spec 1 | `<spec>/tasks.md` |

Each open item becomes a gate-able input for Operational Intents when its L1 Spec is approved and the placeholder is replaced with a canonical entry.
