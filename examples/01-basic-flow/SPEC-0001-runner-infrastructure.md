---
status: approved
date: 2024-01-22
implements: PROP-0001
implementations:
  - repo: github/runner-infra
    status: in-progress
authors:
  - "@platform-team"
reviewers:
  - "@security-team"
---

# SPEC-0001: Runner Infrastructure

## Overview

This specification defines the requirements for a self-hosted GitHub Actions runner infrastructure using GARM. The system will provide auto-scaling, ephemeral runners for all organization workflows.

**Implements:** [PROP-0001: Runner Infrastructure](../../proposals/PROP-0001-runner-infrastructure.md)

## Requirements

### Requirement: Auto-Scaling

The system SHALL automatically scale runners based on workflow queue depth.

#### Scenario: Scale up on queue pressure

- **GIVEN** a pool configured with min=2, max=20 runners
- **WHEN** more than 5 workflows are queued for over 30 seconds
- **THEN** the system provisions additional runners until queue depth < 5 or max is reached

#### Scenario: Scale down on idle

- **GIVEN** runners have been idle for more than 10 minutes
- **WHEN** the number of runners exceeds the minimum
- **THEN** the system terminates idle runners down to the minimum

### Requirement: Security Isolation

The system SHALL execute each workflow job in an isolated, ephemeral environment.

#### Scenario: Clean environment per job

- **GIVEN** a workflow job is assigned to a runner
- **WHEN** the job completes (success or failure)
- **THEN** the runner VM is destroyed and a fresh one provisioned

#### Scenario: No state persistence

- **GIVEN** a workflow job has completed
- **WHEN** a subsequent job runs on a new runner
- **THEN** no files, environment variables, or credentials from the previous job are accessible

### Requirement: Monitoring

The system SHALL expose metrics for runner utilization and queue depth.

#### Scenario: Prometheus metrics

- **GIVEN** the system is running
- **WHEN** Prometheus scrapes the /metrics endpoint
- **THEN** it returns runner_active, runner_idle, and queue_depth gauges

#### Scenario: Alerting thresholds

- **GIVEN** monitoring is configured
- **WHEN** queue_depth exceeds 20 for more than 5 minutes
- **THEN** an alert is triggered to the on-call channel

## Interfaces

### Metrics Endpoint

```
GET /metrics

# HELP runner_active Number of runners currently executing jobs
# TYPE runner_active gauge
runner_active{pool="default"} 5

# HELP runner_idle Number of runners waiting for jobs
# TYPE runner_idle gauge
runner_idle{pool="default"} 2

# HELP queue_depth Number of workflows waiting for a runner
# TYPE queue_depth gauge
queue_depth{pool="default"} 3
```

### Configuration

```yaml
pools:
  - name: default
    min_runners: 2
    max_runners: 20
    scale_up_threshold: 5
    scale_up_delay: 30s
    scale_down_delay: 10m
    instance_type: m5.large
```

## Security Considerations

- Runner VMs must not have access to production networks
- GitHub App credentials must be stored in a secrets manager
- All runner-to-GARM communication must use mTLS
- Audit logs must capture all runner lifecycle events

## Spec Dependencies

None - this is a standalone infrastructure component.
