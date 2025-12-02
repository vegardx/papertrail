---
status: approved
date: 2024-04-15
extends: SPEC-0001
implements: PROP-0001
authors:
  - "@platform-team"
  - "@security-team"
reviewers:
  - "@compliance"
  - "@infrastructure-leads"
---

# SPEC-0001-EXT-0003: Audit Logging

## Overview

This extension replaces the basic Monitoring requirement with comprehensive audit logging and metrics to meet SOC 2 compliance requirements.

**Extends:** [SPEC-0001: Runner Infrastructure](./SPEC-0001-runner-infrastructure.md)  
**Implements:** [PROP-0001: Runner Infrastructure](../../proposals/PROP-0001-runner-infrastructure.md)

## Rationale

A SOC 2 security audit identified gaps in our runner infrastructure's observability:

| Gap | Finding | Requirement |
|-----|---------|-------------|
| Audit Trail | No immutable record of runner lifecycle events | All events must be logged with tamper-evident storage |
| User Traceability | Cannot trace runner usage to specific users | Logs must include triggering user/workflow |
| Retention | Metrics are ephemeral | Logs must be retained for 1 year minimum |

The original "Monitoring" requirement covered operational metrics but not compliance-grade auditing. This extension replaces it with a comprehensive solution.

## Overridden Requirements

### Requirement: Audit Logging and Metrics

**Replaces:** Monitoring (from SPEC-0001)

**Original requirement summary:** Basic Prometheus metrics for runner utilization and queue depth.

**Reason for change:** SOC 2 compliance requires immutable audit logging with user traceability and 1-year retention. Basic metrics are insufficient.

---

The system SHALL provide comprehensive audit logging and metrics that meet SOC 2 compliance requirements.

#### Scenario: Audit log creation

- **GIVEN** any runner lifecycle event occurs (provision, assign, complete, destroy)
- **WHEN** the event is processed
- **THEN** an immutable audit log entry is created with:
  - Timestamp (RFC 3339)
  - Event type
  - Runner ID
  - Workflow ID
  - Repository (org/repo)
  - Triggering user (GitHub username or app)

#### Scenario: Log retention and immutability

- **GIVEN** audit logs are stored
- **WHEN** the retention policy is evaluated
- **THEN** logs must be:
  - Retained for a minimum of 1 year
  - Tamper-evident (cryptographic verification)
  - Stored in append-only storage

#### Scenario: Operational metrics (preserved from original)

- **GIVEN** the system is running
- **WHEN** Prometheus scrapes the /metrics endpoint
- **THEN** it returns:
  - `runner_active` gauge
  - `runner_idle` gauge
  - `queue_depth` gauge
  - `audit_events_total` counter (new)

#### Scenario: Audit log query

- **GIVEN** a compliance officer needs to investigate runner usage
- **WHEN** they query audit logs for a specific repository and time range
- **THEN** they receive all matching events within 30 seconds

## Interfaces

### Audit Log Schema

```json
{
  "timestamp": "2024-04-15T10:30:00Z",
  "event_type": "runner.assigned",
  "runner_id": "runner-abc123",
  "workflow_id": "12345678",
  "workflow_run_id": "87654321",
  "repository": "github/example-repo",
  "triggering_user": "octocat",
  "triggering_actor_type": "user",
  "region": "us-east-1",
  "pool": "default",
  "metadata": {
    "labels": ["self-hosted", "linux", "x64"]
  }
}
```

### Event Types

| Event | Description |
|-------|-------------|
| `runner.provisioned` | New runner VM created |
| `runner.registered` | Runner registered with GitHub |
| `runner.assigned` | Workflow job assigned to runner |
| `runner.completed` | Job completed (success/failure) |
| `runner.destroyed` | Runner VM terminated |
| `runner.error` | Runner encountered an error |

### Audit Log Configuration

```yaml
audit:
  enabled: true
  storage:
    type: s3
    bucket: runner-audit-logs
    region: us-east-1
  retention:
    days: 365
  encryption:
    enabled: true
    kms_key_id: alias/runner-audit
  integrity:
    enabled: true
    algorithm: sha256
```

### Additional Metrics

```
# HELP audit_events_total Total number of audit events by type
# TYPE audit_events_total counter
audit_events_total{event_type="runner.assigned"} 12345
audit_events_total{event_type="runner.completed"} 12340

# HELP audit_log_write_latency_seconds Time to write audit log entry
# TYPE audit_log_write_latency_seconds histogram
audit_log_write_latency_seconds_bucket{le="0.01"} 10000
audit_log_write_latency_seconds_bucket{le="0.1"} 12000
```

## Security Considerations

- Audit logs must be stored in a separate AWS account from runners
- Log access must be restricted to compliance and security teams
- Log integrity must be verifiable via cryptographic checksums
- Failed log writes must block the operation (fail-safe)

## Added Requirements

None - this extension only overrides the Monitoring requirement.
