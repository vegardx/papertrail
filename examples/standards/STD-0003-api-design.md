---
status: approved
date: 2024-03-01
applies-to: services
authors:
  - "@platform-team"
  - "@api-guild"
reviewers:
  - "@architecture-review"
  - "@security-team"
gitignore: []
---

# STD-0003: API Design

## Overview

This standard defines requirements for designing REST APIs. It ensures consistency across services, enabling easier integration, better documentation, and predictable behavior for API consumers.

All services exposing HTTP APIs MUST follow this standard.

## Scope

### Applies To

* REST APIs (HTTP/JSON)
* Public and internal APIs
* Service-to-service communication

### Does Not Apply To

* gRPC APIs (see STD-0006)
* GraphQL APIs (see STD-0007)
* WebSocket APIs
* File upload/download endpoints (partial applicability)

## Standard

### URL Structure

#### MUST

* Use lowercase letters and hyphens for URL paths
* Use plural nouns for resource collections (`/users`, not `/user`)
* Use resource identifiers in paths (`/users/{id}`)
* Version APIs in the URL path (`/api/v1/...`)

#### SHOULD

* Nest resources to show relationships (`/users/{id}/orders`)
* Limit nesting to 2 levels maximum
* Use query parameters for filtering, sorting, pagination

#### MUST NOT

* Use verbs in URLs (use HTTP methods instead)
* Use trailing slashes
* Expose database IDs directly (use UUIDs or prefixed IDs)

### HTTP Methods

#### MUST

* Use methods according to their semantics:

| Method | Purpose | Idempotent | Safe |
|--------|---------|------------|------|
| GET | Retrieve resource(s) | Yes | Yes |
| POST | Create resource | No | No |
| PUT | Replace resource entirely | Yes | No |
| PATCH | Partial update | Yes | No |
| DELETE | Remove resource | Yes | No |

* Return appropriate status codes (see below)
* Support `OPTIONS` for CORS preflight

#### SHOULD

* Use `HEAD` for existence checks
* Implement conditional requests (`If-None-Match`, `If-Modified-Since`)

### Status Codes

#### MUST

* Use standard HTTP status codes:

| Code | Usage |
|------|-------|
| 200 | Successful GET, PUT, PATCH, DELETE |
| 201 | Successful POST (resource created) |
| 204 | Successful DELETE (no content) |
| 400 | Invalid request (validation error) |
| 401 | Authentication required |
| 403 | Authenticated but not authorized |
| 404 | Resource not found |
| 409 | Conflict (duplicate, version mismatch) |
| 422 | Unprocessable entity (semantic error) |
| 429 | Rate limit exceeded |
| 500 | Internal server error |
| 503 | Service unavailable |

* Never return 200 for errors
* Include `Location` header for 201 responses

### Request Format

#### MUST

* Accept `application/json` content type
* Validate all input data
* Return 400 for malformed JSON
* Return 422 for valid JSON with semantic errors

#### SHOULD

* Support `application/json` charset UTF-8
* Ignore unknown fields (forward compatibility)
* Trim whitespace from string inputs

### Response Format

#### MUST

* Return `application/json` content type
* Use consistent field naming (camelCase)
* Include resource `id` in all responses
* Use ISO 8601 for dates (`2024-03-01T10:30:00Z`)
* Use consistent envelope for single resources:
  ```json
  {
    "id": "usr_abc123",
    "email": "user@example.com",
    "createdAt": "2024-03-01T10:30:00Z"
  }
  ```

#### SHOULD

* Include `createdAt` and `updatedAt` timestamps
* Use prefixed IDs (`usr_`, `ord_`, `inv_`) for clarity
* Return the created/updated resource in responses

### Error Responses

#### MUST

* Use consistent error format:
  ```json
  {
    "error": {
      "code": "VALIDATION_ERROR",
      "message": "The request contains invalid data",
      "details": [
        {
          "field": "email",
          "code": "INVALID_FORMAT",
          "message": "Must be a valid email address"
        }
      ]
    }
  }
  ```
* Include machine-readable `code` field
* Include human-readable `message` field
* Include `details` array for validation errors

#### MUST NOT

* Expose stack traces in production
* Reveal internal implementation details
* Distinguish between "user not found" and "wrong password" (security)

### Pagination

#### MUST

* Paginate all list endpoints
* Use cursor-based pagination for large datasets:
  ```json
  {
    "data": [...],
    "pagination": {
      "hasMore": true,
      "cursor": "eyJpZCI6MTAwfQ=="
    }
  }
  ```
* Limit default page size (max 100)

#### SHOULD

* Support `limit` query parameter
* Support `cursor` query parameter
* Include total count only when explicitly requested (`?includeCount=true`)

