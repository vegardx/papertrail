# Example 01: Basic Flow

This example shows the complete journey from idea to implementation-ready repository.

## Context

The platform team needs to replace their aging Jenkins CI infrastructure with self-hosted GitHub Actions runners. They've been discussing it for weeks and want to formalize the decision.

---

## Part 1: Creating the Proposal

The engineer opens VS Code in the papertrail repository and starts a conversation.

### Conversation

**User:** `/propose`

**Copilot:** I'll help you create a new proposal. Let's start with the basics.

**What problem are you trying to solve?**

**User:** We need to replace our Jenkins CI infrastructure. It's becoming a maintenance burden, teams are frustrated with the slow feedback loops, and we want to standardize on GitHub Actions. But we need self-hosted runners for security and compliance reasons - we can't use GitHub-hosted runners for our private code.

**Copilot:** Good context. That gives me a clear problem statement.

**What title would you like for this proposal?** Keep it short and descriptive.

**User:** Runner Infrastructure

**Copilot:** Got it. Now let's define scope.

**What are the goals of this proposal?** What must it achieve to be considered successful?

**User:** 
- Provide self-hosted runners for all GitHub Actions workflows
- Auto-scale based on demand to avoid queue times
- Meet security requirements (isolated execution, no persistent state)
- Reduce CI feedback time from 15 minutes to under 5 minutes

**Copilot:** Clear goals. 

**What are the non-goals?** What's explicitly out of scope?

**User:**
- Migrating existing Jenkins pipelines (teams do that themselves)
- Supporting non-GitHub CI systems
- Providing GPU runners (future work)

**Copilot:** Now let's explore solutions.

**What options have you considered?** I'll help you structure the pros and cons.

**User:** We've looked at three options:
1. Use GARM (GitHub Actions Runner Manager) - it's open source and designed for this
2. Build our own orchestration on top of the GitHub runner binary
3. Use a commercial solution like BuildJet or Actuated

**Copilot:** Let me gather details on each.

**Option 1: GARM** - What are the pros and cons?

**User:** 
Pros: Purpose-built for this, active community, supports multiple cloud providers, handles auto-scaling natively
Cons: Another system to maintain, learning curve for the team

**Copilot:** **Option 2: Build our own** - What are the pros and cons?

**User:**
Pros: Full control, can customize exactly to our needs
Cons: Significant engineering investment, we'd be reinventing the wheel, ongoing maintenance burden

**Copilot:** **Option 3: Commercial solution** - What are the pros and cons?

**User:**
Pros: Fully managed, less operational burden
Cons: Expensive at our scale, less control over security posture, vendor lock-in

**Copilot:** **What's your recommendation?**

**User:** Option 1 - GARM. It's the right balance of capability and control. We avoid building from scratch while keeping everything in-house for security.

**Copilot:** I have everything I need. Here's a summary before I create the file:

---

## Proposal Summary

**ID:** PROP-0001  
**Title:** Runner Infrastructure  

**Problem:** Jenkins CI is a maintenance burden with slow feedback. Need self-hosted GitHub Actions runners for security/compliance.

**Goals:**
- Self-hosted runners for all workflows
- Auto-scaling to avoid queue times
- Isolated execution, no persistent state
- CI feedback under 5 minutes

**Non-Goals:**
- Migrating Jenkins pipelines
- Non-GitHub CI support
- GPU runners

**Recommendation:** Use GARM (Option 1)

---

**Ready to create `docs/proposals/PROP-0001-runner-infrastructure.md`?**

**User:** Yes

**Copilot:** Created `docs/proposals/PROP-0001-runner-infrastructure.md`

The proposal is ready for review. Once approved, use `/spec PROP-0001` to create the technical specification.

---

## Part 2: Creating the Specification

A week later, the proposal has been reviewed and approved. Time to define the requirements.

### Conversation

**User:** `/spec PROP-0001`

**Copilot:** I'll create a specification implementing PROP-0001: Runner Infrastructure.

Let me read the proposal first...

*[Reads docs/proposals/PROP-0001-runner-infrastructure.md]*

Got it. This spec will define requirements for the GARM-based runner infrastructure.

**What repositories will implement this spec?** List the org/repo names.

**User:** github/runner-infra

**Copilot:** Now let's define the requirements. I'll guide you through each one using the SHALL/MUST format with GIVEN/WHEN/THEN scenarios.

**What's the first requirement?**

**User:** The system must automatically scale runners based on workflow queue depth.

**Copilot:** Let me help you make that concrete.

**Requirement: Auto-Scaling**

Statement: *The system SHALL automatically scale runners based on workflow queue depth.*

Now let's define the scenario. **Given what initial state?**

**User:** Given a pool configured with min=2, max=20 runners

**Copilot:** **When what happens?**

**User:** When more than 5 workflows are queued for over 30 seconds

**Copilot:** **Then what should occur?**

**User:** Then the system should provision additional runners until queue depth is below 5 or max is reached

**Copilot:** Got it:

> **Requirement: Auto-Scaling**
> 
> The system SHALL automatically scale runners based on workflow queue depth.
> 
> **Scenario: Scale up on queue pressure**
> - GIVEN a pool configured with min=2, max=20 runners
> - WHEN more than 5 workflows are queued for over 30 seconds
> - THEN the system provisions additional runners until queue depth < 5 or max reached

**What's the next requirement?**

**User:** Security isolation - each job runs in a fresh VM that's destroyed after

**Copilot:** **Requirement: Security Isolation**

Statement: *The system SHALL execute each workflow job in an isolated, ephemeral environment.*

**Scenario setup - Given?**

**User:** Given a workflow job is assigned to a runner

**Copilot:** **When?**

**User:** When the job completes (success or failure)

**Copilot:** **Then?**

