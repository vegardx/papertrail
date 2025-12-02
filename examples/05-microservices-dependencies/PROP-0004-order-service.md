---
status: approved
date: 2024-05-15
authors:
  - "@commerce-team"
reviewers:
  - "@platform-team"
  - "@architecture-review"
---

# PROP-0004: Order Service

## Problem Statement

Our e-commerce platform needs order management capabilities:
- Create and track orders through their lifecycle
- Associate orders with user accounts
- Emit events for downstream processing (notifications, analytics)

Currently there's no order management—we're building greenfield. The Order Service needs to integrate with the User Service (SPEC-0002) to validate users before creating orders.

## Goals

- REST API for order CRUD operations
- Order lifecycle management (created → paid → shipped → delivered)
- User validation via User Service integration
- Event emission for order state changes
- Foundation for payment integration (separate spec)

## Non-Goals

- Payment processing (separate service/spec)
- Inventory management
- Shipping logistics
- Order analytics dashboard

## Background

This is the second service in our e-commerce platform:
1. User Service (SPEC-0002) - ✅ In progress
2. **Order Service** - This proposal
3. Notification Service - Future
4. Payment Service - Future

The Order Service will be the first service to depend on another, establishing patterns for service-to-service communication.

## Proposed Direction

### Option 1: Go Service with Event Sourcing (Recommended)

Build a Go service that uses event sourcing for order state.

**Pros:**
- Full audit trail of all order changes
- Easy to add new consumers (notifications, analytics)
- Aligns with Go standard
- Natural fit for order lifecycle

**Cons:**
- More complex than simple CRUD
- Team needs to learn event sourcing patterns

### Option 2: Go Service with Traditional CRUD

Build a simpler Go service with PostgreSQL and traditional CRUD.

**Pros:**
- Simpler to implement
- Team is familiar with pattern

**Cons:**
- Harder to track order history
- Need separate audit logging
- Less flexible for adding consumers

## Recommendation

**Option 1: Go Service with Event Sourcing**

Orders have a natural lifecycle that maps well to events:
- OrderCreated
- OrderPaid
- OrderShipped
- OrderDelivered
- OrderCancelled

Event sourcing gives us audit trail for free and makes it easy to add notification and analytics consumers later.

## Next Steps

1. Create technical specification
2. Design API contract and event schemas
3. Implement User Service client for validation
4. Build and test with User Service in staging
