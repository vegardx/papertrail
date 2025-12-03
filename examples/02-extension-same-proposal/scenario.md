# Example 02: Extension - Same Proposal

This example shows how to extend a spec when you realize you need more scope mid-implementation.

## Context

The team has been implementing SPEC-0001 (Runner Infrastructure) for a few weeks. During implementation, they realized that cold-starting VMs for every job is causing delays. They need a caching layer to store common dependencies (Docker images, npm packages, etc.) but this wasn't in the original spec.

Since this is still part of the original "Runner Infrastructure" initiative (PROP-0001), it's an extension with the same proposal.

---

## The Conversation

**User:** `/extend SPEC-0001`

**Copilot:** I'll help you create an extension for SPEC-0001. Let me read the base spec first...

*[Reads decisions/specs/SPEC-0001-runner-infrastructure.md]*

**SPEC-0001: Runner Infrastructure** implements PROP-0001 and has these requirements:
1. Auto-Scaling
2. Security Isolation
3. Monitoring

**What Proposal does this extension implement?**
- A) Same as base: PROP-0001 (Runner Infrastructure)
- B) A different Proposal

**User:** A - same proposal. We just didn't think of caching originally.

**Copilot:** Got it, this extension also implements PROP-0001.

**What do you want to do in this extension?**
- A) Add new requirements only
- B) Override existing requirements only
- C) Both add new and override existing

**User:** A - just adding a new requirement for caching

**Copilot:** Let's define the new requirement.

**What's the requirement statement?** Use SHALL or MUST.

**User:** The system shall provide a shared cache for runner dependencies to reduce job startup time.

**Copilot:** Good. Now let's define the scenarios.

**Requirement: Dependency Caching**

Statement: *The system SHALL provide a shared cache for runner dependencies to reduce job startup time.*

**First scenario - Given?**

**User:** Given a workflow uses a Docker image that was previously pulled by another runner

**Copilot:** **When?**

**User:** When the runner starts executing the job

**Copilot:** **Then?**

**User:** Then the Docker image should be loaded from the shared cache instead of pulled from the registry

**Copilot:** Got it:

> **Scenario: Docker image caching**
> - GIVEN a workflow uses a Docker image that was previously pulled by another runner
> - WHEN the runner starts executing the job
> - THEN the Docker image is loaded from the shared cache instead of pulled from the registry

**Any additional scenarios?**

**User:** Yes - we also need npm/yarn package caching

**Copilot:** **Scenario: Package manager caching - Given?**

**User:** Given a workflow runs npm install with a package-lock.json that matches a cached node_modules

**Copilot:** **When?**

**User:** When npm install executes

**Copilot:** **Then?**

**User:** Then packages should be restored from cache, reducing install time by at least 50%

**Copilot:** Got it:

> **Scenario: Package manager caching**
> - GIVEN a workflow runs npm install with a package-lock.json that matches a cached node_modules
> - WHEN npm install executes
> - THEN packages are restored from cache, reducing install time by at least 50%

**Any more scenarios or requirements?**

**User:** One more scenario - cache invalidation

**Copilot:** **Scenario: Cache invalidation - Given?**

**User:** Given a cached artifact is older than 7 days

**Copilot:** **When?**

**User:** When the cache cleanup job runs

**Copilot:** **Then?**

**User:** Then the stale artifacts should be evicted to free storage

**Copilot:** Got it:

> **Scenario: Cache invalidation**
> - GIVEN a cached artifact is older than 7 days
> - WHEN the cache cleanup job runs
> - THEN stale artifacts are evicted to free storage

**Any more requirements to add?**

**User:** That's it for now

**Copilot:** Here's a summary before I create the file:

---

## Extension Summary

**Base Spec:** SPEC-0001 (Runner Infrastructure)  
**Extension ID:** SPEC-0001-EXT-0001  
**Extension Title:** Caching Layer  
**Implements:** PROP-0001 (Runner Infrastructure)

### Changes Overview
- **Added Requirements:** 1
  - Dependency Caching (3 scenarios)
- **Overridden Requirements:** 0

### File to Create
`decisions/specs/SPEC-0001-EXT-0001-caching-layer.md`

**Do you want me to create this extension?**

**User:** Yes

**Copilot:** Created `decisions/specs/SPEC-0001-EXT-0001-caching-layer.md`

---

## Extension Complete

### Summary
- **Extends:** SPEC-0001 (Runner Infrastructure)
- **Implements:** PROP-0001 (Runner Infrastructure)
- **Added Requirements:**
  - Dependency Caching

### Next Steps
1. Run `/bootstrap SPEC-0001` to update the implementing repository
2. In the repo, run `/plan` to add the new requirement issue

---

## Re-bootstrapping

The engineer runs `/bootstrap SPEC-0001` again to update the repo with the extension.

**User:** `/bootstrap SPEC-0001`

**Copilot:** I'll bootstrap the implementing repositories for SPEC-0001.

*[Reads spec and finds extension]*

Found spec: **SPEC-0001: Runner Infrastructure**

Extensions found:
- SPEC-0001-EXT-0001: Caching Layer

*[Checks github/runner-infra]*

Repository `github/runner-infra` already exists.

Comparing files...

---

## Bootstrap Summary

**Spec:** SPEC-0001 (Runner Infrastructure)  
**Extensions Found:** 1 (Caching Layer)  
**Applicable Standards:** 1 (STD-0001: Go Services)

### Repositories to Process

| Repository | Status | Action |
|------------|--------|--------|
| github/runner-infra | exists | Update via PR |

### Files to Update
- `.github/spec/extensions/SPEC-0001-EXT-0001-caching-layer.md` (new)
- `README.md` (add extension reference)

**Do you want me to create a PR with these changes?**

**User:** Yes

**Copilot:** Created PR #12: "Update spec: Add SPEC-0001-EXT-0001 Caching Layer"

Branch: `update-spec-SPEC-0001`

After merging, run `/plan` in the repo to add the new requirement issue.

---

## Files Created

See the example document in this directory:
- [SPEC-0001-EXT-0001-caching-layer.md](SPEC-0001-EXT-0001-caching-layer.md)
