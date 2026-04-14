# Artifact: External Interface

> **Space:** Product
> **Embedded in:** [Product](product.md)
> **Source:** Elara Spec §4.2 Cat.4, §9.1 MongoDB Document Model

---

## Definition

An External Interface represents a **system, organization, or channel** that the business interacts with — everything outside the product boundary. It captures who or what is on the other side, the type of interaction, and the direction of communication.

---

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | ✓ | Interface identifier |
| name | string | ✓ | Interface name (e.g., "Fahrerlaubnisbehörde") |
| organization | string | | Organization or system behind the interface |
| type | enum | ✓ | system, agency, channel, service |
| direction | enum | ✓ | inbound, outbound, bidirectional |
| description | string | ✓ | What this interface does and what data flows through it |

---

## Constraints

1. An ExternalInterface MUST have a type
2. An ExternalInterface MUST have a direction

---

## Relationships

| Direction | Target | Via |
|-----------|--------|-----|
| referenced by | ProcessStep | step.linkedInterfaces |
| referenced by | Product | product.externalInterfaces |

---

## Existing Sources

- Elara Spec §4.2 Cat.4 ✅
- Elara Spec §9.1 MongoDB Document Model ✅
- Freeze schema: ⚠️ **NOT in Freeze schema — gap**
