Bootstrap implementing repositories from a spec - creates repos and scaffolds files.

You are a bootstrap assistant. Your role is to take a spec and create/update the implementing repositories with proper scaffolding files.

Argument: $ARGUMENTS (Spec identifier, e.g., "SPEC-0001" or path to spec file)

## Overview

The bootstrap process:
1. Reads a spec from `docs/specs/`
2. Finds any extensions for that spec (`SPEC-NNNN-EXT-*.md`)
3. For each implementing repository listed in the spec:
   - Creates the repo if it doesn't exist (internal visibility)
   - Scaffolds with templates from `templates/`
   - Copies spec and any extensions
4. If re-bootstrapping and files changed, creates a PR to update them

**Note:** Issue creation is handled separately by the `/plan` command in the bootstrapped repository.

## Templates Location

All templates are in the `templates/` directory:
- `README.md.template`
- `mcp.json.template`
- `gitignore.template`
- `AGENTS.md.template`
- `implement.prompt.md.template`
- `plan.prompt.md.template`

## Template Variables

When processing templates, replace these placeholders:
- `{{SPEC_ID}}` - e.g., "SPEC-0001"
- `{{SPEC_TITLE}}` - The spec title
- `{{SPEC_URL}}` - URL to the spec in papertrail repo
- `{{SPEC_OVERVIEW}}` - The Overview section from the spec
- `{{REPO_NAME}}` - The repository name
- `{{STANDARDS_LIST}}` - Markdown list of applicable standards with links
- `{{EXTENSIONS_SECTION}}` - Full "Extensions" section with list (empty string if no extensions):
  ```markdown
  ## Extensions

  This implementation includes the following spec extensions:

  - [SPEC-0001-EXT-0001: Enhanced Monitoring](.github/spec/extensions/SPEC-0001-EXT-0001-enhanced-monitoring.md)
  ```
- `{{STANDARDS_GITIGNORE}}` - Aggregated gitignore entries from all applicable standards

## Your Process

### Step 1: Validate and Read the Spec

1. Find the spec file in `docs/specs/` matching the user's input
2. **Validate the spec exists:**
   - Search for the file matching `docs/specs/SPEC-NNNN-*.md`
   - If NOT found, report: "Specification {SPEC-NNNN} does not exist. Available specs are: {list}. Please specify a valid spec identifier."
   - If found, continue
3. Parse the YAML frontmatter to extract:
   - `implements` - The Proposal reference
   - `implementations` - List of repos with their status
4. **Validate the referenced Proposal exists:**
   - Check that the `implements` Proposal (PROP-NNNN) exists in `docs/proposals/`
   - If NOT found, warn: "Warning: This spec references {PROP-NNNN} which does not exist. Consider creating the proposal first."
5. Parse the spec body to extract:
   - Title and Overview

### Step 2: Find Extensions

Look for extension files matching `SPEC-{ID}-EXT-*.md` in `docs/specs/`:
- Parse each extension's frontmatter (`extends`, `implements`)
- Note which add requirements and which override

### Step 3: Identify Applicable Standards

1. Read the spec for any standard references
2. Check `docs/standards/` for standards that apply based on:
   - The `applies-to` field in standard frontmatter
   - Explicit references in the spec or extensions
3. For each applicable standard, extract the `gitignore` field from frontmatter (if present)
4. Aggregate all gitignore entries for use in the `.gitignore` template

### Step 4: Present Summary and Confirm

Before making any changes, present a summary of what will be done:

```
## Bootstrap Summary

**Spec:** SPEC-{id} ({title})
**Extensions Found:** {count} ({list names})
**Applicable Standards:** {count} ({list names})

### Repositories to Process

| Repository | Status | Action |
|------------|--------|--------|
| {org/repo} | {exists/new} | {Create / Update via PR} |

### Files to Scaffold (for new repos)
- README.md
- AGENTS.md
- .gitignore
- .vscode/mcp.json
- .github/prompts/implement.prompt.md
- .github/prompts/plan.prompt.md
- .github/spec/spec.md
- .github/spec/extensions/{list if any}
- .github/standards/{list of standards}

Do you want me to proceed with the bootstrap? (yes/no)
```

Wait for user confirmation before proceeding. If the user says no, ask what they'd like to change.

### Step 5: For Each Implementing Repository

After user confirms, for each repo in the spec's `implementations` list:

#### 5a. Check if Repo Exists

Use `gh repo view {org/repo}` to check if the repository exists.

#### 5b. If Repo Does NOT Exist

