# L1 Spec 9 — Product Definition & Roadmap

> **Document Type:** Platform Governance — Operating Model Detail (L1)
> **ID:** GOV-OM-L1-09
> **Version:** 0.1 — 2026-04-22
> **Status:** SKELETON — awaiting content
> **Author:** OpenLeap Architecture Team
> **Scope:** Telos/Elara integration, UVL-Binding lifecycle, Product-Derivation-Mode operationalization, feature-release choreography
> **Parent:** GOV-OM-001 (L0 Operating Doctrine)
> **Owner (suggested):** Product Owner

---

## 1. Purpose

Operationalize the Product-Roadmap Flow (GOV-OM-001 §10.4) and the Product-Derivation Invocation Mode (§4.2). Define how Telos (Feature-Catalog), Elara (Product-Configuration), and UVL-Bindings interact across the 9-phase lifecycle. Enforce the doctrine rule: **no product feature activation without UVL-Binding update** (§10.4).

---

## 2. Scope

**In scope.**
- Telos Feature-Catalog structure and lifecycle (maintained by Product Owner; content flows into Platform-Feature-Catalog in artifact-catalog.md)
- Elara Project (= Product) lifecycle and its artifact flows
- UVL-Binding lifecycle: draft → selection → bound → deployed
- Product-Derivation mode: which phases produce what, with which gates (Specify+Design+Package focus; no new code unless a capability gap forces extension)
- Feature-release choreography: when a feature is ready → product which includes it gets a rebind → new product bundle → deploy
- `io.openleap.prod.<product>` repo convention alignment with this L1

**Out of scope.** Individual domain content (→ L1-1); build/deploy mechanics (→ L1-5, L1-6).

---

## 3. Dependencies on L0

- GOV-OM-001 §4.2 — Product-Derivation Invocation Mode
- GOV-OM-001 §7.1 — Product Owner role
- GOV-OM-001 §8 — RACI for Conceive (Product-Intent path)
- GOV-OM-001 §10.4 — Product-Roadmap Flow Cross-Cut

---

## 4. Dependencies on other L1 Specs

- L1-1 (Spec-Lifecycle) — Spec mechanics for Product-Spec
- L1-5 (Build & Release) — product-bundle packaging
- L1-6 (Deployment) — per-tenant / per-product deploy mechanics

---

## 5. Invariants derived from L0

- No product feature activation without UVL-Binding update (§10.4)
- UVL is single truth for "which features belong to which product"
- Existing feature-flag / per-tenant settings that activate functionality outside UVL are doctrine-violations to be migrated under this L1

---

## 6. Open Questions

1. Telos tooling: UI for roadmap? Direct markdown in `io.openleap.telos`? Integration with Elara UI?
2. Elara <-> `io.openleap.prod.<product>` split: Elara holds in-progress product; prod repo is the canonical committed product?
3. UVL-Binding editing: Telos CLI? Web tool? IDE plugin?
4. Migration path for any existing non-UVL feature flags: grandfathered with sunset date? Immediate block?
5. Product-release choreography when multiple products share an updated feature: coordinated release or per-product-when-ready?
6. Product-specific customization fills and extensions: scope and mechanics (`io.openleap.prod.<product>/prod/extensions/`)
7. Customer-specific services outside the platform: how are they integrated and versioned (cross-reference `artifact-catalog.md` `customServices`)?

---

## 7. Artifacts this L1 Registers in custody.md

- Formalization of Product + UVL-Binding + Elara-Project custody (references existing artifact-catalog.md §2)
- Possible new: `Feature-Release-Plan` — if used

---

## 8. Agent Roles this L1 Introduces

- `Product-Config-Agent` — assists Product Owner in maintaining Elara configuration
- `UVL-Binding-Agent` — proposes bindings; flags conflicts between feature selections
- `Product-Bundle-Agent` — packages a product release from current bindings

---

## 9. CI Workflows this L1 Commissions

- Possible: `uvl-binding-validate.yml` — checks binding completeness and consistency
- Possible: `product-bundle-build.yml` — per-product bundle assembly

---

## 10. Completion Criteria

Per L1-roadmap.md §5.
