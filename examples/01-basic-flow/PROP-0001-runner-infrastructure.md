---
status: approved
date: 2024-01-15
authors:
  - "@platform-team"
reviewers:
  - "@security-team"
  - "@infrastructure-leads"
---

# PROP-0001: Runner Infrastructure

## Problem Statement

Our Jenkins CI infrastructure has become a significant maintenance burden:
- Slow feedback loops (15+ minute average build times)
- Frequent outages requiring manual intervention
- Teams frustrated with unreliable builds
- Security concerns with shared, persistent build environments

We want to standardize on GitHub Actions, but we cannot use GitHub-hosted runners for our private code due to security and compliance requirements. We need a self-hosted runner solution.

## Goals

- Provide self-hosted runners for all GitHub Actions workflows
- Auto-scale based on demand to avoid queue times
- Meet security requirements (isolated execution, no persistent state)
- Reduce CI feedback time from 15 minutes to under 5 minutes

## Non-Goals

- Migrating existing Jenkins pipelines (teams will handle their own migrations)
- Supporting non-GitHub CI systems
- Providing GPU runners (considered future work)

## Background

We've been running Jenkins for 5 years. Recent growth has exposed scaling limitations:
- Current setup: 20 static build agents
- Peak queue depth: 50+ jobs waiting
- Average wait time: 8 minutes before a job starts

GitHub Actions is already used for open source projects and has strong team adoption interest.

## Proposed Direction

### Option 1: GARM (GitHub Actions Runner Manager)

Use [GARM](https://github.com/cloudbase/garm), an open-source tool designed specifically for managing self-hosted GitHub Actions runners.

**Pros:**
- Purpose-built for this exact use case
- Active community and regular updates
- Supports multiple cloud providers (AWS, Azure, GCP, OpenStack)
- Native auto-scaling based on webhook events
- Pool-based management for different runner types

**Cons:**
- Another system to learn and maintain
- Learning curve for the operations team
- Relatively new project (less battle-tested)

### Option 2: Build Custom Orchestration

Build our own runner orchestration layer on top of the official GitHub runner binary.

**Pros:**
- Full control over implementation
- Can customize exactly to our needs
- No external dependencies

**Cons:**
- Significant engineering investment (estimated 3-6 months)
- Reinventing well-solved problems
- Ongoing maintenance burden
- Delays time-to-value

### Option 3: Commercial Solution (BuildJet/Actuated)

Use a managed service like BuildJet or Actuated.

**Pros:**
- Fully managed, minimal operational burden
- Proven at scale
- Support included

**Cons:**
- Expensive at our scale ($50k+/year estimated)
- Less control over security posture
- Vendor lock-in
- Data leaves our infrastructure

## Recommendation

**Option 1: GARM**

GARM provides the right balance of capability and control:
- Avoids the engineering investment of building from scratch
- Keeps infrastructure in-house for security requirements
- Active development means ongoing improvements
- Pool-based model maps well to our team structure

The learning curve is acceptable given the team's experience with similar orchestration tools.

## Next Steps

1. Create technical specification with detailed requirements
2. Proof of concept with single runner pool
3. Security review of GARM architecture
4. Gradual rollout starting with non-critical workflows
