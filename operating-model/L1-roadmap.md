# L1 Specs — Roadmap

> **Document Type:** Platform Governance — Operating Model Index
> **ID:** GOV-OM-004
> **Version:** 1.0 — 2026-04-22
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** Overview of the nine L1 detail specifications that operationalize GOV-OM-001
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)

---

## 1. Purpose

GOV-OM-001 (L0 Operating Doctrine) defines the binding rules across the organization — who, when, with which artifact, at which gate. It intentionally does not prescribe concrete mechanics (which tool tracks issues, how build semver bumps are automated, which agent framework implements which role). Those are L1 concerns.

This file is the **index** for the nine L1 specs that collectively operationalize L0. Each L1 spec has its own skeleton in `L1/` with scope, dependencies, and open questions.

---

## 2. Spec Index

| # | L1 Spec | Primary Scope | Owner (suggested) | Depends On | Status |
|---|---|---|---|---|---|
| 1 | [Spec-Lifecycle & Template-Governance](L1/01-spec-lifecycle.md) | Authoring, review, template maintenance, ADR catalog evolution, new artifact class process | Architect | — | SKELETON |
| 2 | [Version & Traceability](L1/02-version-traceability.md) | SemVer rules per class operationalized, CHANGELOG format, `spec-integrity-check` + `traceability-check` workflow contracts | Platform Owner | L1-1 | SKELETON |
| 3 | [Implementation-Lifecycle](L1/03-implementation-lifecycle.md) | Scaffold → Code → Test for Backend/UI/BFF; agent-role catalog initial entries | Platform Owner + Domain Owner | L1-1, L1-2 | SKELETON |
| 4 | [Quality Control](L1/04-quality-control.md) | Ontology enforcement, ADR adherence, code-review protocol, QS-veto procedure | Quality Steward | L1-1, L1-3 | SKELETON |
| 5 | [Build & Release](L1/05-build-release.md) | Nexus publish rules, Docker image lifecycle, semver-bump automation, multi-repo coordination | Platform Owner | L1-2, L1-3 | SKELETON |
| 6 | [Deployment](L1/06-deployment.md) | Environment graph, promotion rules, non-prod vs prod gate mechanics, rollback protocol | Release Authority | L1-5 | SKELETON |
| 7 | [Issue & Incident Management](L1/07-issue-incident.md) | Issue-Flow operationalized (tool decision), incident severity, triage SLA enforcement | Platform Owner + Domain Owners | L1-1 | SKELETON |
| 8 | [CVE & Security Response](L1/08-cve-security.md) | Detection pipeline, classification criteria, Fast-Path automation, Auto-Rollback mechanics | Security Authority | L1-6, L1-7 | SKELETON |
| 9 | [Product Definition & Roadmap](L1/09-product-definition.md) | Telos/Elara integration, UVL-Binding lifecycle, Product-Derivation-Mode operationalization, feature-release choreography | Product Owner | L1-1, L1-5, L1-6 | SKELETON |

---

## 3. Dependency Graph

```
           ┌────────────────────────────────┐
           │ L1-1 Spec-Lifecycle & Template │
           └─────┬───────────────────┬──────┘
                 │                   │
                 ▼                   ▼
     ┌───────────────────────┐  ┌─────────────────────┐
     │ L1-2 Version & Trace  │  │ L1-7 Issue/Incident │
     └─────┬────────┬────────┘  └──────────┬──────────┘
           │        │                      │
           ▼        ▼                      │
     ┌──────────────────┐                  │
     │ L1-3 Impl-Cycle  │                  │
     └───┬───────┬──────┘                  │
         │       │                         │
         ▼       ▼                         │
   ┌──────────┐  ┌────────────────┐        │
   │ L1-4 QC  │  │ L1-5 Build&Rel │        │
   └──────────┘  └────────┬───────┘        │
                          │                │
                          ▼                │
                 ┌────────────────┐        │
                 │ L1-6 Deploy    │◄───────┘
                 └────┬───────┬───┘
                      │       │
                      ▼       ▼
              ┌───────────┐  ┌─────────────────┐
              │ L1-8 CVE  │  │ L1-9 Product    │
              └───────────┘  └─────────────────┘
```

---

## 4. Sequencing

### Phase A — Foundation (serial)

**L1-1 → L1-2**. These establish the vocabulary (spec version discipline, traceability mechanics) on which every other L1 depends. Parallelizing them is tempting but risky: traceability rules are a consumer of spec versioning rules, and spec lifecycle is a consumer of traceability enforcement.

### Phase B — Concurrent

**L1-3, L1-4, L1-5, L1-7** can proceed in parallel once Phase A is complete. They share a common vocabulary from Phase A but their internals are independent.

### Phase C — Late

**L1-6 (Deployment)** depends on L1-5 (Build) because the deployment record is the downstream of the build artifact.
**L1-8 (CVE)** depends on L1-6 (the Auto-Rollback mechanic) and L1-7 (CVE-Record as an Issue-flow specialization).
**L1-9 (Product)** depends on L1-5 (product-bundle as a packaging specialization) and L1-6 (product-specific deploy orchestration).

---

## 5. Completion Criteria per L1

A L1 spec is considered complete when:

1. Scope, Dependencies, and Invariants (see skeleton template) are fully specified
2. All `Open Question` entries have been resolved (either with an answer or an explicit defer with reason)
3. Custody entries that the L1 creates or fills are committed to `operating-model/custody.md` by the same PR
4. Agent-role entries that the L1 introduces are added to `operating-model/agent-roles.md` by the same PR
5. If the L1 adds CI enforcement, the corresponding reusable workflow(s) exist in `io.openleap.ops.workflows`
6. Meta-Gate approval per GOV-OM-001 §13

---

## 6. Revision

This roadmap is itself subject to Meta-Gate revision. Adding or removing an L1 slot, or changing dependency relations, is a MAJOR amendment to this file and triggers a downstream review of all skeletons that reference it.
