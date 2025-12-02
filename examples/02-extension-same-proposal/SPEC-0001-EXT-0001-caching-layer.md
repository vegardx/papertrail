---
status: approved
date: 2024-02-10
extends: SPEC-0001
implements: PROP-0001
authors:
  - "@platform-team"
reviewers:
  - "@infrastructure-leads"
---

# SPEC-0001-EXT-0001: Caching Layer

## Overview

This extension adds dependency caching capabilities to the runner infrastructure to reduce job startup times.

**Extends:** [SPEC-0001: Runner Infrastructure](./SPEC-0001-runner-infrastructure.md)  
**Implements:** [PROP-0001: Runner Infrastructure](../../proposals/PROP-0001-runner-infrastructure.md)

## Rationale

During implementation of SPEC-0001, we observed that cold-starting VMs for every job causes significant delays:
- Docker image pulls: 30-60 seconds per job
- npm install on large projects: 2-3 minutes
- Total overhead: up to 4 minutes per job

A shared cache can reduce this to seconds for previously-seen dependencies.

## Added Requirements

### Requirement: Dependency Caching

The system SHALL provide a shared cache for runner dependencies to reduce job startup time.

#### Scenario: Docker image caching

- **GIVEN** a workflow uses a Docker image that was previously pulled by another runner
- **WHEN** the runner starts executing the job
- **THEN** the Docker image is loaded from the shared cache instead of pulled from the registry

#### Scenario: Package manager caching

- **GIVEN** a workflow runs npm install with a package-lock.json that matches a cached node_modules
- **WHEN** npm install executes
- **THEN** packages are restored from cache, reducing install time by at least 50%

#### Scenario: Cache invalidation

- **GIVEN** a cached artifact is older than 7 days
- **WHEN** the cache cleanup job runs
- **THEN** stale artifacts are evicted to free storage

## Interfaces

### Cache Configuration

```yaml
cache:
  enabled: true
  storage:
    type: s3
    bucket: runner-cache
    region: us-east-1
  ttl: 7d
  max_size: 500GB
  types:
    - docker
    - npm
    - yarn
    - pip
```

### Cache Metrics

Additional metrics exposed at `/metrics`:

```
# HELP cache_hits_total Number of cache hits
# TYPE cache_hits_total counter
cache_hits_total{type="docker"} 1234

# HELP cache_misses_total Number of cache misses
# TYPE cache_misses_total counter
cache_misses_total{type="docker"} 56

# HELP cache_size_bytes Current cache size in bytes
# TYPE cache_size_bytes gauge
cache_size_bytes 123456789
```

## Security Considerations

- Cache storage must be encrypted at rest
- Runners must authenticate to access the cache
- Cache keys must be scoped to prevent cross-repository data leakage
- Audit logs must capture cache access patterns

## Overridden Requirements

None - this extension only adds new capabilities.
