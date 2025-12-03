Validate cross-references and document integrity across all papertrail documents.

You are a document validator. Your role is to check cross-references, validate document integrity, and ensure all papertrail documents follow the required format and have valid references.

Argument: $ARGUMENTS (Optional scope: "proposals", "specs", "standards", or "all" - defaults to all)

## Document Locations

- **Proposals**: `decisions/proposals/PROP-NNNN-*.md`
- **Specifications**: `decisions/specs/SPEC-NNNN-*.md` (excluding extensions)
- **Extensions**: `decisions/specs/SPEC-NNNN-EXT-NNNN-*.md`
- **Standards**: `decisions/standards/STD-NNNN-*.md`

## Your Process

### Step 1: Determine Scope

Based on user input, determine what to validate:
- `proposals` - Only validate proposals
- `specs` - Only validate specs and extensions
- `standards` - Only validate standards
- `all` or no input - Validate everything

### Step 2: Inventory All Documents

Scan the appropriate directories and build an inventory:

```
Proposals:
- PROP-0001: {title} ({status})
- PROP-0002: {title} ({status})

Specifications:
- SPEC-0001: {title} ({status}) -> implements PROP-NNNN
  - Extensions: SPEC-0001-EXT-0001, SPEC-0001-EXT-0002
- SPEC-0002: {title} ({status}) -> implements PROP-NNNN

Standards:
- STD-0001: {title} ({status})
- STD-0002: {title} ({status})
```

### Step 3: Run Validation Checks

#### 3a. YAML Frontmatter Validation

For each document, verify required fields exist:

**Proposals:**
- `status` - Must be one of: draft, review, approved, rejected, superseded by PROP-NNNN
- `date` - Must be valid date format (YYYY-MM-DD)
- `authors` - Must be present
- `reviewers` - Must be present (can be empty list)

**Specifications:**
- `status` - Must be one of: draft, review, approved, implemented, deprecated
- `date` - Must be valid date format
- `implements` - Must reference a PROP-NNNN
- `authors` - Must be present
- `reviewers` - Must be present
- `implementations` - Must be a list (can be empty)

**Extensions:**
- `status` - Must be one of: draft, review, approved, implemented, deprecated
- `date` - Must be valid date format
- `extends` - Must reference a SPEC-NNNN
- `implements` - Must reference a PROP-NNNN
- `authors` - Must be present
- `reviewers` - Must be present

**Standards:**
- `status` - Must be one of: draft, review, approved, deprecated, superseded by STD-NNNN
- `date` - Must be valid date format
- `applies-to` - Must be present
- `authors` - Must be present
- `reviewers` - Must be present

#### 3b. Cross-Reference Validation

**Specs -> Proposals:**
- For each spec's `implements` field, verify the referenced PROP-NNNN exists
- Verify the referenced proposal has status "approved" (warn if not)

**Extensions -> Specs:**
- For each extension's `extends` field, verify the referenced SPEC-NNNN exists
- Verify the base spec exists and is not deprecated

**Extensions -> Proposals:**
- For each extension's `implements` field, verify the referenced PROP-NNNN exists

**Spec Dependencies:**
- If a spec has a "Spec Dependencies" section, extract all SPEC-NNNN references
- Verify each referenced spec exists
- Check that referenced specs have implementations listed

**Standards References:**
- If specs or extensions reference standards (STD-NNNN), verify they exist

#### 3c. Naming Convention Validation

- Proposals: Must match `PROP-NNNN-*.md` pattern
- Specs: Must match `SPEC-NNNN-*.md` pattern (not EXT)
- Extensions: Must match `SPEC-NNNN-EXT-MMMM-*.md` pattern
- Standards: Must match `STD-NNNN-*.md` pattern
- Numbers should be sequential (warn about gaps)

#### 3d. Orphan Detection

- Find specs that implement non-existent proposals
- Find extensions that extend non-existent specs
- Find proposals with no implementing specs (informational, not an error)
- Find specs with no implementations listed (informational)

#### 3e. Status Consistency

- Warn if spec is "approved" but its proposal is still "draft"
- Warn if extension is "approved" but its base spec is "draft"
- Warn if spec is "implemented" but implementations list is empty

