# Artifact: Business Rule

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.3, §9.1 MongoDB Document Model, Freeze schema `business_rules`

---

## Definition

A Business Rule is a **constraint or policy** that governs business behavior — with type classification, rationale, legal or domain source, examples, and exceptions. Rules are referenced from process steps and govern capabilities.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Rule identifier |
| name | string | ✓ | Rule name (e.g., "Mindestalter 17 für B17") |
| description | string | ✓ | What this rule says |
| type | enum | ✓ | invariant, calculation, validation, authorization, timing |
| rationale | string | | Why this rule exists |
| source | string | | Legal or domain source (e.g., "§10 FeV", "GDPR Article 32") |
| examples | string[] | | Concrete examples of the rule in action |
| exceptions | string[] | | Known exceptions to the rule |
| appliesToProcesses | ref[] | | Process IDs where this rule applies |
| governsCapabilities | ref[] | | BusinessCapability IDs this rule governs |

---

## Constraints

1. A BusinessRule MUST have a type classification
2. A BusinessRule SHOULD have a rationale or source

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | BusinessProcess | appliesToProcesses |
| references | BusinessCapability | governsCapabilities |
| referenced by | ProcessStep | step.linkedRules |
| referenced by | BusinessCapability | capability.governedByRules |

---

## Existing Sources

- Elara Spec §4.2 Cat.3 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema `business_rules` ✅
- Status: **Fully defined, no gaps**
