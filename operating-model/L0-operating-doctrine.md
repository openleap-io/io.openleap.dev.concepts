# L0 — Operating Doctrine

> **Document Type:** Platform Governance — Operating Model (Meta-Framework)
> **ID:** GOV-OM-001
> **Version:** 1.0 — 2026-04-22
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** Organization-wide operating model for all spec-driven work on the OpenLeap SPLE platform — across platform services (T1+T2), business domains (T3), data products (T4), and derived products (Product Space via Telos/Elara).
> **Parent:** conceptual-stack.md (authoritative SPLE model), artifact-catalog.md (authoritative artifact definitions)
> **Related:** GOV-TPL-001 (Template Governance), GOV-DORA-001 (DORA Compliance Framework), GOV-DORA-003 (Incident Management), governance/change-deployment-governance.md, governance/devsecops-pipeline.md

---

## 1. Purpose

OpenLeap operates a Software Product Line Engineering (SPLE) platform spanning roughly 56 repositories across platform tiers (T1+T2), business domains (T3), and data products (T4), from which concrete customer products are derived via variability management. The technical bausteine exist; what has been missing is a **binding operating model**: a single normative source that answers, unambiguously, **who does what, when, with which artifact, at which gate** — tool-neutrally, so that agent implementations (Paperclip teams, standalone agents, skills, workflow jobs) can evolve without requiring organizational reorganization.

This document defines that operating model. It is the **meta-framework** into which the existing topical governance policies plug:

- Spec authoring mechanics live in GOV-TPL-001 (template governance)
- Incident-response specifics live in GOV-DORA-003 (incident management)
- Change/deployment specifics live in governance/change-deployment-governance.md
- Pipeline specifics live in governance/devsecops-pipeline.md

This document adds the **unified RACI and gate vocabulary** that binds them together.

**Intended outcome.** After adoption, every action in the organizational workflow can be located in a single lifecycle phase (Section 4), mapped to a single decision class (Section 6), resolved against a single responsibility row (Section 8), and — if an artifact is produced — reconciled against a single custody record (Section 5). Tool substitutions (e.g., replacing a Paperclip team with a standalone agent) become possible without amending this document.

---

## 2. Scope and Non-Scope

### 2.1 In Scope

- **All tiers of the 4-tier service model** (T1 Platform, T2 Common, T3 Domains, T4 Data) as defined in `reconciliation.md`.
- **All three invocation modes**: Platform-Evolution, Domain-Evolution, Product-Derivation (see §4.2).
- **Both personal operation (solo) and team operation** — the same doctrine, switchable without restructuring.
- **All artifacts from Product Space and Platform Space** as enumerated in `artifact-catalog.md`, plus operational artifacts introduced here (Intent, Issue, CVE-Record, Deployment-Record, Incident, Metric-Snapshot, Improvement-Proposal).

### 2.2 Out of Scope

- **Concrete agent-role catalogs.** L0 names role functions abstractly; concrete agent roles are defined in L1 specs and accumulate in `operating-model/agent-roles.md`.
- **Tool bindings.** Issue-tracker choice, deployment-ledger location, agent-framework selection are L1 decisions.
- **Domain content.** Individual domain/service/feature specs live in `io.openleap.dev.spec`; this doctrine governs their lifecycle, not their content.

---

## 3. Design Principles

Five principles constrain every decision in this document. When the doctrine conflicts with operational convenience, the principles take precedence.

1. **Operating Doctrine, not Reference Menu.** Every rule is prescriptive. Where alternatives were considered during design, they are recorded in commit history — not in this document. One way, clearly stated.

2. **Dual-Mode Operability.** Every gate, every accountability, every RACI cell has two behaviors: *Solo* (one person wearing multiple role hats, with audit-record substitutes for segregation-of-duty) and *Team* (roles assigned to persons, with explicit segregation-of-duty). The doctrine transitions without rewriting.

3. **Tool-Neutrality.** The doctrine names role functions and artifact classes — never products, frameworks, or teams. "A Spec-Review-Agent" is a function; whether it is implemented as a Paperclip team member, a Claude Code subagent, an MCP server, or a GitHub Actions job is an L1 decision.

4. **Hard Solo-Mode.** Solo operation does not grant bypass rights to any gate. The same phases run, the same artifacts are produced, the same approvals are recorded — with self-approval documented through audit records that mechanically substitute for the second signature in Team-Mode. This enforces the discipline that the team mode will demand, before the team exists.