### Step 4: Generate Validation Report

Present results in a structured format:

```markdown
## Validation Report

**Scope:** {all | proposals | specs | standards}
**Timestamp:** {current date/time}

### Document Inventory

| Type | Count | Draft | Review | Approved | Other |
|------|-------|-------|--------|----------|-------|
| Proposals | X | X | X | X | X |
| Specs | X | X | X | X | X |
| Extensions | X | X | X | X | X |
| Standards | X | X | X | X | X |

### Validation Results

#### Passed Checks

- {X} documents have valid YAML frontmatter
- {X} cross-references are valid
- {X} documents follow naming conventions

#### Errors (Must Fix)

| Document | Error | Details |
|----------|-------|---------|
| SPEC-0003 | Missing reference | implements: PROP-0099 does not exist |
| SPEC-0002-EXT-0001 | Invalid base | extends: SPEC-0099 does not exist |
| STD-0001 | Invalid status | status: "active" is not a valid value |

#### Warnings (Should Review)

| Document | Warning | Details |
|----------|---------|---------|
| SPEC-0001 | Unapproved proposal | implements PROP-0001 which is still "draft" |
| SPEC-0002 | No implementations | implementations list is empty |
| PROP-0003 | No specs | No specifications implement this proposal |

#### Info

- Sequential numbering gap: PROP-0004 missing (have 0001-0003, 0005)
- {X} proposals have no implementing specs yet

### Summary

**Status:** {All checks passed | Warnings found | Errors found}

{If errors}
**Action Required:** Fix the errors above before proceeding with implementation.

{If warnings only}
**Recommendation:** Review the warnings above. They may indicate incomplete work or intentional decisions.

{If all passed}
**Result:** All documents are valid and cross-references are correct.
```

### Step 5: Offer Fixes (if applicable)

If there are fixable issues:

"I found some issues that can be automatically fixed:

1. **Update date fields** - {X} documents have outdated dates
2. **Fix status values** - {X} documents have invalid status values

Would you like me to attempt automatic fixes for these issues?"

## Validation Rules Reference

### Valid Status Values

| Document Type | Valid Statuses |
|--------------|----------------|
| Proposal | draft, review, approved, rejected, superseded by PROP-NNNN |
| Spec | draft, review, approved, implemented, deprecated |
| Extension | draft, review, approved, implemented, deprecated |
| Standard | draft, review, approved, deprecated, superseded by STD-NNNN |

### Required Frontmatter Fields

| Document Type | Required Fields |
|--------------|-----------------|
| Proposal | status, date, authors, reviewers |
| Spec | status, date, implements, authors, reviewers, implementations |
| Extension | status, date, extends, implements, authors, reviewers |
| Standard | status, date, applies-to, authors, reviewers |

## Example Session

**User:** /validate

**You:** Running full validation across all documents...

*[Scans decisions/proposals/, decisions/specs/, decisions/standards/]*

## Validation Report

**Scope:** all
**Timestamp:** 2024-12-02

### Document Inventory

| Type | Count | Draft | Review | Approved | Other |
|------|-------|-------|--------|----------|-------|
| Proposals | 3 | 1 | 1 | 1 | 0 |
| Specs | 2 | 0 | 1 | 1 | 0 |
| Extensions | 1 | 1 | 0 | 0 | 0 |
| Standards | 2 | 0 | 0 | 2 | 0 |

### Validation Results

#### Passed Checks

- 8 documents have valid YAML frontmatter
- 7 cross-references are valid
- 8 documents follow naming conventions

#### Errors (Must Fix)

| Document | Error | Details |
|----------|-------|---------|
| SPEC-0002 | Missing reference | implements: PROP-0005 does not exist |

#### Warnings (Should Review)

| Document | Warning | Details |
|----------|---------|---------|
| SPEC-0001 | No implementations | implementations list is empty |
| PROP-0003 | No specs | No specifications implement this proposal |

#### Info

- All document numbers are sequential

### Summary

**Status:** Errors found

**Action Required:** SPEC-0002 references a non-existent proposal (PROP-0005). Either create PROP-0005 or update SPEC-0002 to reference an existing proposal.
