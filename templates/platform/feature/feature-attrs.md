<!-- Template Meta
     Template-ID:   TPL-FATTR
     Version:       1.0.0
     Last Updated:  2026-04-03
     Changelog:
       1.0.0 (2026-04-03) — Initial versioned baseline.
-->

# F-{SUITE}-{NNN}-{NN} — Attribute Documentation

> **Conceptual Stack Layer:** Platform-Feature (Attribute Documentation)
> **Space:** Platform
> **Owner:** Domain Engineering Team
> **Companion of:** Feature Spec (`F-{SUITE}-{NNN}-{NN}.md`) + Feature UVL (`F-{SUITE}-{NNN}-{NN}.uvl`)
> **When to create:** Only when at least one UVL attribute requires prose explanation beyond what fits inline.

> **Meta Information**
> - **Feature ID:** `F-{SUITE}-{NNN}-{NN}`
> - **Feature Name:** {Feature Name}
> - **Template:** `feature-attrs.md` v1.0.0
> - **Template Compliance:** {score}% — {missing sections or "fully compliant"}
> - **Companion UVL:** `F-{SUITE}-{NNN}-{NN}.uvl`
> - **Companion Spec:** `F-{SUITE}-{NNN}-{NN}.md`

> **What this document is**
> This file documents the attributes declared in the companion `.uvl` file
> for attributes whose valid values, semantics, or interactions need
> more explanation than fits inline in UVL.
>
> Simple attributes (a page size integer, a boolean toggle) need no
> separate documentation. Create this file only when at least one
> attribute requires prose explanation of what its values mean.
>
> Each section heading matches the `schema:` anchor reference in the `.uvl`.
> Example: if the `.uvl` has `schema: "F-SD-001-02.attrs.md#approval-workflow"`,
> the section heading here is `## approval-workflow`.

---

## {attribute-name}

**UVL declaration:**
```
{attr.path} {Type} "{default}"
    schema: "F-{SUITE}-{NNN}-{NN}.attrs.md#{attribute-name}"
{attr.path}.bindingTime String "{compile|deploy|runtime}"
```

**Binding time:** `{compile | deploy | runtime}`

**Type:** `{Boolean | Integer | Float | String}`

**Default value:** `{value}`

**Valid values:**

| Value | Meaning | When to use |
|---|---|---|
| `"{value-1}"` | {What it means in business terms} | {When a product should choose this} |
| `"{value-2}"` | {What it means in business terms} | {When a product should choose this} |

**Effect on the UI:**
<!-- Describe what changes in the user-facing feature when each value is active.
     Reference the §4.3 row in the feature spec. -->
- `"{value-1}"`: {What the user sees / can do}
- `"{value-2}"`: {What the user sees / can do differently}

**Effect on generated configuration:**
<!-- What Spring Boot property / feature flag / env variable does this produce?
     Property key convention: {suite}.{feature-id}.{attr.path} -->
```yaml
# application-{profile}.yml (binding time: deploy)
{suite}.F-{SUITE}-{NNN}-{NN}.{attr.path}: {value}
```

**Interaction with other attributes:**
<!-- Does this attribute's behaviour depend on another attribute's value?
     E.g., "approval.requireComment has no effect when approval.workflow = 'none'." -->
- `{condition}` → {effect}

**Elara override guidance:**
<!-- When should a ProductConfig override this from the Telos default?
     Who (which role / team) is authorised to override? -->

**Examples:**

*Default configuration:*
```uvl
// In ProductConfig:
// F-{SUITE}-{NNN}-{NN} not overriding {attr.path} → uses default "{default}"
```

*Override in ProductConfig:*
```uvl
// In {product-id}.product-config.uvl:
F-{SUITE}-{NNN}-{NN} {{attr.path} "{non-default-value}"}
```

---

## {next-attribute-name}

<!-- Repeat the section structure above for each attribute
     that has a schema reference in the .uvl file. -->

---

## Change Log

| Date | Version | Author | Changes |
|---|---|---|---|
| YYYY-MM-DD | 1.0 | {Name} | Initial attribute documentation |
