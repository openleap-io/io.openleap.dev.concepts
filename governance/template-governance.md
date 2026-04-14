# Template Governance — Versioning & Change Management

> **Document Type:** Platform Governance
> **ID:** GOV-TPL-001
> **Version:** 1.0 — 2026-04-03
> **Status:** PROPOSED
> **Author:** OpenLeap Architecture Team
> **Scope:** All specification templates and structural templates in the OpenLeap platform

---

## 1. Purpose

OpenLeap uses specification templates to ensure consistency across all
suites, services, features, and products. As the platform evolves, these
templates change — new sections are added, existing sections are
restructured, conventions shift.

Without version tracking, there is no way to know:
- Which template version a given spec was written against
- Whether a spec is "compliant" with the current template
- What changed between template versions
- Which specs need updating after a template change

This document defines the rules for versioning, referencing, and
evolving templates.

---

## 2. Scope — What Is a Template?

A **template** is a file that contains placeholder patterns (e.g.,
`{SUITE}`, `{NNN}`, `{domain}`) and is intended to be instantiated
into concrete specification documents. Templates define the **structure
and conventions** that all instances must follow.

A template is NOT:
- A concrete spec (e.g., `srv_cat-spec.md`) — that is a template **instance**
- A platform reference doc (e.g., [system-topology.md](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/system-topology.md)) — that is a **living document**
- A generated artifact (e.g., `srv.catalog.uvl`) — that is an **output**

---

## 3. Template Registry

### 3.1 Central Registry File

All template versions are tracked in a single machine-readable file:
**`template-registry.json`**

This file is the single source of truth for template versions. It lives
alongside the templates in the repository root and is updated with every
template change.

```json
{
  "$schema": "template-registry.schema.json",
  "lastUpdated": "2026-04-03",
  "templates": [
    {
      "id": "TPL-SVC",
      "file": "domain-service-spec.md",
      "name": "Domain / Service Specification",
      "format": "markdown",
      "version": "1.0.0",
      "updatedAt": "2026-04-03",
      "owner": "Architecture Team",
      "changelog": [
        { "version": "1.0.0", "date": "2026-04-03", "summary": "Initial versioned baseline" }
      ]
    }
  ]
}
```

Benefits:
- **Tooling:** `telos validate-spec` reads this file to compare against spec references
- **Dashboards:** CI can generate a compliance matrix (spec × template version)
- **Single source:** No need to parse each template header to build the version list

The template header (§5) remains as human-readable documentation, but
the registry file is authoritative.

### 3.2 Specification Templates

| Template ID | File | Purpose | Version |
|---|---|---|---|
| `TPL-SVC` | `domain-service-spec.md` | Domain / Service specification for microservices | `1.0.0` |
| `TPL-SUITE` | `suite-spec.md` | Suite-level architecture specification | `1.0.0` |
| `TPL-FEAT` | `feature-spec.md` | Leaf feature specification (UI screen / use case) | `1.0.0` |
| `TPL-FCOMP` | `feature-composition-spec.md` | Composition feature node specification | `1.0.0` |
| `TPL-PROD` | `product-spec.md` | Product specification (ProductConfig + customization) | `1.0.0` |
| `TPL-WF` | `workflow-spec.md` | Workflow / saga specification | `1.0.0` |
| `TPL-FATTR` | `feature-attrs.md` | Feature attribute documentation (for complex attrs) | `1.0.0` |

### 3.3 Structural Templates (UVL / AUI)

| Template ID | File | Purpose | Version |
|---|---|---|---|
| `TPL-UVL-LEAF` | `feature-leaf.uvl` | UVL template for leaf features | `1.0.0` |
| `TPL-UVL-COMP` | `feature-composition.uvl` | UVL template for composition nodes | `1.0.0` |
| `TPL-UVL-CAT` | `suite-catalog.uvl` | UVL template for suite-level catalog | `1.0.0` |
| `TPL-UVL-PLAT` | `platform-catalog.uvl` | UVL template for platform root catalog | `1.0.0` |
| `TPL-UVL-PCFG` | `product-config.uvl` | UVL template for product configuration | `1.0.0` |
| `TPL-AUI` | `screen-contract-aui.yaml` | AUI screen contract template | `1.0.0` |

