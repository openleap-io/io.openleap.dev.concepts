<!-- Template Meta
     Template-ID:   TPL-FCOMP
     Version:       1.0.0
     Last Updated:  2026-04-03
     Changelog:
       1.0.0 (2026-04-03) — Initial versioned baseline.
-->

# F-{SUITE}-{NNN} --- {Composition Node Name}

> **Conceptual Stack Layer:** Platform-Feature (Composition Node)
> **Space:** Platform
> **Owner:** Domain Engineering Team
> **Companion files:** F-{SUITE}-{NNN}.uvl
> **Referenced by:** Suite Feature Catalog, Platform-Feature Specs (children)

> **Meta Information**
> - **Version:** YYYY-MM-DD
> - **Template:** `feature-composition-spec.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections or "fully compliant"}
> - **Author(s):** Name(s)
> - **Status:** [DRAFT | REVIEW | APPROVED | DEPRECATED]
> - **Feature ID:** `F-{SUITE}-{NNN}`
> - **Suite:** `{suite}`
> - **Node type:** COMPOSITION
> - **Parent:** `F-{SUITE}-{NNN-PARENT}` / suite root
> - **Companion UVL:** `F-{SUITE}-{NNN}.uvl`

> **What this document is**
> A composition node document describes the **variability structure** over a
> set of child features. It does NOT contain a user journey, screens, or
> service calls --- those live in leaf nodes.
>
> This document answers:
> - What capability area does this node group?
> - What is the boolean relationship between its children?
> - Why are the children structured this way?
>
> The companion `.uvl` file is the machine-readable source of truth for
> the group logic. This document explains the intent behind it.

---

<!-- ============================================================
     SS0 --- IDENTITY
     WHO is this composition node, WHERE does it sit in the tree,
     and WHAT capability area does it represent?
     ============================================================ -->

## SS0. Identity

### 0.1 Purpose

<!-- 2-3 sentences: what user-facing capability area does this composition
     node group? What is the business reason for grouping these features here?
     Frame in terms of what the user can do, not how the system works.

     EXAMPLE:
     "This composition node groups all capabilities related to Sales Order
     management. A product that includes any order-related feature must
     include at least the mandatory children below."
-->

### 0.2 Children

<!-- List every direct child of this composition node. Each child is either
     a LEAF (has its own Feature Spec with user journey, screens, etc.) or
     another COMPOSITION (has its own Composition Spec). The "Group membership"
     column reflects the UVL group type this child belongs to.

     Keep this table in sync with the companion .uvl file. -->

| Child ID | Name | Node type | Group membership |
|---|---|---|---|
| `F-{SUITE}-{NNN}-01` | {Name} | LEAF | mandatory |
| `F-{SUITE}-{NNN}-02` | {Name} | LEAF | optional |
| `F-{SUITE}-{NNN}-03` | {Name} | COMPOSITION | alternative |
| `F-{SUITE}-{NNN}-04` | {Name} | LEAF | alternative |

### 0.3 Position in the Feature Tree

<!-- Show where this node sits relative to its parent and children.
     Use a simple ASCII tree. This gives readers immediate orientation
     without having to navigate the UVL or the suite catalog. -->

```
F-{SUITE}-{NNN-PARENT}  {Parent name}
+-- F-{SUITE}-{NNN}     {This node}      <-- you are here
    +-- F-{SUITE}-{NNN}-01  {Child}
    +-- F-{SUITE}-{NNN}-02  {Child}
    +-- F-{SUITE}-{NNN}-03  {Child}
