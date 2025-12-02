---
status: approved
date: 2024-05-01
authors:
  - "@platform-team"
reviewers:
  - "@security-team"
  - "@architecture-review"
---

# PROP-0003: User Service

## Problem Statement

User data is currently duplicated across multiple legacy systems:
- Marketing database has user profiles
- Billing system has payment accounts
- Support portal has customer records

This causes:
- Data inconsistencies (user updates in one system don't propagate)
- Difficulty implementing SSO (no single authentication source)
- Complex integrations for new services

We need a centralized user management service as the foundation for our new e-commerce platform.

## Goals

- Single source of truth for user data
- REST API for user CRUD operations
- JWT-based authentication for service-to-service calls
- Foundation for future SSO integration

## Non-Goals

- Authorization/permissions (will be a separate service)
- User analytics and behavior tracking
- Legacy system migration (separate project with own timeline)
- Social login integration (future work)

## Background

Current state:
- 3 separate user databases with ~500K users combined
- ~15% of users have inconsistent data across systems
- New service integrations take 2-3 weeks due to auth complexity

The e-commerce platform will be built as microservices, with User Service as the foundation that other services depend on.

## Proposed Direction

### Option 1: Build New Go Service (Recommended)

Build a new service in Go with PostgreSQL backend.

**Pros:**
- Clean slate, no legacy constraints
- Go is our standard for new services
- Can design API for modern needs
- Strong typing and performance

**Cons:**
- Build from scratch
- Need to handle user migration separately

### Option 2: Extract from Existing System

Refactor the billing system's user management into a service.

**Pros:**
- Already has most user data
- Less migration work

**Cons:**
- Carries legacy technical debt
- Billing system codebase is Java (not our standard)
- Tightly coupled to billing logic

## Recommendation

**Option 1: Build New Go Service**

Starting fresh allows us to:
- Design a clean API that all new services can depend on
- Follow our current engineering standards
- Avoid inheriting legacy complexity

The migration of existing users will be handled as a separate workstream.

## Next Steps

1. Create technical specification
2. Design API contract (OpenAPI)
3. Security review of JWT implementation
4. Build and deploy to staging
5. Migrate existing users (separate project)
