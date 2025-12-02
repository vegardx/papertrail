---
status: approved
date: 2024-03-01
authors:
  - "@platform-team"
  - "@eu-expansion"
reviewers:
  - "@infrastructure-leads"
  - "@legal-compliance"
---

# PROP-0002: Multi-Region Support

## Problem Statement

Our GitHub Actions runner infrastructure (SPEC-0001) currently operates only in US regions. This creates two problems as we expand to Europe:

1. **Latency**: EU-based teams experience 200ms+ latency to US runners, slowing down their CI feedback loops
2. **Compliance**: GDPR requires that certain workflows processing EU customer data run within EU boundaries

Without multi-region support, EU teams cannot use our CI infrastructure for sensitive workloads.

## Goals

- Deploy runner pools in EU regions (Frankfurt: eu-central-1, Ireland: eu-west-1)
- Allow teams to select which region their workflows run in via labels
- Meet GDPR data residency requirements for EU data processing
- Reduce latency for EU-based teams from 200ms to <50ms

## Non-Goals

- Migrating existing workflows automatically (teams opt-in via labels)
- Supporting regions outside US and EU initially (Asia-Pacific is future work)
- Cross-region failover (complexity not justified yet)
- Multi-region caching (cache stays regional for simplicity)

## Background

Our runner infrastructure has been in production for 2 months:
- 150+ repositories using self-hosted runners
- Average 500 workflow runs per hour
- 99.5% availability

The EU expansion is launching Q2, with 3 engineering teams relocating to Dublin and Berlin offices.

## Proposed Direction

### Option 1: Extend Existing GARM (Recommended)

Add multi-region pool support to our existing GARM deployment.

**Architecture:**
- Single GARM control plane in US
- Runner pools deployed per region (us-east-1, eu-central-1, eu-west-1)
- Region selection via workflow labels

**Pros:**
- Single control plane for management
- Unified metrics and alerting
- Reuses existing operational knowledge
- Easier capacity planning across regions

**Cons:**
- More complex networking (control plane to regional pools)
- Control plane is single point of failure
- Higher latency for EU pool management operations

### Option 2: Separate GARM Instances Per Region

Deploy independent GARM instances in each region.

**Architecture:**
- One GARM instance per region
- Completely isolated operations
- Region selection via repository configuration

**Pros:**
- Complete isolation between regions
- Simpler networking (everything local)
- No single point of failure

**Cons:**
- Multiple systems to manage and update
- No unified view of capacity
- Harder to implement cross-region features later
- Duplicated operational burden

## Recommendation

**Option 1: Extend Existing GARM**

The benefits of unified management outweigh the networking complexity:
- One place to update configurations
- Single dashboard for all regions
- Easier capacity rebalancing
- Foundation for future cross-region features

The single point of failure concern is mitigated by:
- Control plane only manages lifecycle, not job execution
- Runners continue operating if control plane is briefly unavailable
- We can add control plane HA later if needed

## Next Steps

1. Create spec extension for SPEC-0001 with multi-region requirements
2. Network architecture review with infrastructure team
3. GDPR compliance review with legal
4. Deploy EU pools in staging environment
5. Beta rollout with EU-expansion teams
