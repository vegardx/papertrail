# Example 05: Microservices Dependencies

This example shows how to build a multi-service system where specs depend on each other.

## Context

The company is building a new e-commerce platform with three microservices:
1. **User Service** - Manages user accounts and authentication
2. **Order Service** - Handles orders, depends on User Service API
3. **Notification Service** - Sends notifications, depends on Order Service events

Each service has its own proposal, spec, and repository. The key concept here is **spec dependencies**: when you bootstrap the Order Service, you need access to the User Service's API definitions.

---

## Part 1: User Service (Foundation)

First, we create the foundation service that others will depend on.

### Creating the Proposal

**User:** `/propose`

**Copilot:** **What problem are you trying to solve?**

**User:** We need a centralized user management service for our e-commerce platform. Currently user data is duplicated across multiple legacy systems, causing inconsistencies and making it hard to implement features like SSO.

**Copilot:** **Title?**

**User:** User Service

**Copilot:** **Goals?**

**User:**
- Single source of truth for user data
- REST API for user CRUD operations
- JWT-based authentication
- Support for SSO integration (future)

**Copilot:** **Non-goals?**

**User:**
- Authorization/permissions (separate service)
- User analytics
- Legacy system migration (separate project)

**Copilot:** *[Continues gathering options and recommendation...]*

**User:** *[Provides details, recommends building a new Go service]*

**Copilot:** Created `decisions/proposals/PROP-0003-user-service.md`

### Creating the Spec

**User:** `/spec PROP-0003`

**Copilot:** I'll create a specification implementing PROP-0003: User Service.

**What repositories will implement this spec?**

**User:** github/user-service

**Copilot:** **What's the first requirement?**

**User:** The service shall provide a REST API for user management.

**Copilot:** *[Gathers scenarios for user CRUD operations...]*

**User:** *[Provides scenarios for create, read, update, delete users]*

**Copilot:** **What's the next requirement?**

**User:** The service shall issue and validate JWT tokens for authentication.

**Copilot:** *[Gathers scenarios for JWT handling...]*

**User:** *[Provides scenarios for token issuance and validation]*

**Copilot:** **Does this spec depend on any other specs?**

**User:** No, this is the foundation service.

**Copilot:** Created `decisions/specs/SPEC-0002-user-service.md`

### Bootstrapping User Service

**User:** `/bootstrap SPEC-0002`

**Copilot:** *[Creates github/user-service with scaffolding...]*

---

## Part 2: Order Service (Depends on User Service)

Now we create the Order Service, which needs to call the User Service API.

### Creating the Proposal

**User:** `/propose`

**Copilot:** **What problem are you trying to solve?**

**User:** We need an order management service that handles the e-commerce checkout flow. It needs to validate users exist before creating orders, and associate orders with user accounts.

**Copilot:** **Title?**

**User:** Order Service

**Copilot:** *[Continues...]*

**User:** *[Provides details about order processing, payment integration needs, etc.]*

**Copilot:** Created `decisions/proposals/PROP-0004-order-service.md`

### Creating the Spec with Dependencies

**User:** `/spec PROP-0004`

**Copilot:** I'll create a specification implementing PROP-0004: Order Service.

**What repositories will implement this spec?**

**User:** github/order-service

**Copilot:** **What's the first requirement?**

**User:** The service shall validate that a user exists before creating an order.

**Copilot:** **Scenario - Given?**

**User:** Given a request to create an order with user_id "user123"

**Copilot:** **When?**

**User:** When the order creation endpoint is called

**Copilot:** **Then?**

**User:** Then the service must call the User Service API to verify the user exists. If not, return 400 Bad Request.

**Copilot:** Got it:

> **Scenario: User validation on order creation**
> - GIVEN a request to create an order with user_id "user123"
> - WHEN the order creation endpoint is called
> - THEN the service calls User Service API to verify user exists; returns 400 if not

*[Continues gathering other requirements...]*

**Copilot:** **Does this spec depend on any other specs?**

**User:** Yes, it depends on SPEC-0002 (User Service) for the user validation API.

**Copilot:** I'll add SPEC-0002 as a dependency.

*[Reads SPEC-0002 to understand the API...]*

Found SPEC-0002: User Service with implementations:
- github/user-service

I'll add this to the Spec Dependencies section. When you bootstrap, the user-service repo will be cloned into `scratchpad/contexts/` for reference.

---

## Spec Summary

**ID:** SPEC-0003  
**Title:** Order Service  
**Implements:** PROP-0004  
**Implementations:** github/order-service

**Spec Dependencies:**
- SPEC-0002 (User Service) - for user validation API

---

**Ready to create the spec?**

**User:** Yes

**Copilot:** Created `decisions/specs/SPEC-0003-order-service.md`

### Bootstrapping Order Service

**User:** `/bootstrap SPEC-0003`

**Copilot:** I'll bootstrap the implementing repositories for SPEC-0003.

*[Reads spec...]*

Found spec: **SPEC-0003: Order Service**

**Spec Dependencies:**
- SPEC-0002: User Service → github/user-service

---

## Bootstrap Summary