### 3.4 Baseline

The current templates in the repository have no version metadata. This
governance document retroactively assigns `1.0.0` to all templates as
their baseline. The first change to any template after adoption becomes
`1.0.1` (patch) or `1.1.0` (minor) as appropriate.

---

## 4. Versioning Rules

### 4.1 Semantic Versioning

Templates follow **Semantic Versioning** (MAJOR.MINOR.PATCH):

| Change type | Version bump | Impact on existing specs |
|---|---|---|
| **PATCH** — typo, comment, rewording, formatting | `x.y.Z+1` | None. Specs are still compliant. |
| **MINOR** — new optional section, new optional field, new guidance | `x.Y+1.0` | Specs are **valid but incomplete**. Should be updated. Not broken. |
| **MAJOR** — section removed, section restructured, mandatory field added | `X+1.0.0` | Specs **must be migrated**. Non-compliant until updated. |

### 4.2 Classification Examples

| Change | Classification | Rationale |
|---|---|---|
| Fix typo in a comment | PATCH | No structural impact |
| Add `<!-- optional -->` section for custom fields | MINOR | Existing specs don't have it but aren't wrong |
| Add new required column to an existing table | MAJOR | Existing specs are now missing required content |
| Remove a section | MAJOR | Existing specs have unmapped content |
| Rename a section | MAJOR | Breaks cross-references |
| Change placeholder naming convention | MAJOR | All instances use the old convention |
| Add guidance inside `<!-- -->` comments | PATCH | Not visible in rendered output |
| Add a new ADR reference | PATCH | Informational only |
| Restructure section numbering | MAJOR | All cross-references break |

### 4.3 Backward Compatibility

- **PATCH and MINOR** are backward-compatible: specs written against
  `1.0.0` are valid against `1.1.0` (they just miss new optional
  sections).
- **MAJOR** is breaking: specs written against `1.x` are NOT valid
  against `2.0` and must be migrated.

---

## 5. Template Header Format

Every template includes a version header block for human readability.
The **authoritative** version is in `template-registry.json`; the
header is a convenience for developers reading the template directly.

### 5.1 Markdown Templates

```markdown
# {Template Title}

<!-- Template Meta
     Template-ID:   TPL-SVC
     Version:       1.1.0
     Last Updated:  2026-04-03
     Changelog:
       1.1.0 (2026-04-03) — Added §3.x Extensible Entity convention,
                             §6.x customFields in API contracts,
                             §12.6 Custom Field Extension Points.
       1.0.0 (2026-04-03) — Initial versioned baseline.
-->

> **Template Version:** `1.1.0` (2026-04-03)
```

### 5.2 UVL Templates

```
// Template Meta
// Template-ID:   TPL-UVL-LEAF
// Version:       1.0.0
// Last Updated:  2026-04-03
// Changelog:
//   1.0.0 (2026-04-03) — Initial versioned baseline.
```

### 5.3 YAML Templates

```yaml
# Template Meta
# Template-ID:   TPL-AUI
# Version:       1.0.0
# Last Updated:  2026-04-03
# Changelog:
#   1.0.0 (2026-04-03) — Initial versioned baseline.
```

---

## 6. Instance Reference Format

Every spec instantiated from a template MUST reference the template
version it was written against. This goes in the document's meta
information block:

```markdown
> **Meta Information**
> - **Version:** 2026-04-03
> - **Template:** `domain-service-spec.md` v1.1.0
> - **Template Compliance:** ~85% — §11, §12 missing
> - **Status:** DRAFT
> - ...
```

### 6.1 Compliance Score

Every spec MUST include a **Template Compliance** line in the meta block
that indicates how closely the spec follows the referenced template
version. This is especially important during the migration from
unversioned to versioned templates, where many specs were written before
formal template structure existed.

