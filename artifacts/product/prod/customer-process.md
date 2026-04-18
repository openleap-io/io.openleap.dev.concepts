# Artifact: Customer BPMN Process (`processes/{id}.bpmn`)

> **Space:** Product (Application Engineering)
> **Lives in:** `io.openleap.prod.{product}/processes/{id}.bpmn`
> **Template:** [TPL-PROD-PROCESS](../../../templates/product/prod/processes/process.bpmn)
> **Example:** [acme-sales/processes/order-to-cash.bpmn](../../../examples/product/acme-sales/processes/order-to-cash.bpmn)
> **In-Elara counterpart:** [`artifacts/product/business-process.md`](../business-process.md)

---

## Definition

A Customer BPMN Process is the Git-persisted BPMN 2.0 XML export of a business
process discovered in Elara. It drives feature selection: its task nodes can
carry OpenLeap-specific hints (`ol:featureHints`) that trace each task to a
platform feature.

The format is standard **OMG BPMN 2.0** (http://www.omg.org/spec/BPMN/2.0/).
OpenLeap adds a small number of extension attributes and elements under the
`ol:` namespace — none of which are required to open the file in a standard
BPMN editor.

---

## Fields (XML structure)

| Element | Required | Description |
|---------|----------|-------------|
| `<bpmn:definitions>` | ✓ | Root; declares namespaces including `xmlns:ol="https://openleap.io/spec/product/bpmn-extensions"` |
| `<bpmn:process id="{id}" name="..." ol:productRef="..." ol:level="L0|L1|L2">` | ✓ | Single process per file |
| `<bpmn:documentation>` | ✓ | One or two sentences; links to fulfilled capabilities |
| `<bpmn:extensionElements><ol:processMetadata>` | ✓ | OpenLeap metadata block |
| `<ol:triggerType>` | ✓ | `user_action` \| `business_event` \| `timer` \| `external_signal` |
| `<ol:frequency>`, `<ol:sla>`, `<ol:outcome>` | | Free-form operational hints |
| `<ol:fulfilsCapabilities>` | ✓ | One or more `<ol:capabilityRef>` |
| `<ol:featureHints>` | | Optional `<ol:hint taskId="..." featureId="..."/>` elements |
| Flow elements (events, tasks, gateways, flows) | ✓ | Authored in a BPMN editor |
| `<bpmndi:BPMNDiagram>` | ✓ | Layout — populated by the BPMN editor |

---

## Constraints

1. File name stem MUST equal `<bpmn:process id>`.
2. `ol:productRef` MUST equal `metadata.id` in `product.yaml`.
3. `ol:level` MUST be one of `L0`, `L1`, `L2` (see `business-process.md`).
4. Every `ol:hint.featureId` MUST appear in `features/selection.uvl`. Decompose validates this.
5. BPMN XML MUST parse under the BPMN 2.0 schema; standard validation applies.

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| fulfils | BusinessCapability | `ol:capabilityRef` |
| hints at | Platform Feature | `ol:featureHints` |
| referenced by | Product Manifest | `processes[].file` |
| visually representable | — | Any BPMN 2.0 editor (Camunda, bpmn.io) |

---

## Existing Sources

| Source | Location | Status |
|--------|----------|--------|
| BPMN 2.0 specification | OMG formal/2011-01-03 | ✅ external standard |
| In-Elara BusinessProcess | `artifacts/product/business-process.md` | ✅ source of steps/gateways |
