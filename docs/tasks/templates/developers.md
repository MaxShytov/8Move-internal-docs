# Template: Developer Documentation

Technical documentation for developers working on the codebase.

**Location:** `docs/architecture/django-backend/workflows/[feature-name].md`

---

## Template

```markdown
# [Feature Name] — Technical Documentation

**Created:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD
**Author:** [Name]

## Overview

[2-3 sentences: what this feature does and why it exists]

## Architecture

### Data Model

` ``mermaid
erDiagram
    ModelA ||--o{ ModelB : has
    ModelB }o--|| ModelC : belongs_to
` ``

### Models

#### [ModelName] (`app/[app_name]/models.py`)

| Field | Type | Description |
|-------|------|-------------|
| `field_name` | CharField(100) | Description |
| `status` | CharField(20) | One of: draft, active, completed |
| `related` | ForeignKey(OtherModel) | Link to [description] |

**Key methods:**
- `method_name()` — what it does
- `another_method()` — what it does

### Flow Diagram

` ``mermaid
sequenceDiagram
    participant U as User
    participant A as API
    participant S as Service
    participant D as Database

    U->>A: POST /api/endpoint/
    A->>S: process_request()
    S->>D: Create record
    D-->>S: Record created
    S-->>A: Response
    A-->>U: 201 Created
` ``

## API Endpoints

### POST `/api/[resource]/`

Create a new [resource].

**Request:**
` ``json
{
    "field_name": "value",
    "related_id": 123
}
` ``

**Response (201):**
` ``json
{
    "id": 456,
    "field_name": "value",
    "created_at": "2026-02-14T10:00:00Z"
}
` ``

**Errors:**
- `400` — Validation error (missing fields, invalid data)
- `403` — Insufficient permissions
- `404` — Related object not found

### GET `/api/[resource]/`

List [resources] with filtering.

**Query Parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `status` | string | Filter by status |
| `search` | string | Search by name |
| `page` | int | Page number |

### GET `/api/[resource]/{id}/`

Retrieve a single [resource].

### PATCH `/api/[resource]/{id}/`

Update a [resource].

### DELETE `/api/[resource]/{id}/`

Delete a [resource].

## Services / Business Logic

### `service_function_name()` (`app/[app_name]/services.py`)

[What this function does, when it's called, what it returns]

**Parameters:**
- `param1` (Type) — Description
- `param2` (Type, optional) — Description

**Returns:** [Type] — Description

**Raises:**
- `ValidationError` — when [condition]

## Permissions & Access Control

| Role | Create | Read | Update | Delete |
|------|--------|------|--------|--------|
| Platform Admin | Yes | All | All | All |
| Supplier User | Yes (own) | Own supplier | Own supplier | Own supplier |
| Client User | No | Linked suppliers | No | No |

## Database Indexes

| Index | Fields | Purpose |
|-------|--------|---------|
| `idx_[table]_status` | `status` | Filter by status |
| `idx_[table]_created` | `created_at` | Sort by date |

## Related Components

- [Related Feature](../[other-feature].md)
- [API Docs](http://localhost:8000/api/schema/swagger-ui/)

## Known Limitations

- Limitation 1: [description and workaround]
- Limitation 2: [description and when it matters]
```

---

## Guidelines

- Include Mermaid diagrams for data models and flows
- Show real request/response examples (use realistic data)
- Document all API endpoints with parameters and error codes
- List permissions per role
- Always include "Known Limitations" — even if empty, it signals completeness
- Link to Swagger for the full auto-generated API reference