**User:** Then the runner VM must be destroyed and a fresh one provisioned

**Copilot:** Got it:

> **Requirement: Security Isolation**
> 
> The system SHALL execute each workflow job in an isolated, ephemeral environment.
> 
> **Scenario: Clean environment per job**
> - GIVEN a workflow job is assigned to a runner
> - WHEN the job completes (success or failure)  
> - THEN the runner VM is destroyed and a fresh one provisioned

**What's the next requirement?**

**User:** Monitoring - we need metrics for runner utilization and queue depth

**Copilot:** **Requirement: Monitoring**

Statement: *The system SHALL expose metrics for runner utilization and queue depth.*

**Scenario?**

**User:** Given the system is running, when Prometheus scrapes the metrics endpoint, then it should return runner_active, runner_idle, and queue_depth gauges

**Copilot:** Got it:

> **Requirement: Monitoring**
> 
> The system SHALL expose metrics for runner utilization and queue depth.
> 
> **Scenario: Prometheus metrics**
> - GIVEN the system is running
> - WHEN Prometheus scrapes the /metrics endpoint
> - THEN it returns runner_active, runner_idle, and queue_depth gauges

**Any more requirements?**

**User:** That covers the MVP

**Copilot:** Let me check for any spec dependencies.

**Does this spec depend on any other specs?** For example, APIs or services from other systems.

**User:** No, this is standalone

**Copilot:** Here's the summary before I create the file:

---

## Spec Summary

**ID:** SPEC-0001  
**Title:** Runner Infrastructure  
**Implements:** PROP-0001  
**Implementations:** github/runner-infra

**Requirements:**
1. Auto-Scaling - scale based on queue depth
2. Security Isolation - ephemeral VMs per job
3. Monitoring - Prometheus metrics

---

**Ready to create `docs/specs/SPEC-0001-runner-infrastructure.md`?**

**User:** Yes

**Copilot:** Created `docs/specs/SPEC-0001-runner-infrastructure.md`

The spec is ready. Use `/bootstrap SPEC-0001` to create the implementing repository.

---

## Part 3: Bootstrapping the Repository

Time to create the actual repo and start implementing.

### Conversation

**User:** `/bootstrap SPEC-0001`

**Copilot:** I'll bootstrap the implementing repositories for SPEC-0001.

Let me read the spec first...

*[Reads docs/specs/SPEC-0001-runner-infrastructure.md]*

Found spec: **SPEC-0001: Runner Infrastructure**

*[Checks for extensions]*

No extensions found.

*[Checks for applicable standards]*

Found standards:
- STD-0001: Go Services (matches runner infrastructure patterns)

---

## Bootstrap Summary

**Spec:** SPEC-0001 (Runner Infrastructure)  
**Extensions Found:** 0  
**Applicable Standards:** 1 (STD-0001: Go Services)

### Repositories to Process

| Repository | Status | Action |
|------------|--------|--------|
| github/runner-infra | new | Create |

### Files to Scaffold
- README.md
- .gitignore
- .vscode/mcp.json
- .github/copilot-instructions.md
- .github/prompts/implement.prompt.md
- .github/prompts/plan.prompt.md
- .github/spec/spec.md
- .github/standards/STD-0001-go-services.md

**Do you want me to proceed with the bootstrap?**

**User:** Yes

**Copilot:** Creating repository github/runner-infra...

*[Creates repo with internal visibility]*

Scaffolding files...

*[Creates all scaffolded files]*

---

## Bootstrap Complete

### Repository: github/runner-infra
- **Status:** Created (internal)
- **URL:** https://github.com/github/runner-infra

### Files Scaffolded
- README.md
- AGENTS.md
- .gitignore
- .vscode/mcp.json
- .github/prompts/implement.prompt.md
- .github/prompts/plan.prompt.md
- .github/spec/spec.md
- .github/standards/STD-0001-go-services.md

### Next Steps
1. Open `github/runner-infra` in VS Code
2. Run `/plan` to create issues from the spec
3. Use `/implement` to start working on issues

---

## Part 4: Planning Issues (in the new repo)

The engineer opens the new repository in VS Code.

### Conversation

**User:** `/plan`

**Copilot:** I'll create GitHub issues from the spec.

*[Reads .github/spec/spec.md]*

Found **SPEC-0001: Runner Infrastructure** with 3 requirements.

*[Checks .github/spec/extensions/]*

No extensions found.

*[Checks for spec dependencies]*

No dependencies - no repos to clone.

Creating issues...

---

## Plan Complete

### Issues Created

| # | Title | Type | Dependencies |
|---|-------|------|--------------|
| 1 | [SPEC-0001] Runner Infrastructure | Tracking | - |
| 2 | [REQ] Auto-Scaling | Requirement | - |
| 3 | [REQ] Security Isolation | Requirement | - |
| 4 | [REQ] Monitoring | Requirement | #2, #3 |

### Dependency Graph
```
#1 Tracking Issue
├── #2 Auto-Scaling
├── #3 Security Isolation  
└── #4 Monitoring (blocked by #2, #3)
```

### Next Steps
Run `/implement` to start working on the first available issue (#2 or #3).

---

## What Happens Next

The engineer would run `/implement` to:
1. Pick up issue #2 (Auto-Scaling)
2. Create a branch `implement/issue-2-auto-scaling`
3. Get guided through the implementation following the spec and standards
4. Push changes and create a draft PR

Once #2 and #3 are done, #4 (Monitoring) becomes unblocked.

---

## Files Created

See the example documents in this directory:
- [PROP-0001-runner-infrastructure.md](PROP-0001-runner-infrastructure.md)
- [SPEC-0001-runner-infrastructure.md](SPEC-0001-runner-infrastructure.md)
