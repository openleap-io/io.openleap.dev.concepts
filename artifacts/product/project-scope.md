# Artifact: Project Scope

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.1, Freeze schema `project_scope`

---

## Definition

The strategic context for a product — why it exists, what it aims to achieve, and what is in/out of bounds.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| businessProblem | string | ✓ | Why does this product exist? What pain is being solved? |
| goals | Goal[] | ✓ | Measurable objectives (min. 1) |
| goals[].description | string | ✓ | What should be achieved |
| goals[].successCriteria | string | | How to measure success |
| goals[].priority | enum | ✓ | high, medium, low |
| inScope | string[] | ✓ | What is included in this product |
| outOfScope | string[] | | What is explicitly excluded |
| constraints | Constraint[] | | Limits on the solution space |
| constraints[].description | string | ✓ | What the constraint is |
| constraints[].type | enum | | technology, budget, regulatory, organizational |
| constraints[].impact | string | | How it affects the product |
| assumptions | string[] | | What we're taking for granted |

---

## Constraints

1. businessProblem MUST be at least 50 characters (meaningful description, not a placeholder)
2. At least one goal with priority

---

## Existing Sources

- Elara Spec §4.1 ✅
- Freeze schema `project_scope` ✅
- Status: **Fully defined, no gaps**
