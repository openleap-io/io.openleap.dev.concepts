# Example: `io.openleap.prod.acme-sales`

> **Illustrates:** the complete `prod.*` repo convention documented in
> [dev.hub architecture/product-repo-layout.md](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/product-repo-layout.md).
>
> **Fictional customer:** ACME Manufacturing — a mid-size industrial supplier
> with a desktop-first sales workbench requirement.
>
> **Suites drawn from:** SD (Sales & Distribution), BP (Business Partner),
> FI (Financial Accounting), PPS (Production Planning & Scheduling).
>
> **Templates used:** see [templates/product/prod/](../../../templates/product/prod/).

---

## What this example shows

| Artifact | File | Illustrates |
|----------|------|-------------|
| Product manifest | [product.yaml](product.yaml) | Identity, owner, target customer, persona/process refs. |
| Feature selection | [features/selection.uvl](features/selection.uvl) | Reference-don't-copy rule; inclusion modes; cross-suite read-only for PPS. |
| Variability bindings | [variability/bindings.yaml](variability/bindings.yaml) | Attribute resolution with compile / deploy / runtime binding times. |
| Extension-point fills | [extensions/F-SD-001/](extensions/F-SD-001/) | `fields.yaml`, `rules.yaml`, `actions.yaml` filling feature-level extension points. |
| Persona | [personas/sales-rep.yaml](personas/sales-rep.yaml) | Elara-derived persona with IAM roles and device profile. |
| BPMN process | [processes/order-to-cash.bpmn](processes/order-to-cash.bpmn) | Stub pointing at BPMN 2.0 with OpenLeap extensions. |
| Screen override | [screen-overrides/F-SD-001-02.aui.yaml](screen-overrides/F-SD-001-02.aui.yaml) | Persona-gated zones and CUI picks; no rule changes. |
| BFF productconfig | [bff/productconfig.yaml](bff/productconfig.yaml) | Release artifact: active features, resolved bindings, extension routes, aggregation hints. |

---

## How the files relate

```
product.yaml ── references ──► features/selection.uvl       (WHAT to ship)
             ── references ──► variability/bindings.yaml    (HOW it's configured)
             ── references ──► extensions/{F-ID}/*.yaml     (WHAT to add at hooks)
             ── references ──► personas/*.yaml              (WHO uses it)
             ── references ──► processes/*.bpmn             (WHY — process support)
             ── references ──► screen-overrides/*.aui.yaml  (HOW screens look)
                                                │
                                                ▼
                               CI packaging ──► bff/productconfig.yaml  (release)
```

Decompose validates all of the above against the dev.spec catalog, then CI
publishes `productconfig.yaml` as the versioned BFF input.

---

## Feature-ID consistency note

The feature IDs used below (`F-SD-001`, `F-SD-001-02`, `F-BP-001`, `F-BP-001-02`,
`F-PPS-010`, `F-FI-007`) follow the `F-{SUITE}-{NNN}` / `F-{SUITE}-{NNN}-{NN}`
convention from [sple-platform-concept.md §2.3](https://github.com/openleap-io/io.openleap.dev.hub/blob/main/architecture/sple-platform-concept.md).
They are illustrative — a real product would pin them to a specific dev.spec
catalog version.
