# Example 03: Extension - New Feature (Different Proposal)

This example shows how to extend an existing spec when a new initiative builds on top of it.

## Context

The runner infrastructure (SPEC-0001) is now in production. A new business requirement comes in: the company is expanding to Europe and needs runners in multiple regions for data residency compliance and reduced latency.

This is a new initiative with its own proposal (PROP-0002), but it extends the existing runner infrastructure rather than starting from scratch.

---

## Part 1: Creating the New Proposal

**User:** `/propose`

**Copilot:** I'll help you create a new proposal. Let's start with the basics.

**What problem are you trying to solve?**

**User:** We're expanding to Europe and need GitHub Actions runners in EU regions. Current runners are US-only, which creates latency issues for EU teams and doesn't meet GDPR requirements for some workflows that process EU customer data.

**Copilot:** Got it. **What title would you like for this proposal?**

**User:** Multi-Region Support

**Copilot:** **What are the goals?**

**User:**
- Deploy runners in EU regions (Frankfurt, Ireland)
- Allow teams to select which region their workflows run in
- Meet GDPR data residency requirements
- Reduce latency for EU-based teams from 200ms to <50ms

**Copilot:** **What are the non-goals?**

**User:**
- Migrating existing workflows (teams opt-in)
- Supporting regions outside US and EU initially
- Cross-region failover (future work)

**Copilot:** **What options have you considered?**

**User:** Two options:
1. Extend existing GARM setup with multi-region pools
2. Deploy separate GARM instances per region

**Copilot:** **Option 1: Extend existing GARM** - pros and cons?

**User:**
Pros: Single control plane, easier management, unified metrics
Cons: More complex networking, single point of failure

**Copilot:** **Option 2: Separate GARM per region** - pros and cons?

**User:**
Pros: Complete isolation, simpler networking, region-independent
Cons: Multiple systems to manage, harder to get unified view

**Copilot:** **What's your recommendation?**

**User:** Option 1 - extend existing GARM. The unified management outweighs the complexity, and we can add cross-region failover later.

**Copilot:** Here's the summary:

---

## Proposal Summary

**ID:** PROP-0002  
**Title:** Multi-Region Support

**Problem:** US-only runners create latency and GDPR compliance issues for EU expansion.

**Goals:**
- EU runners (Frankfurt, Ireland)
- Team-selectable regions
- GDPR compliance
- <50ms latency for EU teams

**Recommendation:** Extend existing GARM with multi-region pools

---

**Ready to create `decisions/proposals/PROP-0002-multi-region-support.md`?**

**User:** Yes

**Copilot:** Created `decisions/proposals/PROP-0002-multi-region-support.md`

---

## Part 2: Extending the Existing Spec

The proposal is approved. Now we need to define the requirements—but instead of a new spec, we extend SPEC-0001 since this builds on that infrastructure.

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

**User:** B - it implements PROP-0002 (Multi-Region Support)

**Copilot:** Let me find that proposal...

*[Reads decisions/proposals/PROP-0002-multi-region-support.md]*

Got it, this extension will implement **PROP-0002: Multi-Region Support**.

**What do you want to do in this extension?**
- A) Add new requirements only
- B) Override existing requirements only
- C) Both add new and override existing

**User:** A - adding new requirements for multi-region

**Copilot:** Let's define the first requirement.

**What's the requirement statement?**

**User:** The system shall support deploying runner pools in multiple geographic regions.

**Copilot:** **Requirement: Multi-Region Pools**

Statement: *The system SHALL support deploying runner pools in multiple geographic regions.*

**Scenario - Given?**

**User:** Given pools are configured for us-east-1 and eu-west-1 regions

**Copilot:** **When?**

**User:** When a workflow requests a runner with label "region:eu-west-1"

**Copilot:** **Then?**

**User:** Then the workflow should be assigned to a runner in the eu-west-1 pool

**Copilot:** Got it:

> **Scenario: Region selection via labels**
> - GIVEN pools are configured for us-east-1 and eu-west-1 regions
> - WHEN a workflow requests a runner with label "region:eu-west-1"
> - THEN the workflow is assigned to a runner in the eu-west-1 pool

**Any more scenarios?**

**User:** Yes - default region behavior

**Copilot:** **Scenario: Default region - Given?**

**User:** Given a workflow doesn't specify a region label

**Copilot:** **When?**

**User:** When the workflow requests a runner

**Copilot:** **Then?**

**User:** Then the workflow should be assigned to a runner in the default region (us-east-1)

**Copilot:** Got it. **What's the next requirement?**

**User:** Region-specific metrics

**Copilot:** **Requirement: Regional Metrics**

Statement?

**User:** The system shall expose metrics broken down by region.

**Copilot:** **Scenario?**

**User:** Given the monitoring system queries runner metrics, when it fetches queue_depth, then the metric should include a region label

**Copilot:** Got it:

> **Requirement: Regional Metrics**
> 
> The system SHALL expose metrics broken down by region.
> 
> **Scenario: Region-labeled metrics**
> - GIVEN the monitoring system queries runner metrics
> - WHEN it fetches queue_depth
> - THEN the metric includes a region label (e.g., queue_depth{region="eu-west-1"})

**Any more requirements?**

**User:** That's enough for the MVP

**Copilot:** Here's the summary:

---

## Extension Summary

**Base Spec:** SPEC-0001 (Runner Infrastructure)  
**Extension ID:** SPEC-0001-EXT-0002  
**Extension Title:** Regional Runners  
**Implements:** PROP-0002 (Multi-Region Support)

### Changes Overview
- **Added Requirements:** 2
  - Multi-Region Pools
  - Regional Metrics
- **Overridden Requirements:** 0

### File to Create
`decisions/specs/SPEC-0001-EXT-0002-regional-runners.md`

**Do you want me to create this extension?**

**User:** Yes

**Copilot:** Created `decisions/specs/SPEC-0001-EXT-0002-regional-runners.md`

---

## Extension Complete

### Summary
- **Extends:** SPEC-0001 (Runner Infrastructure)
- **Implements:** PROP-0002 (Multi-Region Support)
- **Added Requirements:**
  - Multi-Region Pools
  - Regional Metrics

### Next Steps
1. Run `/bootstrap SPEC-0001` to update the implementing repository
2. In the repo, run `/plan` to add the new requirement issues

---

## Key Insight

Notice how this extension:
- **Builds on** SPEC-0001 (reuses the same infrastructure)
- **Implements** PROP-0002 (a different initiative)

This is how you add major features to existing systems without rewriting specs. The base spec stays clean, the new proposal is properly documented, and the extension connects them.

---

## Files Created

See the example documents in this directory:
- [PROP-0002-multi-region-support.md](PROP-0002-multi-region-support.md)
- [SPEC-0001-EXT-0002-regional-runners.md](SPEC-0001-EXT-0002-regional-runners.md)