5. **Single Canonical Custody.** Every artifact class has exactly one canonical location, registered in `operating-model/custody.md`. Vendored copies in consuming repositories are permitted, but require CI-enforced hash-verify against the canonical source. Drift is a build failure, not a discussion.

---

## 4. Lifecycle Backbone

### 4.1 Phases

Nine phases. Gates are mandatory; sequence is the default case; skip paths are allowed only with explicit gate approval of the skip itself.

| # | Phase | Primary Output | Exit-Gate Approvers |
|---|---|---|---|
| 1 | **Conceive** | Intent-Document | Product Owner |
| 2 | **Specify** | Spec-Artifact (per template in `templates/`) + UVL | Architect (structure) + Quality Steward (template+ontology conformance) |
| 3 | **Design** | ADRs, Contracts, Task-List, Variability-Bindings | Architect + Quality Steward |
| 4 | **Implement** | Code (Service / UI / BFF / Workflow) | *No gate* — control lies in Validate |
| 5 | **Validate** | Test reports, Compliance report, Review record | Domain/Platform Owner (functional) + Quality Steward (compliance) |
| 6 | **Package** | Versioned artifact (Nexus coordinate, Docker image, tag) | Platform Owner |
| 7 | **Deploy** | Deployment-Record | Release Authority (+ Security Authority for prod, + Quality Steward veto for prod) |
| 8 | **Operate** | Incident-Records | Domain/Platform Owner |
| 9 | **Learn** | Metric-Snapshots, Improvement-Proposals | Architect (+ PO if Meta-Governance impact) |

Phase 4 (Implement) deliberately has no exit-gate. Code is not a decision artifact; it is input to Validate. This prevents the common "gatekeeper blocks commits" pathology and concentrates enforcement where it belongs — in the artifact that demonstrates quality, not in the act of writing it.

### 4.2 Invocation Modes (SPLE Core)

The same nine phases run in three contexts, with rotating lead human:

| Context | Applies to | Lead Human |
|---|---|---|
| **Platform-Evolution** | T1 Platform + T2 Common assets | Platform Owner |
| **Domain-Evolution** | A single T3 Domain (e.g., CRM, FI, SD, PPS) | Domain Owner |
| **Product-Derivation** | Composition of T3 domains into a concrete customer product via UVL-Binding | Product Owner |

Product-Derivation primarily exercises phases 2 (Specify: UVL+Bindings), 3 (Design: binding→contract), and 6 (Package: product-bundle) — without new service code. The feature activation for a product MUST update the UVL binding; there is no "configuration-only" activation path outside UVL.

### 4.3 Feedback Loop

Phase 9 (Learn) → Phase 1 (Conceive). Issues emerging from Operate, metric anomalies from Learn, and CVE alerts from security scanning generate new Conceive items. The one definitized fast-path bypass is the CVE-Fast-Path (§6.5).

---

## 5. Artifact Chain and Custody

### 5.1 Canonical DAG

```
                                   ┌─────────────┐
                                   │   Intent    │                         (Conceive)
                                   └──────┬──────┘
                                          ▼
                                   ┌─────────────┐
                                   │    Spec     │                         (Specify)
                                   └──────┬──────┘
                ┌─────────────┬───────────┼────────────┬──────────────┐
                ▼             ▼           ▼            ▼              ▼
           ┌─────────┐  ┌──────────┐ ┌─────────┐  ┌──────────┐  ┌───────────┐
           │   UVL   │  │   ADRs   │ │Contracts│  │ Task-List│  │ Test-Plan │
           └─────────┘  └──────────┘ └────┬────┘  └─────┬────┘  └─────┬─────┘
                                          │             │             │     (Design)
                                          │             ▼             │
                                          │       ┌──────────┐        │
                                          │       │  Code +  │◄───────┘
                                          │       │  Tests   │              (Implement)
                                          │       └────┬─────┘
                                          │            ▼
                                          │      ┌──────────┐
                                          │      │ Validate │              (Validate)
                                          │      │  Report  │
                                          │      └────┬─────┘
                                          │           ▼
                                          │      ┌──────────┐
                                          │      │  Build-  │              (Package)
                                          │      │ Artifact │
                                          │      └────┬─────┘
                                          │           ▼
                                          │      ┌──────────┐
                                          └─────►│  Image   │
                                                 └────┬─────┘
                                                      ▼
                                                 ┌──────────┐
                                                 │Deployment│              (Deploy)
                                                 │  Record  │
                                                 └────┬─────┘
                                                      ▼
                                                 ┌──────────┐
                                                 │Incidents │              (Operate)
                                                 │ Metrics  │
                                                 └────┬─────┘
                                                      ▼
                                                 ┌──────────┐
                                                 │Improvement│             (Learn)
                                                 │  Proposal │────► neuer Intent
                                                 └──────────┘
```

