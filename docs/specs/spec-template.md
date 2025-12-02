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

## Requirements

### Requirement: {requirement name}

{System | Component | Service} SHALL/MUST {clear, testable requirement statement}.

#### Scenario: {scenario name}

**GIVEN** {precondition or initial context}
**WHEN** {action or trigger}
**THEN** {expected outcome or behavior}

#### Scenario: {alternative scenario name}

**GIVEN** {precondition or initial context}
**WHEN** {action or trigger}
**THEN** {expected outcome or behavior}

### Requirement: {another requirement name}

{System | Component | Service} SHALL/MUST {clear, testable requirement statement}.

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

## References

* {Link to related documentation}
* {Link to relevant external resources}
