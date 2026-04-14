# Artifact: Business Process

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.1, Freeze schema `business_processes`

---

## Definition

A business process describes how the business works — step by step, with actors, decisions, data flow, and outcomes. Processes are hierarchical: L0 (overview) → L1 (sub-process) → L2 (detailed).

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Process identifier |
| name | string | ✓ | Process name |
| description | string | ✓ | What this process does |
| level | enum | ✓ | L0, L1, L2 |
| parentProcessId | ref | | For L1/L2: parent process ID |
| functionalAreaRef | ref | | Which Functional Area this belongs to |
| triggerType | enum | ✓ | user_action, business_event, timer, external_signal |
| triggerDescription | string | | What specifically triggers it |
| frequency | string | | How often (e.g., "~20/month") |
| sla | string | | Expected turnaround time |
| happyPath | string | ✓ | Narrative of the main success scenario |
| exceptions | string | | Exception scenarios narrative |
| outcome | string | ✓ | What is the business outcome |
| steps | ProcessStep[] | ✓ | Ordered activities (min. 1) |
| gateways | Gateway[] | | Decision points |

### ProcessStep (embedded)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Step identifier |
| name | string | ✓ | Step name |
| description | string | | What happens in this step |
| type | enum | ✓ | userTask, serviceTask, sendTask, receiveTask, manualTask |
| actorRef | ref | ✓ | Which Actor performs this step |
| order | integer | ✓ | Sequence position |
| inputs | DataFlow[] | | What information/objects this step needs |
| outputs | DataFlow[] | | What information/objects this step produces |
| businessObjectsRead | ref[] | | BusinessObject IDs read |
| businessObjectsWritten | ref[] | | BusinessObject IDs written |
| linkedRules | ref[] | | BusinessRule IDs applied in this step |
| linkedTerms | ref[] | | GlossaryTerm IDs used |
| linkedInterfaces | ref[] | | ExternalInterface IDs involved |

### Gateway (embedded)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Gateway identifier |
| name | string | ✓ | Gateway name (the decision question) |
| type | enum | ✓ | exclusive, parallel, inclusive, eventBased |
| afterStepId | ref | ✓ | Which step precedes this gateway |
| conditions | Condition[] | ✓ | Branches (min. 2) |
| conditions[].label | string | ✓ | Branch label |
| conditions[].expression | string | | Condition expression |
| conditions[].targetStepId | ref | ✓ | Where this branch goes |

---

## Constraints

1. L0 processes MUST NOT have a parentProcessId
2. L1/L2 processes MUST have a parentProcessId
3. Every step MUST have an actorRef
4. Gateways MUST have at least 2 conditions

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| contains | ProcessStep, Gateway | Embedded |
| references | Actor | steps[].actorRef |
| references | BusinessObject | steps[].businessObjectsRead/Written |
| references | BusinessRule | steps[].linkedRules |
| references | GlossaryTerm | steps[].linkedTerms |
| references | ExternalInterface | steps[].linkedInterfaces |
| references | BusinessEvent | Implicit: process triggers/produces events |
| referenced by | BusinessCapability | capability.fulfilledByProcesses |
| referenced by | WorkflowCandidate | candidate.discoveredFromProcess |

---

## Visual Representation

- BPMN 2.0 diagram (auto-generated from steps + gateways + actors as lanes)
- BPMN XML is stored and versioned per process

---

## Existing Sources

- Elara Spec §4.2 Cat.1 ✅
- Freeze schema `business_processes` ✅
- Status: **Fully defined, no gaps**