### 5.2 Identity Requirement

Every artifact instance, to pass any phase boundary, MUST carry:
(a) a stable identifier unique within its class,
(b) a version per the class's versioning rule (§5.3),
(c) exactly one canonical custody location registered in `custody.md`,
(d) at least one forward-link to its source artifact (§5.4).

Absence of any of the four is auto-rejection (§6.6) at the next gate.

### 5.3 Versioning Rules

| Class | Scheme | Break Rule |
|---|---|---|
| Spec | SemVer | MAJOR = Contract break or removed capability; MINOR = additive; PATCH = clarification |
| UVL | SemVer | MAJOR = feature removed or incompatibly restructured; MINOR = feature added; PATCH = description change |
| ADR | Append-only (no SemVer) | New ADR *supersedes* old (link `supersedes: ADR-nnn`); old remains with `status: superseded` |
| Contract | SemVer synchronized with Spec | MAJOR = schema break; MINOR = optional field; PATCH = documentation |
| Code | SemVer | CHANGELOG MUST include `speckref: <spec-id>@<version>` trailer |
| Build-Artifact | SemVer (= Code version) | Commit SHA embedded as metadata |
| Image | SemVer + `sha256:…` | Multi-tag: `x.y.z`, `x.y`, `x`, `latest` (per ops.workflows docker-build.yml) |
| Deployment-Record | Immutable monotonic ID | Never updated — new record per deploy |
| Incident / Metric / Improvement / CVE-Record / Intent / Issue | Immutable monotonic ID | Append-only ledger |

### 5.4 Mandatory Forward-Links

Every artifact MUST point forward to its source(s). This is how traceability is mechanized — there is no separate trace database; forward-links ARE the trace.

| Artifact | Forward-Link To |
|---|---|
| Spec | Intent-ID |
| UVL / ADR / Contract / Task-List / Test-Plan | Spec-ID |
| Code (each commit) | Task-ID (via commit trailer `Task: <id>`) |
| Test-Result | Task-ID + Code-SHA |
| Validate-Report | Code-Version + Compliance-Evidence-IDs |
| Build-Artifact | Code-Version + Spec-Version |
| Image | Build-Artifact coordinates |
| Deployment-Record | Image + Environment + Approver(s) + UVL-Binding (active-features list) |
| Incident | Deployment-Record(s) spanning the incident + Affected-Versions |
| Improvement-Proposal | Incident-IDs / Metric-Source-IDs |
| CVE-Record | Affected-Versions + Patch-Build-Artifact(s) + Approval-Records |

Rollup rule: the question "which spec is running in prod on environment E right now?" is answered by backward-traversal — Deployment-Record(E, latest) → Image → Build-Artifact → Code-Version → CHANGELOG → `speckref` → Spec-Version. No separate tracing system is required or permitted.

### 5.5 Custody Rule

Every artifact class has exactly one **canonical location**, registered in `operating-model/custody.md`, curated by the Architect.

**Vendored copies** in consuming repositories are permitted (for air-gap and offline-build scenarios). They MUST be verified via CI hash-check against the canonical source on every build. The verifier is the `spec-integrity-check` reusable workflow in `io.openleap.ops.workflows` (see §9.2). A hash mismatch is a build failure.

**New artifact classes** MUST NOT be produced operationally until their custody record exists. This is enforced at the Architectural gate for artifact-taxonomy changes (Meta-Governance, §6.1).

---

## 6. Gate Typology

### 6.1 Decision Classes

Six classes. Every gate is classified into exactly one (primary) and optionally one secondary class. The class determines the human roles that may approve.

| Class | Examples | Approver Roles |
|---|---|---|
| **Strategic** | Product direction, breaking-change roadmap, Prod release | Product Owner (± Architect) |
| **Architectural** | New ADR, Ontology change, Custody change, new artifact class | Architect |
| **Tactical** | Spec approval, Contract MINOR change, Feature acceptance | Domain Owner + Quality Steward |
| **Operational** | Build outcome, Non-Prod image promotion, dependency bump | Platform Owner |
| **Security/Compliance** | CVE response, Prod deploy, data classification | Security Authority |
| **Meta-Governance** | Amendments to this doctrine, ADR-catalog changes, Ontology-rule changes, gate-rule changes | Architect + Product Owner jointly |