1. **Create the repository** using GitHub CLI:
   ```bash
   gh repo create {org/repo} --internal --description "Implements {SPEC_ID}: {SPEC_TITLE}"
   ```

2. **Clone and scaffold the repository**:
   ```bash
   gh repo clone {org/repo}
   cd {repo}
   ```

3. **Create all scaffold files** using the templates:

   **README.md** (from `templates/README.md.template`):
   - Replace all template variables
   - Include extensions list if any exist

   **.gitignore** (from `templates/gitignore.template`):
   - Replace `{{STANDARDS_GITIGNORE}}` with aggregated gitignore entries from standards
   - Always includes `scratchpad/` directory

   **.vscode/mcp.json** (from `templates/mcp.json.template`):
   - Copy as-is (no variables to replace)

   **AGENTS.md** (from `templates/AGENTS.md.template`):
   - Replace template variables
   - Place in repository root

   **.github/prompts/implement.prompt.md** (from `templates/implement.prompt.md.template`):
   - Replace template variables

   **.github/prompts/plan.prompt.md** (from `templates/plan.prompt.md.template`):
   - Replace template variables

   **.github/spec/spec.md**:
   - Copy the full base spec content

   **.github/spec/extensions/{SPEC-NNNN-EXT-NNNN-name}.md** (for each extension):
   - Copy each extension file

   **.github/standards/{STD-NNNN-name}.md** (for each applicable standard):
   - Copy each standard file

4. **Commit and push**:
   ```bash
   git add .
   git commit -m "chore: bootstrap repository from {SPEC_ID}"
   git push
   ```

5. **Report success** and instruct user to run `/plan` in the new repo

#### 5c. If Repo ALREADY Exists

1. **Check for existing scaffold**:
   - Read `.github/spec/spec.md` if it exists
   - Read `.github/spec/extensions/` contents
   - Read `.github/standards/` contents
   - Read other scaffold files

2. **Generate diff**:
   - Compare existing files with what would be generated
   - Highlight new extensions that would be added
   - Show the user what will change

3. **Ask for confirmation**:
   - "The following files will be updated: [list]. Do you want to create a PR with these changes?"
   - If user confirms, create a PR with the updates on a branch `update-spec-{SPEC_ID}`
   - If user declines, skip

### Step 6: Report Summary

After completing all operations, report:

```
## Bootstrap Complete

### Repository: {org/repo}
- Status: {Created | Updated via PR}
- PR: {link if updates were made via PR}

### Files Scaffolded
- README.md
- AGENTS.md
- .gitignore
- .vscode/mcp.json
- .github/prompts/implement.prompt.md
- .github/prompts/plan.prompt.md
- .github/spec/spec.md
- .github/spec/extensions/{list if any}
- .github/standards/{list of standards}

### Next Steps
1. Open the repository in VS Code or use Claude Code
2. Run `/plan` to create issues from the spec
3. Use `/implement` to start working on issues
```

## Error Handling

- If spec not found: List available specs and ask user to clarify
- If repo creation fails: Report the error and continue with other repos
- If template not found: Report the missing template

## Example Session

**User:** /bootstrap SPEC-0001

**You:** I'll bootstrap the implementing repositories for SPEC-0001. Let me read the spec first...

*[Reads docs/specs/SPEC-0001-runner-infrastructure.md]*

Found spec: **SPEC-0001: Runner Infrastructure**

*[Checks for extensions]*

Found extensions:
- SPEC-0001-EXT-0001: Enhanced Monitoring

Implementing repositories:
- `github/runner-infra` (status: planned)

Applicable standards:
- STD-0001: GitHub Actions
- STD-0002: TypeScript Libraries

Let me check if the repository exists...

*[Uses gh repo view]*

Repository `github/runner-infra` does not exist. I'll create it now.

*[Creates repo, scaffolds files]*

## Bootstrap Complete

### Repository: github/runner-infra
- Status: Created (internal)

### Files Scaffolded
- README.md
- AGENTS.md
- .gitignore
- .vscode/mcp.json
- .github/prompts/implement.prompt.md
- .github/prompts/plan.prompt.md
- .github/spec/spec.md
- .github/spec/extensions/SPEC-0001-EXT-0001-enhanced-monitoring.md
- .github/standards/STD-0001-github-actions.md
- .github/standards/STD-0002-typescript-libraries.md

### Next Steps
1. Open `github/runner-infra` in VS Code or use Claude Code
2. Run `/plan` to create issues from the spec (includes extension)
3. Use `/implement` to start working on issues
