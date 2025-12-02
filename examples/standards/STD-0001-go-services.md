---
status: approved
date: 2024-01-10
applies-to: services
authors:
  - "@platform-team"
reviewers:
  - "@architecture-review"
  - "@security-team"
gitignore:
  - "bin/"
  - "dist/"
  - "*.exe"
  - "vendor/"
  - ".env"
  - ".env.local"
---

# STD-0001: Go Services

## Overview

This standard defines requirements for building backend services in Go. It covers language version, project structure, dependency management, testing, and operational requirements.

All new services MUST follow this standard. Existing services SHOULD migrate when undergoing significant refactoring.

## Scope

### Applies To

* Backend HTTP/gRPC services written in Go
* CLI tools that operate as long-running services
* Workers and background job processors

### Does Not Apply To

* One-off scripts or tools
* Lambda/serverless functions (see STD-0005)
* Libraries (see STD-0002)

## Standard

### Language & Runtime

#### MUST

* Use Go 1.21 or later
* Use Go modules for dependency management
* Pin all dependencies to specific versions (no floating versions)
* Run `go mod tidy` before committing

#### SHOULD

* Update to latest stable Go version within 3 months of release
* Use `golang:1.21-alpine` as base Docker image for builds

#### MAY

* Use generics where they improve code clarity
* Use `go:embed` for static assets

### Repository Structure

#### MUST

* Follow standard Go project layout:
  ```
  ├── cmd/
  │   └── {service-name}/
  │       └── main.go
  ├── internal/
  │   ├── api/
  │   ├── config/
  │   ├── domain/
  │   └── repository/
  ├── pkg/              # Public packages (if any)
  ├── api/              # OpenAPI/protobuf definitions
  ├── migrations/       # Database migrations
  ├── Dockerfile
  ├── Makefile
  ├── go.mod
  └── go.sum
  ```
* Place business logic in `internal/` to prevent external imports
* Place the main entry point in `cmd/{service-name}/main.go`

#### SHOULD

* Use `internal/domain/` for business entities and logic
* Use `internal/repository/` for data access
* Use `internal/api/` for HTTP/gRPC handlers

### Configuration

#### MUST

* Use environment variables for all configuration
* Support `PORT` environment variable for HTTP server port
* Never commit secrets or credentials to the repository
* Validate configuration at startup, fail fast on invalid config

#### SHOULD

* Use a structured config package (e.g., `envconfig`, `viper`)
* Log configuration values at startup (redacting secrets)
* Support `.env` files for local development only

### Error Handling

#### MUST

* Return errors rather than panic (except for truly unrecoverable situations)
* Wrap errors with context using `fmt.Errorf("context: %w", err)`
* Use structured error types for domain errors

#### SHOULD

* Define error variables for known error conditions
* Include operation context in error messages

### Logging

#### MUST

* Use structured logging (JSON format in production)
* Use `slog` (Go 1.21+) or `zerolog`
* Include correlation/request IDs in all log entries
* Log at appropriate levels: DEBUG, INFO, WARN, ERROR

#### SHOULD

* Include service name and version in all log entries
* Use consistent field names across services

### Testing

#### MUST

* Achieve minimum 70% code coverage for business logic
* Write table-driven tests for functions with multiple cases
* Use `t.Parallel()` for independent tests
* Mock external dependencies in unit tests

#### SHOULD

* Use `testify/assert` for assertions
* Use `testify/mock` or `gomock` for mocking
* Write integration tests for repository layer
* Use `dockertest` for database integration tests

#### MAY

* Use `go-cmp` for complex struct comparisons
* Use property-based testing for parsing/serialization logic

### API Design

#### MUST

* Use OpenAPI 3.0+ for REST API documentation
* Return consistent error response format:
  ```json
  {
    "error": {
      "code": "RESOURCE_NOT_FOUND",
      "message": "User with ID 'abc' not found"
    }
  }
  ```
* Use appropriate HTTP status codes
* Include request ID in all responses

#### SHOULD

* Version APIs via URL path (`/api/v1/...`)
* Use JSON for request/response bodies
* Implement pagination for list endpoints

### Observability

#### MUST

* Expose `/health` endpoint for liveness checks
* Expose `/ready` endpoint for readiness checks
* Expose `/metrics` endpoint in Prometheus format
* Include standard metrics: request count, latency histogram, error rate

#### SHOULD