```

---

<!-- ============================================================
     SS1 --- CHILDREN & VARIABILITY STRUCTURE
     HOW are the children related? What group types apply?
     WHY is the tree structured this way?
     ============================================================ -->

## SS1. Children & Variability Structure

### 1.1 Group Logic

<!-- Describe in plain language the boolean structure over the children.
     The UVL in the companion file is authoritative --- this section
     explains the *intent* behind it.

     Choose one of the group types below and explain the business rationale.

     GROUP TYPES:
     - mandatory:    "All of these features are always required together."
     - optional:     "This feature may or may not be included."
     - alternative:  "Exactly one of these variants must be chosen --- they
                      are mutually exclusive because {reason}."
     - or:           "At least one of these must be included. Multiple can
                      be combined because {reason}."
     - mixed:        Describe the sub-groups below.

     If the node uses "mixed" (different group types for different subsets
     of children), describe each sub-group separately with its own rationale. -->

**Group type:** [mandatory | optional | alternative | or | mixed]

**Business rationale for this structure:**

<!-- Why is this group structured this way?

     EXAMPLE:
     "Full and read-only detail are alternative because including both would
     create two competing access points to the same record, causing
     navigation ambiguity. A product must choose one."

     BAD:  "Because the UVL says so."
     GOOD: "These are alternative because {business reason}." -->

### 1.2 Sub-Groups (if mixed)

<!-- Only fill this section if the group type above is "mixed".
     Otherwise delete it or leave it empty.

     For each sub-group, list the children, the sub-group type, and
     the rationale for choosing that type. -->

| Sub-group | Children | Group type | Rationale |
|---|---|---|---|
| {e.g., "Core views"} | `{NNN}-01`, `{NNN}-02` | mandatory | {Why these are always required together} |
| {e.g., "Detail variants"} | `{NNN}-03`, `{NNN}-04` | alternative | {Why exactly one must be chosen} |

### 1.3 Rationale for Tree Position

<!-- Why does this composition node exist at this level in the tree?
     Why is it not merged into its parent or split into smaller nodes?

     Address:
     - What would be lost if these children were promoted to the parent level?
     - What would be lost if this node were further decomposed?
     - Is this grouping driven by business capability, user workflow, or
       variability concerns? -->

---

<!-- ============================================================
     SS2 --- CONSTRAINTS
     WHAT rules govern valid combinations of children?
     These are formalised in the companion .uvl file.
     ============================================================ -->

## SS2. Constraints

### 2.1 Intra-Node Cross-Tree Constraints

<!-- Constraints that apply within this composition node --- between siblings
     or between a child and a grandchild. These are documented here and
     formalised in the companion .uvl.

     Each row must have a business rationale. Constraints without rationale
     are a specification smell --- they suggest a missing design decision.

     EXAMPLE:
     If F-SD-001-02 (Create Order) is included, then
     F-SD-001-03-01 (Order Detail Full) must also be included,
     because creation without a detail view leaves the user with
     no way to verify or edit the created record. -->

| If | Then | Rationale |
|---|---|---|
| `F-{SUITE}-{NNN}-02` is included | `F-{SUITE}-{NNN}-03-01` must be included | {Why} |
| `F-{SUITE}-{NNN}-03` variant A is chosen | `F-{SUITE}-{NNN}-04` is excluded | {Why} |

### 2.2 Cross-Node Constraints

<!-- Constraints that span beyond this composition node --- e.g., a child
     of this node requires a feature in a different suite or a different
     subtree of the same suite.

     These are important for product configuration: if a product includes
     children from this node, it may transitively require features elsewhere.

     Leave empty if no cross-node constraints exist. -->

| Child | Requires / Excludes | External feature | Rationale |
|---|---|---|---|
| `F-{SUITE}-{NNN}-02` | requires | `F-{OTHER_SUITE}-{NNN}` | {Why --- e.g., needs customer data from BP} |

### 2.3 Constraint Summary (UVL Reference)

<!-- Point readers to the companion .uvl file for the machine-readable
     constraint definitions. The tables above are the human explanation;
     the .uvl is the authoritative source.

     Format:
     Companion file: F-{SUITE}-{NNN}.uvl
     Constraint count: {N} intra-node + {M} cross-node
     Last validated: {YYYY-MM-DD} -->

**Companion file:** `F-{SUITE}-{NNN}.uvl`
**Constraint count:** {N} intra-node + {M} cross-node
**Last validated:** {YYYY-MM-DD}

---

<!-- ============================================================
     SS3 --- VALID & INVALID CONFIGURATIONS
     CONCRETE EXAMPLES of what works and what does not.
     These make the constraints tangible for product owners and
     business analysts who will not read the .uvl directly.
     ============================================================ -->

## SS3. Valid & Invalid Configurations

### 3.1 Valid Configurations

<!-- Enumerate the meaningful valid leaf selections under this node.
     This makes the constraints human-readable without requiring the
     reader to evaluate the UVL manually.

     For large trees, list the most common/important cases only.
     Each configuration should have a name, the selected leaves,
     and the use case (when would a product choose this combination?). -->

| Configuration name | Selected leaves | Use case |
|---|---|---|
| {e.g., "Full access"} | `{01}`, `{02}`, `{03-01}` | {When a product needs full capability} |
| {e.g., "Read-only"} | `{01}`, `{03-02}` | {When a product only needs to display} |
| {e.g., "Minimal"} | `{01}` | {When only the core capability is needed} |

### 3.2 Invalid Configurations

<!-- Explicitly name the combinations that are NOT valid.
     This helps authors understand the constraints without running
     the validator.

     Each row should reference the specific constraint that is violated
     and explain in plain language why the combination is invalid. -->

| Invalid combination | Violated constraint | Why invalid |
|---|---|---|
| `{02}` without `{03-01}` | `F-{SUITE}-{NNN}-02 => F-{SUITE}-{NNN}-03-01` | {Reason} |
| Both `{03-01}` and `{03-02}` | alternative group | {Reason} |

### 3.3 Edge Cases

<!-- Document any configurations that are technically valid but
     discouraged, or configurations that are surprisingly invalid.
     These are the cases that trip up product teams during configuration. -->

| Configuration | Status | Notes |
|---|---|---|
| {e.g., "Only {04} without {01}"} | Valid but discouraged | {Why --- e.g., usable but missing primary entry point} |

---

<!-- ============================================================
     SS4 --- CHANGE LOG
     Version history, open questions, decisions, review status.
     ============================================================ -->

## SS4. Change Log

### 4.1 Open Questions

| ID | Question | Impact | Owner | Needed by |
|---|---|---|---|---|
| Q-001 | {Should {child} be optional or mandatory?} | {ProductConfig flexibility} | {Name} | {Date} |

### 4.2 Decision Log

<!-- Record structural decisions about this composition node.
     Each decision should document what was decided, why, and what
     alternatives were rejected.

     EXAMPLE:
     D-001: "F-SD-001-03 is alternative (not or) because including
     both Full and Read-Only detail views creates navigation ambiguity.
     Rejected alternative: or-group with runtime toggle." -->

#### D-001: {Decision Title}

**Decided:** {Date} by {Name}
**Decision:** {What --- e.g., "F-{SUITE}-{NNN}-03 is alternative, not or, because..."}
**Rationale:** {Why}
**Rejected alternatives:** {What else was considered}

### 4.3 Version History

| Date | Version | Author | Changes |
|------|---------|--------|---------|
| YYYY-MM-DD | 1.0 | {Name} | Initial composition node |

---

## Review & Approval

**Status:** [DRAFT | IN REVIEW | APPROVED]

**Reviewers:**
- Suite UX Lead: {Name} --- {Date}
- Suite Architect: {Name} --- {Date}

**Approval:**
- Suite Architect: {Name} --- {Date} --- [ ] Approved
