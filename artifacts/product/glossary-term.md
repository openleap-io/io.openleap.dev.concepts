# Artifact: Glossary Term

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.3, §9.1 MongoDB Document Model, Freeze schema `glossary_terms`

---

## Definition

A Glossary Term defines a **business term** with its precise meaning, aliases, disambiguation, and functional area scope. Glossary Terms establish the ubiquitous language of the domain.

**KEY ROLE:** Glossary Terms **feed into Suite Spec UBL** (Ubiquitous Business Language) during decomposition — they become the authoritative vocabulary for the domain model.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Term identifier |
| term | string | ✓ | The business term (e.g., "Anmeldung") |
| definition | string | ✓ | Precise definition of this term |
| aliases | string[] | | Alternative names for this term |
| examples | string[] | | Concrete usage examples |
| relatedTerms | ref[] | | Other GlossaryTerm IDs that are related |
| notToConfuseWith | Disambiguation[] | | Terms this must not be confused with |
| notToConfuseWith[].termRef | ref | ✓ | Reference to the confusable term |
| notToConfuseWith[].explanation | string | ✓ | Why they are different |
| functionalAreaRef | ref | | Which Functional Area this belongs to |

---

## Constraints

1. A GlossaryTerm MUST have a definition (not just the term)
2. notToConfuseWith entries MUST include an explanation

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| references | GlossaryTerm | relatedTerms |
| references | GlossaryTerm | notToConfuseWith[].termRef |
| references | FunctionalArea | functionalAreaRef |
| referenced by | ProcessStep | step.linkedTerms |
| referenced by | BusinessObject | term DESCRIBES object (Knowledge Graph) |
| feeds into | Suite Spec UBL | Ubiquitous Business Language for domain model |

---

## Existing Sources

- Elara Spec §4.2 Cat.3 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema `glossary_terms` ✅
- Status: **Fully defined, no gaps**
