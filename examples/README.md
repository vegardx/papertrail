# Examples

This directory contains realistic examples showing how Papertrail works in practice. Each example includes full conversation transcripts so you can see exactly what to type and what to expect.

## Scenario: Building a GitHub Actions Runner Platform

All examples follow a connected story: a team building a self-hosted GitHub Actions runner platform. This shows how documents relate to each other across the full lifecycle.

## Examples

### [01 - Basic Flow](01-basic-flow/)

The foundation: creating a proposal, turning it into a spec, and bootstrapping the implementing repository.

**You'll see:**
- Writing a proposal with `/propose`
- Creating a spec with `/spec`  
- Bootstrapping a repo with `/bootstrap`
- Planning issues with `/plan`

### [02 - Extension: Same Proposal](02-extension-same-proposal/)

Mid-implementation, the team realizes they need a caching layer they didn't think of originally.

**You'll see:**
- Extending a spec to add new requirements
- How extensions reference the same proposal
- Re-bootstrapping to update the repo

### [03 - Extension: New Feature](03-extension-new-feature/)

A new initiative (multi-region support) needs to build on the existing runner infrastructure.

**You'll see:**
- Creating a new proposal for the feature
- Extending an existing spec with a different proposal
- How the extension adds requirements while preserving the base

### [04 - Extension: Override](04-extension-override/)

Compliance requirements change—monitoring now needs audit logging, replacing the original requirement.

**You'll see:**
- Overriding an existing requirement
- How `/plan` handles overrides (closes old issue, creates new)
- Documenting why the change was needed

### [05 - Microservices Dependencies](05-microservices-dependencies/)

Building a multi-service system where the Order Service depends on the User Service API.

**You'll see:**
- Specs with dependency declarations
- How `scratchpad/contexts/` provides API references
- Building services in the right order

### [Standards](standards/)

Example engineering standards that define *how* to build (not *what* to build).

**Includes:**
- STD-0001: Go Services - language, structure, testing, observability
- STD-0002: TypeScript Libraries - build setup, testing, publishing
- STD-0003: API Design - REST conventions, error handling, pagination

## Document Map

After all examples, you'd have:

```
decisions/
├── proposals/
│   ├── PROP-0001-runner-infrastructure.md     # 01
│   └── PROP-0002-multi-region-support.md      # 03
├── specs/
│   ├── SPEC-0001-runner-infrastructure.md     # 01
│   ├── SPEC-0001-EXT-0001-caching-layer.md    # 02
│   ├── SPEC-0001-EXT-0002-regional-runners.md # 03
│   ├── SPEC-0001-EXT-0003-audit-logging.md    # 04
│   ├── SPEC-0002-user-service.md              # 05
│   └── SPEC-0003-order-service.md             # 05
└── standards/
    ├── STD-0001-go-services.md                # standards
    ├── STD-0002-typescript-libraries.md       # standards
    └── STD-0003-api-design.md                 # standards
```

## Reading the Examples

Each example directory contains:

- **`scenario.md`** - Full conversation transcript with context
- **Example documents** - The actual proposal/spec/extension created

The transcripts show:
1. What the user types (prompts)
2. What Copilot asks (clarifying questions)
3. What the user answers
4. What gets created

This lets you follow along and understand the "why" behind each interaction.
