---
status: "{draft | review | approved | implemented | deprecated}"
date: {YYYY-MM-DD when the spec was last updated}
implements: {PROP-NNNN reference, e.g., PROP-0001}
authors: {list of specification authors}
reviewers: {list of people who reviewed this spec}
implementations:
  - repo: {org/repo-name}
    status: "{planned | in-progress | complete}"
    link: {optional link to PR, issue, or docs}
---

# {short title describing what is being specified}

## Overview

{Brief summary of what this specification covers and its purpose. One to two paragraphs maximum.}

## Related Proposal

This specification implements [PROP-NNNN: {Proposal title}](../proposals/PROP-NNNN-{slug}.md).

{Optional: Brief explanation of how this spec relates to the Proposal.}

## Goals

* {Goal 1: What this spec aims to achieve}
* {Goal 2}

## Non-Goals

* {Non-goal 1: What is explicitly out of scope}
* {Non-goal 2}

## Technology

### Language & Runtime

* **Language:** {e.g., TypeScript 5.x, Go 1.21, Python 3.12}
* **Runtime:** {e.g., Node.js 20 LTS, Deno 2.x}

### Frameworks

* **Primary Framework:** {e.g., Docusaurus 3.x, Express 4.x, FastAPI}
* **Build Tool:** {e.g., npm, pnpm, Make}

### Key Libraries

* {Library 1}: {purpose, e.g., "Algolia DocSearch - search functionality"}
* {Library 2}: {purpose}

### File Structure

```
{project root}
├── {directory}/     # {purpose}
│   ├── {subdirectory}/
│   └── {file}
├── {directory}/     # {purpose}
├── {config-file}    # {purpose}
└── {config-file}    # {purpose}
```

## Requirements

### Requirement: {requirement name}

{System | Component | Service} SHALL/MUST {clear, testable requirement statement}.

**Configuration:**
* `{config-file}` SHALL {specific configuration requirement}
* `{config-file}` SHALL {another configuration requirement}

**Behavior:**
* {Specific behavior with measurable criteria}
* {Another behavior with measurable criteria}

**Files:**
| Action | Path | Purpose |
|--------|------|---------|
| Create | `{path/to/file}` | {What this file does} |
| Create | `{path/to/file}` | {What this file does} |

#### Scenario: {scenario name}

**GIVEN** {precondition or initial context}
**WHEN** {action or trigger}
**THEN** {expected outcome or behavior}
**AND** {additional verifiable outcome}

#### Scenario: {alternative scenario name}

**GIVEN** {precondition or initial context}
**WHEN** {action or trigger}
**THEN** {expected outcome or behavior}

### Requirement: {another requirement name}

{System | Component | Service} SHALL/MUST {clear, testable requirement statement}.

**Configuration:**
* {Specific configuration requirements}

**Files:**
| Action | Path | Purpose |
|--------|------|---------|
| Create | `{path/to/file}` | {What this file does} |

#### Scenario: {scenario name}

**GIVEN** {precondition or initial context}
**WHEN** {action or trigger}
**THEN** {expected outcome or behavior}

## Interfaces

{Define any APIs, interfaces, or contracts that implementations must adhere to. Include request/response formats, endpoints, function signatures, etc. Use OpenAPI, JSON Schema, or similar where appropriate.}

## Data Model

{Describe any data structures, schemas, or state that implementations must support.}

## Security Considerations

{Security requirements and constraints that implementations must address.}

## Open Questions

* {Question 1: Unresolved decision or clarification needed}
* {Question 2}

## Spec Dependencies

{List other specs that this spec depends on or integrates with. These specs may provide APIs, data formats, or services that this implementation will consume. Remove this section if there are no dependencies.}

### SPEC-NNNN: {title}

**Relationship:** {How this spec relates to the referenced spec, e.g., "Consumes the authentication API defined in this spec"}

**Relevant requirements:**
* {Requirement name from the referenced spec that this implementation depends on}
* {Another requirement}

### SPEC-NNNN: {another title}

**Relationship:** {Description of the dependency}

**Relevant requirements:**
* {Requirement name}

## References

* {Link to related documentation}
* {Link to relevant external resources}
