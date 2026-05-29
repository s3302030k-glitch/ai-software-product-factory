# 09 — API Design

> Defines all API endpoints, request/response contracts, validation, error handling, and authorization rules.

---

## Purpose

Provide a complete API reference that frontend developers, backend developers, and AI agents use to build and consume the API. Every endpoint, its shape, validation, errors, and auth rules are specified here.

## Status

`Draft` | `In Progress` | `Complete`

---

## API Conventions

| Convention | Value |
|-----------|-------|
| **Base URL** | _e.g., `/api/v1`_ |
| **Format** | JSON |
| **Authentication** | _e.g., Bearer token / Cookie session_ |
| **Naming** | `kebab-case` for URLs, `camelCase` for JSON fields |
| **Pagination** | _e.g., `?page=1&limit=20`_ |
| **Versioning** | _URL prefix (`/v1/`) or header-based_ |

### Standard Response Envelope

```json
// Success
{
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 100
  }
}

// Error
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      { "field": "email", "message": "Email is required" }
    ]
  }
}
```

### Standard HTTP Status Codes

| Code | Meaning | When Used |
|------|---------|-----------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST that creates a resource |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Malformed request |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Authenticated but not authorized |
| 404 | Not Found | Resource does not exist |
| 409 | Conflict | Duplicate or state conflict |
| 422 | Unprocessable Entity | Validation failure |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Unexpected server error |

---

## Endpoint Template

_Copy this template for each endpoint._

---

### `METHOD /path/to/resource`

**Description:** _What this endpoint does_
**Auth Required:** Yes / No
**Roles Allowed:** _e.g., Admin, Manager_
**Rate Limit:** _e.g., 100 requests/minute_

#### Request

**Path Parameters:**

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | `uuid` | Yes | _Resource identifier_ |

**Query Parameters:**

| Param | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `page` | `integer` | No | `1` | _Page number_ |
| `limit` | `integer` | No | `20` | _Items per page (max 100)_ |
| `sort` | `string` | No | `created_at` | _Sort field_ |
| `order` | `string` | No | `desc` | _Sort direction: asc/desc_ |

**Request Body:**

```json
{
  "name": "string (required, max 100)",
  "email": "string (required, valid email, unique)",
  "status": "string (optional, enum: active|inactive, default: active)"
}
```

#### Validation Rules

| Field | Rule | Error Code |
|-------|------|-----------|
| `name` | Required, 1-100 characters | `VALIDATION_ERROR` |
| `email` | Required, valid email format, unique | `VALIDATION_ERROR` / `CONFLICT` |
| `status` | If provided, must be valid enum | `VALIDATION_ERROR` |

#### Response

**Success (200/201):**

```json
{
  "data": {
    "id": "uuid",
    "name": "string",
    "email": "string",
    "status": "string",
    "createdAt": "ISO 8601 datetime",
    "updatedAt": "ISO 8601 datetime"
  }
}
```

**Error (422):**

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      { "field": "name", "message": "Name is required" }
    ]
  }
}
```

#### Authorization

_How is access control enforced for this endpoint?_

- Admin: Full access
- Manager: Access to own team's resources only
- User: Access to own resources only
- Unauthenticated: 401

#### Error Handling

| Scenario | Status | Error Code | Message |
|---------|--------|-----------|---------|
| Resource not found | 404 | `NOT_FOUND` | "Resource not found" |
| Duplicate entry | 409 | `CONFLICT` | "A resource with this email already exists" |
| Unauthorized | 401 | `UNAUTHORIZED` | "Authentication required" |
| Forbidden | 403 | `FORBIDDEN` | "You do not have permission to perform this action" |

---

## Endpoint Index

_List all endpoints for quick reference._

| Method | Path | Description | Auth | Priority |
|--------|------|-------------|------|----------|
| GET | `/api/v1/resources` | List resources | Yes | Must Have |
| POST | `/api/v1/resources` | Create resource | Yes | Must Have |
| GET | `/api/v1/resources/:id` | Get resource by ID | Yes | Must Have |
| PUT | `/api/v1/resources/:id` | Update resource | Yes | Must Have |
| DELETE | `/api/v1/resources/:id` | Delete resource | Yes | Must Have |

---

## Versioning Notes

_How will API versioning be handled?_

1. **Current version:** `v1`
2. **Versioning strategy:** _URL prefix (`/api/v1/`) recommended for clarity_
3. **Breaking change policy:** _Breaking changes require a new version; old versions supported for N months_
4. **Deprecation process:** _Add `Sunset` header, update docs, notify consumers_

---

## Scope

- This document defines **API contracts** — the interface between frontend and backend.
- Implementations must match these contracts exactly.

## Out of Scope

- Database query implementation
- Frontend component implementation
- Third-party API integrations (document separately)

## Guardrails

- [ ] Every endpoint must have validation rules defined
- [ ] Every endpoint must have error responses documented
- [ ] Authorization must be specified for every endpoint
- [ ] AI agents must not create undocumented endpoints
- [ ] Response shapes must match the documented contracts

## Related Files

- `04-user-roles.md` — Roles referenced in authorization rules
- `06-pages-spec.md` — Pages that consume these endpoints
- `07-data-model.md` — Data structures returned by these endpoints
- `10-security-model.md` — Security requirements for API layer

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
