---
status: "{draft | review | approved | deprecated | superseded by STD-NNNN}"
date: {YYYY-MM-DD when the standard was last updated}
applies-to: "{all | github-actions | libraries | services | infrastructure | ...}"
authors: {list of standard authors}
reviewers: {list of people who reviewed this standard}
gitignore:
  - "{optional: paths or patterns to add to .gitignore, e.g., node_modules/}"
  - "{optional: another pattern, e.g., dist/}"
---

# {short title describing the standard}

## Overview

{Brief summary of what this standard covers and why it exists. One to two paragraphs maximum.}

## Scope

### Applies To

{Describe what this standard applies to. Be specific.}

* {Project type, language, or context 1}
* {Project type, language, or context 2}

### Does Not Apply To

* {Exception 1}
* {Exception 2}

## Standard

### {Category 1, e.g., "Language & Runtime"}

#### MUST

* {Mandatory requirement 1}
* {Mandatory requirement 2}

#### SHOULD

* {Recommended practice 1}
* {Recommended practice 2}

#### MAY

* {Optional practice 1}

### {Category 2, e.g., "Repository Structure"}

#### MUST

* {Mandatory requirement}

#### SHOULD

* {Recommended practice}

### {Category 3, e.g., "Testing"}

#### MUST

* {Mandatory requirement}

#### SHOULD

* {Recommended practice}

### {Category 4, e.g., "Documentation"}

#### MUST

* {Mandatory requirement}

#### SHOULD

* {Recommended practice}

### {Category 5, e.g., "Security"}

#### MUST

* {Mandatory requirement}

#### SHOULD

* {Recommended practice}

## Examples

### Compliant Example

{Show a compliant implementation, code snippet, or configuration.}

```
{example code or structure}
```

### Non-Compliant Example

{Show what NOT to do and explain why.}

```
{example of violation}
```

**Why this violates the standard:** {explanation}

## Enforcement

{Describe how compliance with this standard is verified.}

* **Automated checks**: {linters, CI checks, etc.}
* **Manual review**: {code review checklist items}
* **Exceptions process**: {how to request an exception}

## Tooling Integration

{Describe how to integrate this standard into development workflows. Remove this section if not applicable.}

### Linter Configuration

{If this standard can be enforced via linting, provide configuration snippets.}

```json
{
  "example": "linter config"
}
```

### CI/CD Integration

{If this standard should be checked in CI, describe the setup.}

```yaml
# Example GitHub Actions step
- name: Check compliance
  run: {command}
```

### IDE Setup

{If there are IDE plugins or settings that help enforce this standard, list them.}

* **VS Code**: {extension or setting}
* **JetBrains**: {plugin or setting}

## Version History

{Track significant changes to this standard. Remove this section for new standards.}

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | {YYYY-MM-DD} | {author} | Initial version |
| 1.1 | {YYYY-MM-DD} | {author} | {Brief description of changes} |

## Rationale

{Explain WHY these standards exist. Link to related Proposals if applicable.}

* {Reason 1}
* {Reason 2}

## References

* {Link to related standards}
* {Link to external style guides or best practices}
* {Link to related Proposals}
