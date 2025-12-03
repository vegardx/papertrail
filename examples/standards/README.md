# Example Standards

This directory contains example standards that demonstrate how to write comprehensive engineering standards using the Papertrail template.

## Standards

| ID | Title | Applies To | Description |
|----|-------|------------|-------------|
| [STD-0001](STD-0001-go-services.md) | Go Services | services | Language, structure, testing, and operational requirements for Go backend services |
| [STD-0002](STD-0002-typescript-libraries.md) | TypeScript Libraries | libraries | Build setup, testing, documentation, and publishing for reusable TS packages |
| [STD-0003](STD-0003-api-design.md) | API Design | services | REST API conventions including URLs, status codes, pagination, and error handling |

## How Standards Work

### Decoupled from Specs

Standards define *how* to build, not *what* to build:

- **Specs** define requirements: "The system SHALL expose metrics for runner utilization"
- **Standards** define conventions: "Services MUST use structured JSON logging"

This separation means:
- The same spec can be implemented following different standards (Go vs TypeScript)
- Standards can be updated without changing specs
- Teams can adopt standards incrementally

### Applied During Bootstrap

When you `/bootstrap` a spec, you select which standards apply:

```
Found standards:
- STD-0001: Go Services (matches service pattern)
- STD-0003: API Design (spec exposes REST API)

Apply these standards? (yes/no)
```

The selected standards are copied to `.github/standards/` in the implementing repo.

### Enforced During Implementation

When you `/implement` a requirement, Copilot:
1. Reads the applicable standards
2. Guides implementation to follow them
3. Generates compliant code (correct error format, proper logging, etc.)

### Gitignore Integration

Standards can specify `.gitignore` patterns in their frontmatter:

```yaml
gitignore:
  - "node_modules/"
  - "dist/"
  - "coverage/"
```

During `/bootstrap`, these patterns are aggregated into the repo's `.gitignore`.

### Scaffold Integration

Standards can specify directories and files to scaffold in their frontmatter:

```yaml
scaffold:
  directories:
    - "src/"
    - "tests/"
  files:
    - path: "tsconfig.json"
      content: |
        {
          "compilerOptions": {
            "strict": true
          }
        }
```

During `/bootstrap`, these directories are created and files are written to the implementing repo. This ensures projects start with the correct structure and configuration files required by the standard.

## Writing Good Standards

### DO

- Be specific and actionable
- Use MUST/SHOULD/MAY consistently (RFC 2119)
- Include compliant and non-compliant examples
- Explain the rationale
- Provide tooling configuration
- Keep enforcement practical

### DON'T

- Be vague ("write good code")
- Require impossible standards (100% coverage)
- Forget to explain why
- Skip the examples
- Ignore existing practices

## Standard Categories

Standards typically cover these areas:

| Category | Examples |
|----------|----------|
| **Language & Runtime** | Version requirements, module system |
| **Repository Structure** | Directory layout, file naming |
| **Code Style** | Formatting, naming conventions |
| **Testing** | Coverage requirements, test patterns |
| **Documentation** | Required docs, comment style |
| **Security** | Input validation, auth patterns |
| **Observability** | Logging, metrics, tracing |
| **Build & Deploy** | CI/CD requirements, versioning |

## Creating a New Standard

1. Use `/std` in the Papertrail repository
2. Follow the template structure
3. Get review from relevant teams
4. Announce to affected teams
5. Provide migration support if changing existing standard