* Implement distributed tracing (OpenTelemetry)
* Include trace IDs in logs
* Create dashboards in Grafana

### Security

#### MUST

* Validate all input data
* Use parameterized queries (never string concatenation for SQL)
* Run as non-root user in containers
* Scan dependencies for vulnerabilities in CI

#### SHOULD

* Implement rate limiting for public endpoints
* Use mTLS for service-to-service communication
* Rotate credentials regularly

## Examples

### Compliant Example

```go
// cmd/user-service/main.go
package main

import (
    "context"
    "log/slog"
    "os"
    "os/signal"
    "syscall"

    "github.com/org/user-service/internal/api"
    "github.com/org/user-service/internal/config"
)

func main() {
    // Structured logging
    logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
    slog.SetDefault(logger)

    // Load and validate config
    cfg, err := config.Load()
    if err != nil {
        slog.Error("failed to load config", "error", err)
        os.Exit(1)
    }

    // Graceful shutdown
    ctx, cancel := signal.NotifyContext(context.Background(), 
        syscall.SIGINT, syscall.SIGTERM)
    defer cancel()

    // Start server
    if err := api.Run(ctx, cfg); err != nil {
        slog.Error("server error", "error", err)
        os.Exit(1)
    }
}
```

### Non-Compliant Example

```go
package main

import (
    "fmt"
    "net/http"
)

var dbPassword = "hardcoded-secret"  // VIOLATION: hardcoded secret

func main() {
    http.HandleFunc("/users", func(w http.ResponseWriter, r *http.Request) {
        query := "SELECT * FROM users WHERE id = " + r.URL.Query().Get("id") // VIOLATION: SQL injection
        fmt.Println(query) // VIOLATION: unstructured logging
        // ...
    })
    http.ListenAndServe(":8080", nil) // VIOLATION: no graceful shutdown, ignores PORT
}
```

**Why this violates the standard:**
- Hardcoded credentials (MUST use environment variables)
- SQL injection vulnerability (MUST use parameterized queries)
- Unstructured logging (MUST use structured logging)
- No graceful shutdown handling
- Ignores PORT environment variable

## Enforcement

* **Automated checks**: 
  - `golangci-lint` runs in CI with custom config
  - `go test -cover` must show ≥70% coverage
  - `gosec` scans for security issues
  - `govulncheck` checks for vulnerable dependencies

* **Manual review**: 
  - PR reviewers verify project structure
  - Security review for auth/crypto code

* **Exceptions process**: 
  - Request via PR with justification in description
  - Requires approval from @platform-team

## Tooling Integration

### Linter Configuration

```yaml
# .golangci.yml
run:
  go: "1.21"
  timeout: 5m

linters:
  enable:
    - errcheck
    - gosimple
    - govet
    - ineffassign
    - staticcheck
    - unused
    - gosec
    - errorlint
    - gocritic

linters-settings:
  errcheck:
    check-type-assertions: true
  gocritic:
    enabled-tags:
      - diagnostic
      - style
      - performance
```

### CI/CD Integration

```yaml
# .github/workflows/ci.yml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: '1.21'
      
      - name: Lint
        uses: golangci/golangci-lint-action@v4
        
      - name: Test
        run: go test -race -coverprofile=coverage.out ./...
        
      - name: Check coverage
        run: |
          coverage=$(go tool cover -func=coverage.out | grep total | awk '{print $3}' | sed 's/%//')
          if (( $(echo "$coverage < 70" | bc -l) )); then
            echo "Coverage $coverage% is below 70%"
            exit 1
          fi
          
      - name: Security scan
        run: gosec ./...
```

### IDE Setup

* **VS Code**: Install `golang.go` extension, enable format on save
* **GoLand**: Enable "Go Modules" and configure golangci-lint as external tool

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2024-01-10 | @platform-team | Initial version |

## Rationale

* **Go 1.21+**: Required for `slog` and latest security patches
* **Standard layout**: Consistency across services reduces onboarding time
* **Structured logging**: Enables log aggregation and searching in production
* **70% coverage**: Balances test effort with confidence; critical paths often exceed this
* **Environment variables**: Follows 12-factor app principles, works with Kubernetes

## References

* [Standard Go Project Layout](https://github.com/golang-standards/project-layout)
* [Effective Go](https://go.dev/doc/effective_go)
* [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md)
* [12 Factor App](https://12factor.net/)
