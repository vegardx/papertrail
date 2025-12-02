---
status: approved
date: 2024-05-22
implements: PROP-0004
implementations:
  - repo: github/order-service
    status: planned
authors:
  - "@commerce-team"
reviewers:
  - "@platform-team"
---

# SPEC-0003: Order Service

## Overview

This specification defines the requirements for an order management service that handles order lifecycle and emits events for downstream processing.

**Implements:** [PROP-0004: Order Service](../../proposals/PROP-0004-order-service.md)

## Requirements

### Requirement: User Validation

The service SHALL validate that a user exists before creating an order.

#### Scenario: Valid user

- **GIVEN** a request to create an order with user_id "usr_abc123"
- **WHEN** POST /api/v1/orders is called
- **THEN** the service calls User Service GET /api/v1/users/usr_abc123 to verify the user exists before creating the order

#### Scenario: Invalid user

- **GIVEN** a request to create an order with user_id "usr_nonexistent"
- **WHEN** POST /api/v1/orders is called
- **THEN** the service returns 400 Bad Request with error "User not found"

#### Scenario: User Service unavailable

- **GIVEN** the User Service is unavailable
- **WHEN** POST /api/v1/orders is called
- **THEN** the service returns 503 Service Unavailable with error "Unable to validate user"

### Requirement: Order Lifecycle

The service SHALL manage orders through their lifecycle states.

#### Scenario: Create order

- **GIVEN** a valid user and order items
- **WHEN** POST /api/v1/orders is called
- **THEN** an order is created in "pending" state and an OrderCreated event is emitted

#### Scenario: Order state transition

- **GIVEN** an order in "pending" state
- **WHEN** PATCH /api/v1/orders/:id with status "paid" is called
- **THEN** the order transitions to "paid" and an OrderPaid event is emitted

#### Scenario: Invalid state transition

- **GIVEN** an order in "delivered" state
- **WHEN** PATCH /api/v1/orders/:id with status "pending" is called
- **THEN** 400 Bad Request is returned (cannot go backwards)

### Requirement: Event Emission

The service SHALL emit events for all order state changes.

#### Scenario: Event structure

- **GIVEN** an order state change occurs
- **WHEN** the change is persisted
- **THEN** an event is published to the message bus with order_id, event_type, timestamp, and full order state

#### Scenario: Event ordering

- **GIVEN** multiple state changes on an order
- **WHEN** events are consumed
- **THEN** events are ordered by sequence number within each order

## Interfaces

### API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | /api/v1/orders | Create order |
| GET | /api/v1/orders/:id | Get order |
| GET | /api/v1/orders?user_id=X | List orders for user |
| PATCH | /api/v1/orders/:id | Update order state |
| DELETE | /api/v1/orders/:id | Cancel order |

### Order Object

```json
{
  "id": "ord_xyz789",
  "user_id": "usr_abc123",
  "status": "pending",
  "items": [
    {
      "product_id": "prod_123",
      "quantity": 2,
      "unit_price": 29.99
    }
  ],
  "total": 59.98,
  "created_at": "2024-05-22T10:00:00Z",
  "updated_at": "2024-05-22T10:00:00Z"
}
```

### Order States

```
pending → paid → shipped → delivered
    ↓        ↓       ↓
 cancelled cancelled cancelled
```

### Event Schema

```json
{
  "event_id": "evt_abc123",
  "event_type": "order.created",
  "timestamp": "2024-05-22T10:00:00Z",
  "sequence": 1,
  "data": {
    "order_id": "ord_xyz789",
    "user_id": "usr_abc123",
    "status": "pending",
    "total": 59.98
  }
}
```

### Event Types

| Event | Description |
|-------|-------------|
| order.created | New order created |
| order.paid | Payment confirmed |
| order.shipped | Order shipped |
| order.delivered | Order delivered |
| order.cancelled | Order cancelled |

## Security Considerations

- User validation must use service-to-service JWT
- Orders can only be accessed by the owning user or admin
- Event bus requires authentication
- Rate limiting on order creation (prevent abuse)

## Spec Dependencies

| Spec | Purpose | Implementation |
|------|---------|----------------|
| SPEC-0002 | User validation API | github/user-service |

When implementing, reference `.contexts/user-service/` for:
- API contract (OpenAPI spec)
- JWT validation approach
- Error response formats