#### 6.1.1 Format

```markdown
> - **Template Compliance:** ~{NN}% — {missing or incomplete sections}
```

For a fully compliant spec:
```markdown
> - **Template Compliance:** 100%
```

#### 6.1.2 Calculating the Score

The compliance score is a **percentage of required template sections
that are present and structurally correct** in the spec:

```
score = (present required sections / total required sections) × 100
```

**Required sections** are all numbered sections (§0–§15 for TPL-SVC,
etc.) defined in the template. Optional sections (marked
`<!-- optional -->` in the template) do not count against compliance.

**Present** means the section exists with meaningful content — not just
a heading with placeholder text.

#### 6.1.3 Missing Sections Summary

After the percentage, list the missing or incomplete sections using
their section numbers:

```markdown
> - **Template Compliance:** ~65% — §2, §11, §12 missing
```

If sections are partially present, use "incomplete":
```markdown
> - **Template Compliance:** ~80% — §6 incomplete, §12 missing
```

#### 6.1.4 Relationship to HTML Compliance Comments

Many specs already contain a detailed `<!-- TEMPLATE COMPLIANCE: ~NN% -->`
HTML comment block at the top of the file with section-by-section audit
information:

```html
<!-- TEMPLATE COMPLIANCE: ~65%
Template: domain-service-spec.md v1.0.0
Present sections: §0 (...), §1 (...), §3 (...)
Missing sections: §2 (...), §11 (...), §12 (...)
-->
```

The **HTML comment** is the detailed audit record (for tooling and
reviewers). The **meta block line** is the human-readable summary (for
anyone reading the spec). Both SHOULD be kept in sync:

- The percentage in the meta block matches the HTML comment
- The missing sections list in the meta block is a condensed version of
  the HTML comment's detail

When updating compliance, update **both** locations.

### 6.2 Intentional Deviations

Some specs intentionally deviate from the template — for example, a
simple infrastructure service that does not need all sections of
`TPL-SVC`. This is acceptable and MUST be documented explicitly:

```markdown
> **Template:** `domain-service-spec.md` v1.1.0
> **Deviations:**
> - §11 Feature Dependency Register: omitted (infrastructure service, no UI features)
> - §12 Extension Points: omitted (no product-level extensibility needed)
```

**Rules for intentional deviations:**
- The spec MUST still reference the template version
- Each omitted or modified section MUST be listed with a rationale
- Deviations are **per-spec**, not per-suite (one service may deviate,
  its sibling may not)
- Reviewers verify that deviations are justified, not accidental

This ensures compliance checking can distinguish "intentionally
different" from "accidentally outdated."

### 6.3 Implementation Status Tracking

Specs may or may not have a corresponding implementation repository on
GitHub. The mapping between specs and their implementations is tracked
in a separate registry:

**`https://github.com/openleap-io/io.openleap.dev.hub/blob/main/landscape/impl-status.json`**

This file tracks, for each spec:
- Whether an implementation repository exists
- The implementation's spec version (if the repo contains a `spec/` folder)
- The **version sync** state between spec and implementation

#### 6.3.1 Version Sync States

| State | Meaning |
|---|---|
| `in-sync` | Spec version and implementation spec version match |
| `spec-ahead` | Spec repo has been updated more recently than the implementation |
| `impl-ahead` | Implementation has changes not yet reflected in the spec repo |
| `diverged` | Both have changed independently; manual reconciliation needed |
| `spec-only` | No implementation repository exists |
| `impl-only` | Implementation exists but no spec in this repo |
| `unknown` | Repository exists but hasn't been crawled yet |

#### 6.3.2 Crawling

The implementation status is updated by running the crawl prompt
(`prompts/crawl-implementation-status.md`) which uses the GitHub CLI to
check each mapped repository for a `spec/` folder and compare version
dates.

The `lastCrawled` field in the registry indicates when the last
automated check was performed. The registry should be re-crawled at
least monthly or after significant spec updates.

---

