# Data Model

## Entity Relationship Overview

[Describe the main entities and how they relate to each other. Use plain language — this is for humans, not just the database.]

## Entities

### [Entity Name]

**What it represents**: [Plain-language description]

**Key fields**:

| Field | Type | Required | Description | Example |
|---|---|---|---|---|
| id | UUID | yes | Unique identifier | `a1b2c3d4-...` |
| [field_name] | [string/int/bool/date/ref] | yes/no | [What it stores] | [Example value] |

**Relationships**:
- Belongs to [Entity] via [field]
- Has many [Entity] via [field]

**Constraints**:
- [Constraint 1, e.g., "name must be unique within the parent scope"]
- [Constraint 2]

---

### [Next Entity]

...
