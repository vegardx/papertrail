---
status: "{draft | review | approved | implemented | deprecated}"
date: {YYYY-MM-DD when the extension was last updated}
extends: {SPEC-NNNN reference, e.g., SPEC-0001}
implements: {ADR-NNNN reference - can be same or different from base spec}
authors: {list of extension authors}
reviewers: {list of people who reviewed this extension}
---

# {SPEC-NNNN-EXT-NNNN: short title describing the extension}

## Overview

{Brief summary of why this extension exists and what it adds to the base spec. One to two paragraphs maximum.}

## Related ADR

This extension implements [ADR-NNNN: {ADR title}](../decisions/ADR-NNNN-{slug}.md).

{Explain how this ADR relates to the base spec's ADR, if different.}

## Base Spec

This extends [SPEC-NNNN: {base spec title}]({base-spec-filename}.md).

## Added Requirements

{New requirements that don't exist in the base spec.}

### Requirement: {requirement name}

{System | Component | Service} SHALL/MUST {clear, testable requirement statement}.

#### Scenario: {scenario name}

**GIVEN** {precondition or initial context}
**WHEN** {action or trigger}
**THEN** {expected outcome or behavior}

### Requirement: {another requirement name}

{System | Component | Service} SHALL/MUST {clear, testable requirement statement}.

#### Scenario: {scenario name}

**GIVEN** {precondition or initial context}
**WHEN** {action or trigger}
**THEN** {expected outcome or behavior}

## Overridden Requirements

{Requirements from the base spec that this extension replaces. Remove this section if not overriding anything.}

### Overrides: {original requirement name from base spec}

**Original requirement:** {Brief summary of what the original requirement stated}

**Reason for override:** {Why this requirement needs to change}

**New requirement:**

{System | Component | Service} SHALL/MUST {updated requirement statement}.

#### Scenario: {scenario name}

**GIVEN** {precondition or initial context}
**WHEN** {action or trigger}
**THEN** {expected outcome or behavior}

## Interfaces

{Additional interfaces introduced by this extension, if any.}

## Security Considerations

{Additional security considerations introduced by this extension.}

## Open Questions

* {Question 1: Unresolved decision or clarification needed}

## References

* {Link to base spec}
* {Link to related documentation}