**Spec:** SPEC-0003 (Order Service)  
**Extensions Found:** 0  
**Applicable Standards:** 1 (STD-0001: Go Services)  
**Spec Dependencies:** 1 (SPEC-0002: User Service)

### Repositories to Process

| Repository | Status | Action |
|------------|--------|--------|
| github/order-service | new | Create |

### Dependency Handling
The following repos will be cloned into `scratchpad/contexts/` for API reference:
- github/user-service (implements SPEC-0002)

**Do you want me to proceed?**

**User:** Yes

**Copilot:** Creating repository github/order-service...

*[Creates repo, scaffolds files...]*

Cloning dependency into `scratchpad/contexts/`:
- Shallow cloning github/user-service → scratchpad/contexts/user-service/

---

## Bootstrap Complete

### Repository: github/order-service
- **Status:** Created (internal)

### Files Scaffolded
- README.md
- AGENTS.md
- .gitignore (includes `scratchpad/`)
- .mcp.json
- .github/prompts/implement.prompt.md
- .github/prompts/plan.prompt.md
- .github/spec/spec.md
- .github/standards/STD-0001-go-services.md

### Dependencies in scratchpad/contexts/
- `scratchpad/contexts/user-service/` - User Service code for API reference

### Next Steps
1. Open `github/order-service` in VS Code
2. Run `/plan` to create issues
3. Reference `scratchpad/contexts/user-service/` when implementing user validation

---

## Part 3: How Dependencies Work in Practice

When implementing the Order Service, here's what `scratchpad/contexts/` provides:

```
github/order-service/
├── scratchpad/
│   └── contexts/
│       └── user-service/       # Shallow clone of user-service
│       ├── api/
│       │   └── openapi.yaml    # User API specification
│       ├── pkg/
│       │   └── client/         # Generated Go client
│       └── ...
├── .github/
│   └── spec/
│       └── spec.md             # SPEC-0003
├── cmd/
├── internal/
└── ...
```

When you run `/implement` on the "User validation" requirement, Copilot can:
1. Read the User Service's OpenAPI spec from `scratchpad/contexts/user-service/api/openapi.yaml`
2. See how the Go client is structured in `scratchpad/contexts/user-service/pkg/client/`
3. Generate correct API calls that match the actual interface

**Important:** Files in `scratchpad/contexts/` are read-only reference. Never modify them—they're just for understanding the APIs you depend on.

---

## Part 4: Notification Service (Depends on Order Events)

The Notification Service depends on Order Service events (not REST API).

### Creating the Spec with Event Dependencies

**User:** `/spec PROP-0005` *(assuming proposal already created)*

**Copilot:** **What's the first requirement?**

**User:** The service shall send order confirmation emails when an order is created.

**Copilot:** **Scenario?**

**User:** Given Order Service publishes an "order.created" event, when the Notification Service receives it, then it sends a confirmation email to the user's email address.

**Copilot:** **Does this spec depend on any other specs?**

**User:** Yes:
- SPEC-0003 (Order Service) for the order.created event schema
- SPEC-0002 (User Service) for looking up user email addresses

**Copilot:** I'll add both as dependencies.

SPEC-0003 has implementations:
- github/order-service

SPEC-0002 has implementations:
- github/user-service

---

### Bootstrapping with Multiple Dependencies

**User:** `/bootstrap SPEC-0004`

**Copilot:** 

---

## Bootstrap Summary

**Spec:** SPEC-0004 (Notification Service)  
**Spec Dependencies:** 
- SPEC-0003: Order Service → github/order-service
- SPEC-0002: User Service → github/user-service

### Dependency Handling
The following repos will be cloned into `scratchpad/contexts/`:
- github/order-service (for event schemas)
- github/user-service (for user lookup API)

**Do you want me to proceed?**

**User:** Yes

**Copilot:** *[Creates repo, clones both dependencies...]*

---

## The Final Picture

After building all three services:

```
papertrail/
├── decisions/
│   ├── proposals/
│   │   ├── PROP-0003-user-service.md
│   │   ├── PROP-0004-order-service.md
│   │   └── PROP-0005-notification-service.md
│   └── specs/
│       ├── SPEC-0002-user-service.md           # No dependencies
│       ├── SPEC-0003-order-service.md          # Depends on SPEC-0002
│       └── SPEC-0004-notification-service.md   # Depends on SPEC-0002, SPEC-0003
```

```
github/user-service/           # Foundation - no scratchpad/contexts/
github/order-service/
└── scratchpad/contexts/
    └── user-service/          # API reference
github/notification-service/
└── scratchpad/contexts/
    ├── order-service/         # Event schemas
    └── user-service/          # User lookup API
```

The dependency graph ensures services are built in the right order:
1. User Service (no dependencies)
2. Order Service (depends on User Service)
3. Notification Service (depends on both)

---

## Files Created

See the example documents in this directory:
- [PROP-0003-user-service.md](PROP-0003-user-service.md)
- [SPEC-0002-user-service.md](SPEC-0002-user-service.md)
- [PROP-0004-order-service.md](PROP-0004-order-service.md)
- [SPEC-0003-order-service.md](SPEC-0003-order-service.md)
