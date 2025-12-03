# Example 04: Extension - Override

This example shows how to override an existing requirement when business needs change.

## Context

The runner infrastructure has been running smoothly. Then, a security audit happens. The auditors flag that the current monitoring (basic Prometheus metrics) doesn't meet the new SOC 2 compliance requirements. Specifically:

- All runner lifecycle events must be audit logged
- Logs must be immutable and retained for 1 year
- Access to runners must be traceable to specific users/workflows

This means the original "Monitoring" requirement in SPEC-0001 needs to change. Rather than modifying the approved spec, we create an extension that overrides that requirement.

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

**User:** A - same proposal. This is a compliance enhancement, not a new feature.

**Copilot:** Got it, this extension also implements PROP-0001.

**What do you want to do in this extension?**
- A) Add new requirements only
- B) Override existing requirements only
- C) Both add new and override existing

**User:** B - I need to override the Monitoring requirement. The current one doesn't meet SOC 2 compliance.

**Copilot:** I'll help you override the Monitoring requirement.

Here's the current requirement from SPEC-0001:

> ### Requirement: Monitoring
> 
> The system SHALL expose metrics for runner utilization and queue depth.
> 
> **Scenario: Prometheus metrics**
> - GIVEN the system is running
> - WHEN Prometheus scrapes the /metrics endpoint
> - THEN it returns runner_active, runner_idle, and queue_depth gauges

**Why does this requirement need to change?**

**User:** SOC 2 audit found that basic metrics aren't enough. We need comprehensive audit logging with immutable storage, user traceability, and 1-year retention. The original requirement only covered operational metrics, not compliance-grade auditing.

**Copilot:** Got it. Now let's define the replacement requirement.

**What's the new requirement statement?**

**User:** The system shall provide comprehensive audit logging and metrics that meet SOC 2 compliance requirements.

**Copilot:** **Requirement: Audit Logging and Metrics** (replaces Monitoring)

Statement: *The system SHALL provide comprehensive audit logging and metrics that meet SOC 2 compliance requirements.*

Let's define the scenarios. **First scenario - Given?**

**User:** Given any runner lifecycle event occurs (provision, assign, complete, destroy)

**Copilot:** **When?**

**User:** When the event is processed

**Copilot:** **Then?**

**User:** Then an immutable audit log entry must be created with timestamp, event type, runner ID, workflow ID, repository, and triggering user

**Copilot:** Got it:

> **Scenario: Audit log creation**
> - GIVEN any runner lifecycle event occurs (provision, assign, complete, destroy)
> - WHEN the event is processed
> - THEN an immutable audit log entry is created with timestamp, event type, runner ID, workflow ID, repository, and triggering user

**Additional scenarios?**

**User:** Yes - log retention

**Copilot:** **Scenario: Log retention - Given?**

**User:** Given audit logs are stored

**Copilot:** **When?**

**User:** When the retention policy is evaluated

**Copilot:** **Then?**

**User:** Then logs must be retained for a minimum of 1 year and be tamper-evident

**Copilot:** Got it. **More scenarios?**

**User:** One more - we still need the original metrics, they're just not sufficient alone

**Copilot:** **Scenario: Operational metrics - Given?**

**User:** Given the system is running

**Copilot:** **When?**

**User:** When Prometheus scrapes the metrics endpoint

**Copilot:** **Then?**

**User:** Then it should return runner_active, runner_idle, queue_depth, AND audit_events_total gauges

**Copilot:** Got it. The original metrics are preserved but extended. **Any more scenarios?**

**User:** That covers it

**Copilot:** Here's the summary:

---

## Extension Summary

**Base Spec:** SPEC-0001 (Runner Infrastructure)  
**Extension ID:** SPEC-0001-EXT-0003  
**Extension Title:** Audit Logging  
**Implements:** PROP-0001 (Runner Infrastructure)

### Changes Overview
- **Added Requirements:** 0
- **Overridden Requirements:** 1
  - Monitoring → Audit Logging and Metrics (SOC 2 compliance)

### File to Create
`decisions/specs/SPEC-0001-EXT-0003-audit-logging.md`

**Do you want me to create this extension?**

**User:** Yes

**Copilot:** Created `decisions/specs/SPEC-0001-EXT-0003-audit-logging.md`

---

## Extension Complete

### Summary
- **Extends:** SPEC-0001 (Runner Infrastructure)
- **Implements:** PROP-0001 (Runner Infrastructure)
- **Overridden Requirements:**
  - Monitoring (replaced with Audit Logging and Metrics)

### Next Steps
1. Run `/bootstrap SPEC-0001` to update the implementing repository
2. In the repo, run `/plan` to handle the override (closes old issue, creates new)

---

## How /plan Handles Overrides

When you run `/plan` after adding an override extension:

1. It detects that "Monitoring" is overridden by "Audit Logging and Metrics"
2. It closes the original "Monitoring" issue with a comment:
   > "Superseded by SPEC-0001-EXT-0003: Audit Logging and Metrics. See issue #X."
3. It creates a new issue for "Audit Logging and Metrics"
4. If the original issue had any work done, it links to it for context

This preserves history while clearly indicating the requirement has changed.

---

## Files Created

See the example document in this directory:
- [SPEC-0001-EXT-0003-audit-logging.md](SPEC-0001-EXT-0003-audit-logging.md)