### Filtering & Sorting

#### SHOULD

* Use query parameters for filtering: `?status=active&createdAfter=2024-01-01`
* Use `sort` parameter with field names: `?sort=createdAt` or `?sort=-createdAt` (descending)
* Support multiple sort fields: `?sort=-priority,createdAt`

#### MAY

* Support complex filters with operators: `?price[gte]=100&price[lt]=500`
* Support field selection: `?fields=id,name,email`

### Headers

#### MUST

* Include `X-Request-Id` in all responses (for tracing)
* Support `Authorization: Bearer <token>` for authentication
* Return proper CORS headers for browser clients

#### SHOULD

* Support `Accept-Language` for localized messages
* Include `X-RateLimit-*` headers:
  - `X-RateLimit-Limit`
  - `X-RateLimit-Remaining`
  - `X-RateLimit-Reset`

### Versioning

#### MUST

* Version APIs from the start (`/api/v1/`)
* Maintain backward compatibility within a major version
* Document breaking changes in changelog
* Support previous version for minimum 6 months after deprecation

#### SHOULD

* Use deprecation headers: `Deprecation: true`, `Sunset: <date>`
* Provide migration guides for version upgrades

### Security

#### MUST

* Use HTTPS only (no HTTP)
* Validate and sanitize all input
* Implement rate limiting
* Use secure authentication (JWT, OAuth 2.0)
* Never log sensitive data (passwords, tokens, PII)

#### SHOULD

* Implement request signing for sensitive operations
* Use short-lived access tokens with refresh tokens
* Audit log all write operations

## Examples

### Compliant Example

```http
POST /api/v1/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...

{
  "email": "newuser@example.com",
  "name": "Jane Doe"
}
```

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/v1/users/usr_xyz789
X-Request-Id: req_abc123

{
  "id": "usr_xyz789",
  "email": "newuser@example.com",
  "name": "Jane Doe",
  "createdAt": "2024-03-01T10:30:00Z",
  "updatedAt": "2024-03-01T10:30:00Z"
}
```

### Non-Compliant Example

```http
POST /api/createUser HTTP/1.1

{
  "user_email": "test@example.com"
}
```

```http
HTTP/1.1 200 OK

{
  "success": true,
  "data": {
    "ID": 12345,
    "user_email": "test@example.com",
    "created": "March 1, 2024"
  }
}
```

**Why this violates the standard:**
- URL contains verb `createUser` (MUST use noun and POST method)
- Missing version prefix `/api/v1/`
- Returns 200 for creation (MUST return 201)
- Uses `success` wrapper (MUST use direct response)
- Exposes database ID (MUST use prefixed ID)
- Inconsistent field naming (snake_case vs camelCase)
- Non-standard date format (MUST use ISO 8601)

## Enforcement

* **Automated checks**:
  - OpenAPI spec validation in CI
  - Contract testing with consumer-driven contracts
  - Response schema validation in integration tests
  - Spectral linting for OpenAPI specs

* **Manual review**:
  - API design review before implementation
  - Security review for authentication/authorization

* **Exceptions process**:
  - Document rationale in Proposal or PR description
  - Requires @api-guild approval

## Tooling Integration

### Linter Configuration

```yaml
# .spectral.yml
extends: spectral:oas
rules:
  operation-operationId: error
  operation-description: warn
  oas3-api-servers: error
  info-contact: warn
  
  # Custom rules
  path-must-be-kebab-case:
    given: "$.paths[*]~"
    then:
      function: pattern
      functionOptions:
        match: "^(/[a-z0-9-]+)+$"
```

### CI/CD Integration

```yaml
# .github/workflows/api-lint.yml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Lint OpenAPI spec
        uses: stoplightio/spectral-action@v0.8.10
        with:
          file_glob: 'api/*.yaml'
          
      - name: Validate spec
        run: npx @redocly/cli lint api/openapi.yaml
```

### IDE Setup

* **VS Code**: Install OpenAPI (Swagger) Editor extension
* **Stoplight Studio**: For visual API design

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2024-03-01 | @api-guild | Initial version |

## Rationale

* **Consistent structure**: Reduces cognitive load for API consumers
* **Prefixed IDs**: Prevents confusion between resource types, improves debugging
* **Cursor pagination**: Performs better than offset for large datasets
* **Strict error format**: Enables automated error handling in clients

## References

* [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines)
* [Google API Design Guide](https://cloud.google.com/apis/design)
* [JSON:API Specification](https://jsonapi.org/)
* [RFC 7807 - Problem Details for HTTP APIs](https://tools.ietf.org/html/rfc7807)