## 7. Change Management Process

### 7.1 Proposing a Template Change

Template changes go through normal **Pull Request** review:

1. **Author** modifies the template file and updates:
   - The template header (version + changelog entry)
   - The `template-registry.json` (version + changelog entry)
   - If MAJOR: a migration guide as a companion document

2. **PR review** by at least one Architecture Team member. Review
   checks:
   - Is the version bump classification correct?
   - Is the changelog entry clear?
   - For MAJOR: is the migration guide complete?
   - Are dependent templates affected (see §8)?

3. **Merge.** No formal ADR required for PATCH or MINOR changes.
   MAJOR changes SHOULD have an ADR or enhancement document (like
   `custom-fields-extension-enhancement.md`) as context, but the
   PR itself is the approval mechanism.

### 7.2 Propagating Changes to Existing Specs

| Version bump | Propagation strategy |
|---|---|
| **PATCH** | No action needed. Specs are still compliant. |
| **MINOR** | Create one backlog item per suite to update existing specs. Priority: normal. Specs without the new section still pass validation but are flagged as "incomplete." |
| **MAJOR** | Create a migration guide. Create one backlog item per affected spec. Priority: high. Deadline: 1 quarter. Specs not migrated after deadline are flagged as non-compliant. |

### 7.3 Registry Update Rule

Every PR that modifies a template file MUST also update
`template-registry.json` in the same commit. CI can enforce this with a
simple check: "if any template file changed, registry must also have
changed."

---

## 8. Template Dependency Graph

Templates reference each other. Changes to one may require awareness
updates in dependents:

```
platform-catalog.uvl (TPL-UVL-PLAT)
    └── imports suite-catalog.uvl (TPL-UVL-CAT)
        └── imports feature-composition.uvl (TPL-UVL-COMP)
            └── imports feature-leaf.uvl (TPL-UVL-LEAF)

product-spec.md (TPL-PROD)
    └── references feature-spec.md (TPL-FEAT) — §17 Feature Customization
    └── references suite-spec.md (TPL-SUITE) — §16 Suite Selection

suite-spec.md (TPL-SUITE)
    └── references domain-service-spec.md (TPL-SVC) — §3 Service Landscape
    └── references feature-composition-spec.md (TPL-FCOMP) — §6 Feature Catalog

feature-spec.md (TPL-FEAT)
    └── references screen-contract-aui.yaml (TPL-AUI) — §6 AUI
    └── references feature-leaf.uvl (TPL-UVL-LEAF) — §9 UVL
    └── references domain-service-spec.md (TPL-SVC) — §5 Backend Dependencies

domain-service-spec.md (TPL-SVC)
    └── references feature-spec.md (TPL-FEAT) — §11 Feature Dependency Register
    └── references feature-leaf.uvl (TPL-UVL-LEAF) — §12 Extension Points
```

**Rule:** When a template changes in a way that affects a dependent
template's conventions, the dependent SHOULD get at least a PATCH bump
to acknowledge the change in its changelog. This prevents silent
incompatibilities.

---

## 9. Governance Roles

| Role | Responsibility |
|---|---|
| **Template Owner** | Each template has one owner (person or team) who approves changes. Default: Architecture Team. |
| **Spec Author** | References the correct template version. Documents intentional deviations. |
| **PR Reviewer** | Verifies version bump classification, registry update, and changelog entry. |
| **CI (future)** | Enforces: template file changed → registry changed. Reports compliance matrix. |

---

## 10. Central Registry File — Schema

### 10.1 File: `template-registry.json`

```json
{
  "$schema": "template-registry.schema.json",
  "lastUpdated": "2026-04-03",
  "templates": [
    {
      "id": "TPL-SVC",
      "file": "domain-service-spec.md",
      "name": "Domain / Service Specification",
      "format": "markdown",
      "version": "1.0.0",
      "updatedAt": "2026-04-03",
      "owner": "Architecture Team",
      "dependsOn": ["TPL-FEAT", "TPL-UVL-LEAF"],
      "dependedBy": ["TPL-SUITE", "TPL-FEAT"],
      "changelog": [
        {
          "version": "1.0.0",
          "date": "2026-04-03",
          "classification": "baseline",
          "summary": "Initial versioned baseline"
        }
      ]
    }
  ]
}
```

