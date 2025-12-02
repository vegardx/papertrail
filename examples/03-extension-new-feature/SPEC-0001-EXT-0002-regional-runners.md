---
status: approved
date: 2024-03-10
extends: SPEC-0001
implements: PROP-0002
authors:
  - "@platform-team"
  - "@eu-expansion"
reviewers:
  - "@infrastructure-leads"
  - "@legal-compliance"
---

# SPEC-0001-EXT-0002: Regional Runners

## Overview

This extension adds multi-region support to the runner infrastructure, enabling teams to run workflows in specific geographic regions for latency and compliance requirements.

**Extends:** [SPEC-0001: Runner Infrastructure](./SPEC-0001-runner-infrastructure.md)  
**Implements:** [PROP-0002: Multi-Region Support](../../proposals/PROP-0002-multi-region-support.md)

## Rationale

As the company expands to Europe, we need runners in EU regions to:
1. Reduce latency for EU-based teams (200ms → <50ms)
2. Meet GDPR requirements for workflows processing EU customer data

This extension builds on the existing runner infrastructure rather than creating a parallel system.

## Added Requirements

### Requirement: Multi-Region Pools

The system SHALL support deploying runner pools in multiple geographic regions.

#### Scenario: Region selection via labels

- **GIVEN** pools are configured for us-east-1 and eu-west-1 regions
- **WHEN** a workflow requests a runner with label `region:eu-west-1`
- **THEN** the workflow is assigned to a runner in the eu-west-1 pool

#### Scenario: Default region for unlabeled workflows

- **GIVEN** a workflow doesn't specify a region label
- **WHEN** the workflow requests a runner
- **THEN** the workflow is assigned to a runner in the default region (us-east-1)

#### Scenario: Region unavailable fallback

- **GIVEN** a workflow requests region:eu-west-1
- **WHEN** no runners are available in eu-west-1 and queue timeout is exceeded
- **THEN** the workflow should fail with a clear error (not silently fall back to another region)

### Requirement: Regional Metrics

The system SHALL expose metrics broken down by region.

#### Scenario: Region-labeled metrics

- **GIVEN** the monitoring system queries runner metrics
- **WHEN** it fetches queue_depth
- **THEN** the metric includes a region label (e.g., `queue_depth{region="eu-west-1"}`)

#### Scenario: Cross-region dashboard

- **GIVEN** an operator views the monitoring dashboard
- **WHEN** they select "All Regions" view
- **THEN** they see aggregated metrics with per-region breakdown

## Interfaces

### Region Configuration

```yaml
regions:
  - name: us-east-1
    default: true
    pools:
      - name: default
        min_runners: 2
        max_runners: 20
        
  - name: eu-west-1
    pools:
      - name: default
        min_runners: 2
        max_runners: 10
        
  - name: eu-central-1
    pools:
      - name: default
        min_runners: 2
        max_runners: 10
```

### Workflow Label Usage

```yaml
# .github/workflows/build.yml
jobs:
  build:
    runs-on: [self-hosted, region:eu-west-1]
    steps:
      - uses: actions/checkout@v4
      # ...
```

### Regional Metrics

```
# HELP queue_depth Number of workflows waiting for a runner
# TYPE queue_depth gauge
queue_depth{region="us-east-1",pool="default"} 3
queue_depth{region="eu-west-1",pool="default"} 1
queue_depth{region="eu-central-1",pool="default"} 0
```

## Security Considerations

- Each region's infrastructure must be isolated at the network level
- Runner credentials must be region-scoped
- Audit logs must capture region information for compliance reporting
- EU regions must not have network paths to non-EU data stores

## Overridden Requirements

None - this extension only adds new capabilities.

## Dependencies

This extension depends on the base SPEC-0001 requirements:
- Auto-Scaling (applied per-region)
- Security Isolation (maintained per-region)
- Monitoring (extended with region labels)