### 6.2 Gate Attributes

Every gate is fully described by five attributes:

1. **Decision class** (§6.1)
2. **Approver role(s)** — who may approve
3. **Dual-mode behavior** — Solo and Team rules (§6.3)
4. **Auto-reject conditions** — machine-checkable violations (§6.6)
5. **Escalation path** — what happens when the approver is unavailable or deadlocked

### 6.3 Dual-Mode Rules

| Mode | Self-Approval | Segregation-of-Duty | Substitute |
|---|---|---|---|
| **Solo** | Permitted | Not available | Audit-record with full forward-links + compliance-evidence attached |
| **Team** | Forbidden | Required for specific gate pairs | — |

**Solo-mode is hard**: no gate may be skipped, collapsed, or bypassed. The discipline simulates the team-mode pattern the doctrine transitions into without further rework.

**Team-mode SoD pairs** (Autor ≠ Approver in these specific positions):
- Spec-Gate: Spec-Author ≠ Approving Architect ≠ Quality Steward
- Design-Gate: Architect ≠ Quality Steward (two persons)
- Acceptance-Gate: Implementor ≠ Quality Steward ≠ functional Acceptor
- Package-Gate: Build-triggering actor (human or agent is fine) + separate Platform-Owner approval on the resulting artifact
- Prod-Deploy-Gate: Release Authority ≠ Security Authority ≠ Quality Steward; three distinct persons required for Prod
- Meta-Change-Gate: Proposer ≠ approving Architect AND Product Owner

### 6.4 Phase-Exit Gate Matrix

| Phase-Exit / Gate | Primary Class | Approver(s) | Solo Audit | Team SoD |
|---|---|---|---|---|
| **Intent-Gate** (Conceive → Specify) | Strategic | Product Owner | PO self-sign + audit record | T1/T2 intents require Architect co-sign |
| **Spec-Gate** (Specify → Design) | Architectural + Tactical | Architect + Quality Steward | self for both hats with compliance-report | Author ≠ Approvers |
| **Design-Gate** (Design → Implement) | Architectural | Architect + Quality Steward | self | Architect ≠ Quality Steward |
| **Acceptance-Gate** (Validate → Package) | Tactical + Operational | Domain/Platform Owner + Quality Steward | self | Implementor ≠ QS ≠ Acceptor |
| **Package-Gate** (Package → Deploy) | Operational | Platform Owner | self | Build actor + separate PO approval |
| **Non-Prod-Deploy-Gate** | Operational | Release Authority | self | RA ≠ Package-Approver |
| **Prod-Deploy-Gate** | Strategic + Security | Release Authority + Security Authority **AND** Quality Steward has veto | self for all three hats with audit | three distinct persons |
| **Operate-Gate** (Incident-Close) | Operational | Domain/Platform Owner | self | Operator ≠ Approver for Sev1/2 |
| **Learn-Gate** (Improvement → new Intent) | Meta-Governance | Architect (+ PO when Meta) | self | Proposer ≠ Architect |
| **Meta-Gate** (Doctrine / ADR-Katalog / Ontology / Custody change) | Meta-Governance | Architect + Product Owner | self with explicit dual self-sign | Two distinct persons mandatory |
| **CVE-Fast-Path-Gate** | Security/Compliance | Security Authority (immediate) + Architect (post-hoc ≤ 48h) | SA self + post-hoc audit | SA ≠ post-hoc Architect |

### 6.5 Fast-Paths and Emergency

**Only one fast-path is defined: CVE-Fast-Path-Gate.**

Workflow:
1. Security-Scan-Agent detects CVE in CI or continuous dependency monitor.
2. Security Authority classifies: Critical / High / Medium / Low (per GOV-DORA-001 severity table).
3. **Critical** → SA approves patch deploy immediately, without preceding Conceive/Specify/Design gates. Patch enters Validate with an abbreviated pack.
4. Architect reviews the patch **post-hoc within 48 hours**. Post-hoc review covers: was the patch correct, does it require a follow-up spec/ADR, are there downstream impacts.
5. **If 48h is missed**: the patch is **automatically rolled back** in staging. The CVE-Record is marked `post-hoc-missed`. The fix must then pass through the regular Conceive → Deploy path. The Architect's failure to meet the 48h post-hoc SLA is itself an Operate-Gate incident (triage required).

**High**-severity CVE → Fast-Path with a 72h SLA for the **service repo to be unblocked for new deploys**. The component's deploy gate is locked until the regular review completes.

**Medium/Low** → regular Intent flow; no gate special-casing.

