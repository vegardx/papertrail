# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-12-03

Initial release of Papertrail - a documentation framework for managing proposals, specifications, and standards with AI-assisted workflows.

### Added

- **Core Document Types**
  - Proposals (`/propose`) - Explore problems and propose solutions using MADR-inspired format
  - Specifications (`/spec`) - Define requirements using OpenSpec-style SHALL/MUST statements with GIVEN/WHEN/THEN scenarios
  - Spec Extensions (`/extend`) - Add or override requirements without modifying base specs
  - Standards (`/std`) - Define reusable conventions using RFC 2119 keywords

- **Workflow Commands**
  - `/bootstrap` - Create implementing repositories from specs with scaffolding
  - `/plan` - Generate GitHub issues from spec requirements
  - `/implement` - Guide implementation of specific requirements
  - `/review` - Review documents and provide improvement suggestions
  - `/validate` - Check cross-references and document integrity
  - `/status` - Display overview of all proposals and implementations
  - `/init` - Initialize repository structure based on applicable standards

- **Claude Code Support**
  - Full parity with VS Code Copilot prompts via `.claude/commands/`
  - Shared MCP configuration at `.mcp.json`

- **Standards Features**
  - `gitignore` field in frontmatter for automatic .gitignore generation
  - `scaffold.directories` for creating directory structure via `/init`
  - `scaffold.files` for creating configuration files with default content

- **Repository Scaffolding**
  - README.md with spec overview and links
  - AGENTS.md with AI assistant guidelines
  - Spec and extension files in `.github/spec/`
  - Applicable standards in `.github/standards/`
  - Prompt templates for `/plan` and `/implement`

- **Documentation**
  - Comprehensive examples with full conversation transcripts
  - Example standards for Go Services, TypeScript Libraries, and API Design

### Breaking Changes

- `docs/` directory renamed to `decisions/` to better reflect its purpose
- MCP configuration moved from `.vscode/mcp.json` to `.mcp.json` at repository root
- Bootstrapped repos now require running `/init` to set up .gitignore and .mcp.json

### Changed

- ADR terminology replaced with "Proposals" throughout
- `copilot-instructions.md` renamed to `AGENTS.md`
- `.contexts/` and `.plans/` consolidated into `scratchpad/`

[1.0.0]: https://github.com/github/papertrail/releases/tag/v1.0.0