### 10.2 Registry Maintenance Rules

- Every PR that changes a template file MUST also update the
  corresponding entry in `template-registry.json`
- The `lastUpdated` field at the root reflects the date of the most
  recent template change
- CI can enforce: `git diff --name-only | grep template-file` →
  must also see `template-registry.json` in the diff
- The `dependsOn` / `dependedBy` fields enable tooling to flag
  cascade impacts

---

## 11. Initial Rollout Plan

### Phase 1: Adopt Governance (Week 1)

- Commit `template-registry.json` with all 13 templates at `1.0.0`
- Add version headers to all 13 template files
- Add `Template:` reference convention to each template's meta block
  (so new specs automatically include the reference)

### Phase 2: First Template Update — Custom Fields (Week 2)

- Update `TPL-SVC` (`domain-service-spec.md`) to `1.1.0`
  - New optional §3.x: Extensible Entity convention
  - New optional §6.x: `customFields` in API contracts
  - New optional §12.6: Custom Field Extension Points
- Update `TPL-FEAT` (`feature-spec.md`) to `1.1.0`
  - New optional §9.3: Custom field extension point type in table
- Update `TPL-UVL-LEAF` (`feature-leaf.uvl`) to `1.1.0`
  - New: `customFields` attribute convention in comments
- Update `template-registry.json` with all three version bumps
- Existing specs are valid but flagged as "incomplete vs 1.1.0"

### Phase 3: Backlog Creation (Week 3)

- Create one backlog item per suite to update existing specs to
  template v1.1.0
- Priority: normal (MINOR change, not breaking)

---

## 12. Decisions

| ID | Decision | Rationale |
|---|---|---|
| D-001 | Central registry file (`template-registry.json`) as single source of truth for versions | Enables tooling, dashboards, CI checks. Template headers are human convenience, registry is authoritative. |
| D-002 | Intentional template deviations documented per-spec in meta block | Allows simpler services to skip sections without being flagged as non-compliant. Explicit documentation prevents "accidentally missing" ambiguity. |
| D-003 | PR review sufficient for PATCH/MINOR template changes; no formal ADR required | Reduces process overhead. MAJOR changes should have an enhancement doc or ADR as PR context, but the PR itself is the approval. |
| D-004 | GIN index always-on for `custom_fields` columns | See ADR-PLATFORM-007 (Custom Fields Extension). Cost of unused GIN negligible; cost of retroactive GIN on populated tables is high. |

---

## 13. Open Questions

| ID | Question | Impact | Owner | Needed by |
|---|---|---|---|---|
| Q-002 | Should `telos validate-spec` be a CLI tool, a CI check, or both? | Affects enforcement | DevOps | Phase 3 |

---

## Appendix: Quick Reference Card

```
TEMPLATE CHANGE? → Classify:

  Typo / comment / rewording?
    → PATCH (x.x.+1). No action on specs.

  New optional section / field / guidance?
    → MINOR (x.+1.0). Specs valid but incomplete. Backlog items.

  Removed / restructured / mandatory addition?
    → MAJOR (+1.0.0). Migration guide. High-priority. 1Q deadline.

IN EVERY TEMPLATE-CHANGE PR:
  □ Template header updated (version + changelog)
  □ template-registry.json updated (version + changelog)
  □ Dependent templates checked (§8 dependency graph)

WRITING A SPEC? → Include in meta block:
  > **Template:** `{template-file}` v{X.Y.Z}

INTENTIONAL DEVIATION? → Document:
  > **Deviations:**
  > - §{N}: omitted ({reason})

REVIEWING A SPEC? → Check:
  □ Template reference present?
  □ Template version is current (or deviations documented)?
  □ All required sections from referenced version present?
```