**No other emergency path exists.** "Prod emergency deploy" outside CVE-Fast-Path is not defined — it must go through Prod-Deploy-Gate. Historically, undefined emergency paths have been the single largest source of production incidents; the doctrine rejects this tradeoff.

### 6.6 Auto-Reject Conditions

Agents MUST perform these checks before presenting an artifact at any gate. Any violation is an auto-reject; the human approver does not see nor decide — the artifact is returned to the producer with the failing check.

- **Traceability Gap** — Mandatory forward-link to source artifact absent (§5.4)
- **Custody Violation** — Artifact class has no custody entry, OR vendored copy fails hash-verify against canonical
- **Ontology Violation** — Any of the rules in `io.openleap.dev.guidelines` ontology or the corresponding UI ontology in `io.openleap.ui.guidelines` (Quality Steward's automatic veto)
- **Template Violation** — Artifact does not conform to the template registered in `dev.concepts/templates/` for its class
- **SemVer Violation** — Breaking change without MAJOR bump; deployment record references non-existent version
- **Test Failure or Coverage Regression** at Acceptance-Gate
- **Missing Compliance Evidence** at Prod-Deploy-Gate for DORA-relevant artifacts (per GOV-DORA-001 evidence table)

### 6.7 Escalation and Unavailability

- **Approver unavailable in Team-Mode** → Custody-Katalog names a documented stand-in per role. Stand-in has full approval authority.
- **Approver unavailable in Solo-Mode** → The gate **waits**. The doctrine accepts a blocked workflow over a rule violation. (The single-person case is intentional — in Solo, the system is waiting for the same person to come back, which they will.)
- **Deadlock** between two approvers → Escalation to the next-higher decision class. Architectural deadlocks escalate to Product Owner; Strategic deadlocks escalate to re-opened roadmap review; Meta-Governance deadlocks escalate to a documented external reviewer (named in the Custody-Katalog meta section).
- **Meta-deadlock** (disagreement over the doctrine itself) → open an ADR in the meta-governance folder; the newer ADR wins if it is merged through the Meta-Gate; otherwise the older rule stands.

---

## 7. Actor Model

### 7.1 Human Roles

Seven roles. Each role holds decisions. No agent may hold a human role, though an agent may assist a human role and may recommend an approval or a rejection.

| # | Role | Ownership | Approval-Scope |
|---|---|---|---|
| 1 | **Product Owner (PO)** | Product intent, roadmap, feature priority, variability decisions (Telos/Elara) | Spec-Intent, Feature-Release, Breaking Changes with product impact, Meta-Gate (shared) |
| 2 | **Architect** | Cross-cutting structure: ADR catalog, Ontology, Artifact-Taxonomie in `dev.concepts` | New ADRs, Ontology changes, new artifact classes, Architectural exceptions, Meta-Gate (shared) |
| 3 | **Domain Owner** | Exactly one T3 domain end-to-end (Domain-Spec, services, contracts) | Domain-Spec release, Contract breaks within the domain, Domain-Service promotion |
| 4 | **Platform Owner** | T1 + T2 long-term health, `ops.workflows`, `parent`/`starter`, shared infrastructure | Platform releases, reusable-workflow changes, shared-lib updates, Package-Gate |
| 5 | **Release Authority** | The gate to production | Deployment to production (per environment separately if a staged production is in use) |
| 6 | **Security Authority** | CVE response, compliance, sensitive data | CVE patches, dependency exceptions, data-classification changes |
| 7 | **Quality Steward** | Enforcement of Ontology and Guidelines (Architect evolves the rules; QS enforces the existing rules) | Compliance-Gate (all phases where QS appears in A column), **veto on Prod-Deploy-Gate** |

**Relationship to existing roles in governance docs.** The roles in GOV-DORA-003 and change-deployment-governance.md are referenced and absorbed: an "operations manager" role mentioned there maps to *Platform Owner* + *Domain Owner* in this doctrine; a "change approver" maps to *Release Authority*; a "security officer" maps to *Security Authority*.

### 7.2 Role Distribution (Dual-Mode)

| Mode | Role Distribution | Gate Discipline |
|---|---|---|
| Solo (today) | One person wears all seven hats. Commits, audit-records, and compliance-evidence identify which hat is active for each action. | Self-approval permitted, MUST be audit-substituted |
| Team (target) | Roles assigned to persons (one person may hold up to two non-adversarial roles simultaneously, e.g., Domain Owner + Platform Owner for adjacent scopes; **never** both Release Authority and Security Authority for Prod-Deploy; **never** Author + Quality Steward on the same artifact) | Full SoD per §6.3 |
| Mixed (growth transition) | Specific gates opt into team-mode first (e.g., Prod-Deploy and Meta-Gate) while routine gates remain solo with audit. Custody-Katalog records which gates are in which mode at any time. | Gate-by-gate configuration |

### 7.3 Agent Layer (abstract)

L0 does not enumerate agent roles. At each artifact transition defined in §5 and §4.1, agent assistance is expected. The L0 doctrine guarantees:

- Agents **produce or transform** artifacts; they never **approve**.
- Agents MUST attach a forward-link and perform auto-reject checks (§6.6) on every artifact they produce, before presenting it at a gate.
- Agents MAY recommend decisions; the recommendation is recorded as consulting input, not as a decision.

Concrete agent roles (Spec-Authoring-Agent, Design-Agent, Scaffold-Agent, Test-Agent, Build-Agent, Release-Agent, Deploy-Agent, Security-Scan-Agent, Incident-Agent, Improvement-Agent, etc.) are defined in L1 specs and collected in `operating-model/agent-roles.md`. The catalog grows as L1 specs are written. The underlying implementation (Paperclip team member, standalone Claude Code agent, MCP server, GitHub Actions job, skill) is an L1 choice per role; the doctrine does not prescribe.

---

## 8. RACI Aggregation

Phase-level RACI. L1 specs refine to task-level RACI within their scope.

**Legend.** R = Responsible (executes), A = Accountable (signs off), C = Consulted (opinion required), I = Informed (kept in the loop).

| Phase / Action | PO | Arch | DomO | PlatO | RelA | SecA | QS |
|---|---|---|---|---|---|---|---|
| Conceive | **A** | C | R¹ | R¹ | I | C² | I |
| Specify | C | **A** (structure) | R | R | I | C² | **A** (compliance) |
| Design | I | **A** | R | R | I | C² | **A** (compliance) |
| Implement | I | I | R | R | I | I | I |
| Validate | I | I | **A** (function) | **A** (function) | I | C² | **A** (compliance) |
| Package | I | I | I | **A** + R | I | C² | C |
| Deploy non-prod | I | I | C | C | **A** + R | I | C |
| Deploy prod | C | I | C | C | **A** shared | **A** shared | C + **Veto** |
| Operate (Incident) | I³ | C³ | **A** (functional) | **A** (technical) | C | C⁴ | I |
| Learn (Improvement) | C | R | C | C | I | I | C |
| CVE-Patch (Fast-Path) | I | R (post-hoc) | C | R | C | **A** | R |
| Meta-Change (L0 / Ontology / Custody / ADR-Katalog) | **A** shared | **A** shared + R | C | C | I | C | C |

¹ For Conceive: Platform Owner is R in Platform-Evolution context; Domain Owner in Domain-Evolution; Product Owner in Product-Derivation.
² Security Authority is C when the artifact touches security-relevant code (authorization paths, data handling, crypto, external interfaces) — which most T1 Platform work does. For T3 pure-business work, SecA is I, not C.
³ Sev1/Sev2 incidents escalate to PO (business impact) and Architect (architectural signal).
⁴ Operate is C for Security Authority only when the incident involves a security component or data exposure; otherwise I.

**Structural observations.**
- Prod-Deploy is the **only** gate with shared accountability. Everywhere else, a single A prevents Diffusion of Responsibility.
- Quality Steward is A or Veto at **seven positions**, R at **one** (CVE post-hoc compliance check). QS is an enforcer, not a producer. This is intentional: enforcement must be institutionally separated from production.
- Product Owner is A at Strategic gates only (Conceive, Meta, Prod-Deploy consulted). The doctrine protects the PO from operational overload.

---

## 9. Enforcement Toolchain

The doctrine is enforceable because specific CI workflows mechanize its core checks. These are reusable workflows in `io.openleap.ops.workflows`, consumed by every OpenLeap service repository.

### 9.1 Existing Workflows (reused)

- `maven-build.yml` — Java/Spring service build, test, Nexus deploy, optional Docker push
- `vue-build.yml` — Vue/Nuxt library or application build, npm publish or Docker build
- `docker-build.yml` — Docker build/push with semantic tags (per §5.3 Image rule)
- `security-scan.yml` — OWASP dependency vulnerability check (feeds CVE-Flow §10.3)
- `dora-metrics.yml` — DORA KPI collection (feeds Metric-Flow §10.5)

### 9.2 New Workflows (mandated by this doctrine)

- **`spec-integrity-check.yml`** — Vendored-copy hash-verify. Fails the build if a vendored artifact in `<repo>/spec/` differs from the canonical source registered in `dev.concepts/operating-model/custody.md`.
- **`traceability-check.yml`** — Verifies every commit on a PR carries a `Task:` trailer with a resolvable task-id; verifies deployment-record schema on Deploy-Gate artifacts.

Consuming repositories reference:
```yaml
uses: openleap-io/io.openleap.ops.workflows/.github/workflows/spec-integrity-check.yml@v1
uses: openleap-io/io.openleap.ops.workflows/.github/workflows/traceability-check.yml@v1
```

### 9.3 Workflow Additions L1 Will Commission

L1 specs commission further enforcement workflows, not specified here:
- CVE-Record generation and Auto-Rollback logic (L1 Spec 8)
- Deployment-Record ledger append (L1 Spec 6)
- Improvement-Proposal generator (L1 Spec 2 + 5)

---

## 10. Cross-Cuts

### 10.1 Traceability Rollup

Follows from §5.4. No tooling beyond git + commit-trailer + `spec-integrity-check`. The chain

```
Deployment-Record → Image → Build-Artifact → Code-Version → CHANGELOG → speckref → Spec-Version
```

is mechanically traversable.

### 10.2 Issue Flow

**Sources.** Four documented sources: Incident (Operate), Metric Anomaly (Learn), External Feedback (via product channels), Dependency/CVE alert (Security-Scan).

**Routing rule.** Tier determines owner.
- `T1`/`T2` → Platform Owner (triage)
- `T3/<domain>` → Domain Owner of the affected domain (triage)
- Tag `security` → Security Authority (triage, **parallel** — does not replace the tier router)
- Quality-related issues (ontology-drift, compliance-regression) → Quality Steward is added as Consulted

**Lifecycle.** `Issue → Triage → Accepted-as-Intent → Conceive` *or* `Issue → Rejected (with audit-record)`. No issue may remain in untriaged state longer than **14 days**. Operate-Gate approval of the phase requires zero backlog above this SLA at close-of-quarter review.

**Custody** of the issue catalog is recorded in `operating-model/custody.md` as the class `Issue`. The concrete tool (GitHub Issues per repository, central repo, external tracker) is an L1 Spec 7 decision.

### 10.3 CVE Flow

**Detection.** `security-scan.yml` in CI + continuous dependency monitoring (exact monitor tool decided in L1 Spec 8).

**Classification.** Security Authority classifies per GOV-DORA-001 severity table: Critical / High / Medium / Low.

**Pathways.**
- Critical → CVE-Fast-Path-Gate with 48h post-hoc and Auto-Rollback on miss (§6.5)
- High → Fast-Path with 72h component-deploy-block until regular review completes
- Medium / Low → regular Intent-Flow (Issue → Conceive → …)

**Artifact.** `CVE-Record` — immutable ledger entry containing: CVE-ID, affected versions, patch link (to Build-Artifact coordinates), approval records, post-hoc status (`pending` / `approved` / `missed` → triggering rollback).

### 10.4 Product-Roadmap Flow

**Primary artifacts.** Telos (Feature-Catalog), Elara (Product-Configuration), UVL (Product-Feature-Model). All three carry their own custody entries.

**Flow.** Product Owner creates Roadmap-Entry → Intent (Product-Derivation context) → Conceive. Features traverse the 9-phase cycle in Product-Derivation mode (§4.2): primarily Specify (UVL-Binding), Design (binding→contract), and Package (product-bundle) — without new service code.

**Doctrine rule.** **No product feature activation without UVL-Binding update.** UVL is the single source of truth for "which features belong to which product". Any feature-flag or per-tenant setting that activates functionality independently of UVL is a doctrine violation; existing such mechanisms are migrated under L1 Spec 9.

### 10.5 Metric Flow

**Sources.**
- DORA: deployment frequency, lead time for changes, change failure rate, MTTR (via `dora-metrics.yml`)
- SPLE-specific: feature-adoption per product, variability-coverage, spec-drift-frequency, gate-reject-rates, CVE-class-breakdown

**Consumer.** Learn phase. An Improvement-Agent (L1 Spec 2 defines concrete role) aggregates weekly, produces an Improvement-Proposal with forward-links to metric sources.

**Output paths.**
- Proposal affects Doctrine / Ontology / Templates → **Meta-Gate** (Architect + PO)
- Proposal affects a Domain / Service → **new Intent** (Conceive)
- Proposal affects a Tool / Workflow → **Platform-Owner Intent**

---

## 11. Relationship to Existing Governance

This doctrine is the meta-framework; existing governance documents are instances that plug into it.

| Existing Document | Relationship |
|---|---|
| `governance/template-governance.md` (GOV-TPL-001) | Specializes the Meta-Gate for Template changes. Architect + PO approve; this doctrine gives the frame. |
| `governance/incident-management-policy.md` (GOV-DORA-003) | Specializes the Operate-Gate. Severity-specific SLAs and post-mortem procedures live there. |
| `governance/change-deployment-governance.md` | Specializes the Package + Deploy gates. Change categories and approval flows live there. |
| `governance/devsecops-pipeline.md` | Specializes enforcement mechanics across Implement → Validate → Package. |
| `governance/dora-compliance-framework.md` (GOV-DORA-001) | Provides the compliance-evidence table referenced at Prod-Deploy-Gate. |
| `governance/ict-risk-management-policy.md` | Informs Security Authority's CVE classification criteria. |
| `governance/third-party-governance.md` | Informs Architect's decisions on external-dependency ADRs. |
| `governance/bff-guideline.md` | Specializes BFF-specific Tactical gates within Design. |
| `governance/suite_package_blueprint.md` | Provides the packaging template referenced at Package-Gate. |

When an existing document conflicts with this doctrine, the **newer-dated and more-specific** document wins; conflicts are surfaced via the Meta-Gate and resolved by Architect + PO.

---

## 12. Adoption Roadmap

### 12.1 Immediate (with this doctrine)

- This document (GOV-OM-001) committed
- `operating-model/custody.md` populated with all artifact classes named in §5
- `operating-model/L1-roadmap.md` committed with L1 sequencing
- `operating-model/L1/01-spec-lifecycle.md` through `09-product-definition.md` skeletons committed
- `operating-model/agent-roles.md` skeleton committed (empty; grows with L1 specs)
- Operating-model-specific templates (`templates/operating-model/intent-template.md`, `cve-record-template.md`, `deployment-record-template.md`, `improvement-proposal-template.md`) committed
- `io.openleap.ops.workflows`: `spec-integrity-check.yml` and `traceability-check.yml` committed

### 12.2 L1 Phase 1 (critical path)

L1 Spec 1 (Spec-Lifecycle & Template-Governance) and L1 Spec 2 (Version & Traceability) provide the vocabulary on which the remaining L1 specs depend.

### 12.3 L1 Phase 2 (parallel)

L1 Specs 3, 4, 5, 7 can proceed in parallel once Phase 1 completes.

### 12.4 L1 Phase 3 (late-stage)

L1 Specs 6, 8, 9 depend on Phase 2 completions.

---

## 13. Amendment Process

This doctrine is amendable only through the Meta-Gate (§6.4). Amendment workflow:

1. Proposer drafts an Amendment-ADR in `operating-model/amendments/ADR-OM-nnn-<slug>.md`, referencing the section(s) it modifies.
2. Amendment-ADR links to at least one Improvement-Proposal that motivated it (Learn-phase output).
3. Architect and Product Owner independently review (Solo: two explicit self-signs; Team: two distinct persons).
4. On approval, the Amendment-ADR is merged and this document's version bumped per SemVer:
   - MAJOR: phase count change, role removal, RACI A-shift between roles
   - MINOR: new gate or artifact class, additional RACI cell
   - PATCH: wording clarification, link update

All amendments are append-only in the `amendments/` folder. This document's commit history records the chain.

---

## 14. Glossary

- **ADR** — Architecture Decision Record
- **A / R / C / I** — Accountable / Responsible / Consulted / Informed (RACI)
- **BFF** — Backend-for-Frontend
- **CVE** — Common Vulnerability Enumeration
- **DAG** — Directed Acyclic Graph
- **DORA** — Digital Operational Resilience Act (EU)
- **HITL** — Human-in-the-loop
- **L0 / L1** — Level-0 (this doctrine) / Level-1 (detail specs derived from L0)
- **PO** — Product Owner
- **QS** — Quality Steward
- **RA** — Release Authority
- **RACI** — Responsible, Accountable, Consulted, Informed
- **SA** — Security Authority
- **SPLE** — Software Product Line Engineering
- **SemVer** — Semantic Versioning
- **SoD** — Segregation of Duty
- **T1 / T2 / T3 / T4** — The four service tiers (Platform / Common / Domains / Data)
- **UVL** — Unified Variability Language
