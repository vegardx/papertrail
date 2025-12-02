---
status: approved
date: 2024-05-08
implements: PROP-0003
implementations:
  - repo: github/user-service
    status: in-progress
authors:
  - "@platform-team"
reviewers:
  - "@security-team"
---

# SPEC-0002: User Service

## Overview

This specification defines the requirements for a centralized user management service that provides REST APIs for user operations and JWT-based authentication.

**Implements:** [PROP-0003: User Service](../../proposals/PROP-0003-user-service.md)

## Requirements

### Requirement: User Management API

The service SHALL provide a REST API for user management operations.

#### Scenario: Create user

- **GIVEN** a valid user creation request with email and password
- **WHEN** POST /api/v1/users is called
- **THEN** a new user is created and returned with id, email, and created_at (password not returned)

#### Scenario: Get user by ID

- **GIVEN** a user exists with ID "user123"
- **WHEN** GET /api/v1/users/user123 is called with valid authentication
- **THEN** the user object is returned with id, email, name, and created_at

#### Scenario: Update user

- **GIVEN** a user exists with ID "user123"
- **WHEN** PATCH /api/v1/users/user123 is called with updated fields
- **THEN** the user is updated and the new state is returned

#### Scenario: User not found

- **GIVEN** no user exists with ID "nonexistent"
- **WHEN** GET /api/v1/users/nonexistent is called
- **THEN** 404 Not Found is returned with error details

### Requirement: JWT Authentication

The service SHALL issue and validate JWT tokens for authentication.

#### Scenario: Login and token issuance

- **GIVEN** a user exists with email "user@example.com" and correct password
- **WHEN** POST /api/v1/auth/login is called with credentials
- **THEN** a JWT access token and refresh token are returned

#### Scenario: Token validation

- **GIVEN** a valid JWT token issued by this service
- **WHEN** GET /api/v1/auth/validate is called with the token
- **THEN** the token claims (user_id, email, exp) are returned

#### Scenario: Invalid credentials

- **GIVEN** a user exists with email "user@example.com"
- **WHEN** POST /api/v1/auth/login is called with wrong password
- **THEN** 401 Unauthorized is returned (no indication if email exists)

#### Scenario: Token refresh

- **GIVEN** a valid refresh token
- **WHEN** POST /api/v1/auth/refresh is called
- **THEN** a new access token is returned (refresh token rotated)

### Requirement: Service Health

The service SHALL expose health and readiness endpoints.

#### Scenario: Health check

- **GIVEN** the service is running
- **WHEN** GET /health is called
- **THEN** 200 OK is returned with service status

#### Scenario: Readiness with database

- **GIVEN** the database connection is healthy
- **WHEN** GET /ready is called
- **THEN** 200 OK is returned

## Interfaces

### API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | /api/v1/users | Create user |
| GET | /api/v1/users/:id | Get user by ID |
| PATCH | /api/v1/users/:id | Update user |
| DELETE | /api/v1/users/:id | Delete user |
| POST | /api/v1/auth/login | Login, get tokens |
| POST | /api/v1/auth/refresh | Refresh access token |
| GET | /api/v1/auth/validate | Validate token |
| GET | /health | Health check |
| GET | /ready | Readiness check |

### User Object

```json
{
  "id": "usr_abc123",
  "email": "user@example.com",
  "name": "Jane Doe",
  "created_at": "2024-05-01T10:00:00Z",
  "updated_at": "2024-05-01T10:00:00Z"
}
```

### JWT Claims

```json
{
  "sub": "usr_abc123",
  "email": "user@example.com",
  "iat": 1714560000,
  "exp": 1714563600
}
```

### Error Response

```json
{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "User with ID 'nonexistent' not found"
  }
}
```

## Security Considerations

- Passwords must be hashed with bcrypt (cost factor 12+)
- JWT signing key must be stored in secrets manager
- Rate limiting on auth endpoints (10 req/min per IP)
- All endpoints require HTTPS
- Failed login attempts must not reveal if email exists

## Spec Dependencies

None - this is the foundation service.
