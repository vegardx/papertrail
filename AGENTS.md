# Agent Instructions

## Planning and Clarification

### Always Plan First

Before implementing any changes, create a plan of what you're going to do:
- Outline the steps you'll take
- Identify files that will be modified or created
- Consider potential impacts on other parts of the codebase

### Ask Questions When Unclear

If anything is unclear or ambiguous:
- Ask the user for clarification before proceeding
- Provide suggestions or options when asking (don't just ask open-ended questions)
- Example: "Should I use approach A (faster, less flexible) or approach B (slower, more extensible)?"

## Scratchpad Directory

The `scratchpad/` directory is gitignored and contains two subdirectories:

### `scratchpad/contexts/` - Read-Only Reference

This directory contains cloned repositories from spec dependencies. If any, use it to:
- Understand the API contracts you're consuming
- Reference implementation patterns from dependent specs
- Look up data structures and interfaces

**IMPORTANT:** NEVER modify files in `scratchpad/contexts/`. It exists only for understanding what you're implementing against.

### `scratchpad/agents/` - Your Workspace

This directory is your workspace for:
- Implementation plans and notes
- Design documents and diagrams
- Summaries of research or analysis
- Any working documents that shouldn't be in git history

Use `scratchpad/agents/` freely for any documents you create during implementation. These files are gitignored and won't pollute the repository history.

## Committing Changes

### Always Ask First

After implementing changes, always ask the user if they want to commit. Never commit automatically without confirmation.

### Logical Commits

When changes span multiple files or concepts, split them into logical commits:
- Group related changes into separate commits
- Each commit should represent a single logical unit of work
- Example: separate "add new feature" from "update tests" from "update documentation"

### Partial Staging

When a single file contains multiple unrelated changes:
- Stage only the lines related to the current commit
- Use partial staging (`git add -p`) to keep commits focused
- Do not mix unrelated changes in one commit

### Commit Message Format

Follow conventional commit format:
- `feat:` new features
- `fix:` bug fixes
- `refactor:` code changes that don't add features or fix bugs
- `docs:` documentation changes
- `chore:` maintenance tasks (dependencies, tooling, etc.)

### Breaking Changes

If a change breaks backward compatibility:
- Add `!` before the colon: `feat!:`, `fix!:`, `refactor!:`
- Inform the user about the breaking change before committing
- Example: `feat!: change API response format`

Always highlight breaking changes to the user so they can make an informed decision about the commit.
