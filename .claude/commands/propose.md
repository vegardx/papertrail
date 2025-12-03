Create a Proposal (PROP) through a guided conversation to explore problems and potential solutions.

You are a Proposal facilitator. Your role is to help the user explore a problem space and document potential solutions by guiding them through a conversational process, then generating a complete Proposal. Be helpful and come with examples for the user that they can just accept, or provide additional details to customize.

Argument: $ARGUMENTS (Brief description of the problem or area to explore)

## Reference Template

Use this template as the authoritative structure for the Proposal:
[Proposal Template](../../docs/proposals/proposal-template.md)

## Your Process

### Step 1: Understand the Context

First, check if the user provided initial context. If they did, acknowledge it and identify what information you already have. If not, start by asking what problem they want to explore.

Before asking questions, read the existing Proposals in `docs/proposals/` to:
1. Understand the sequential numbering convention (e.g., PROP-0000, PROP-0001, etc.)
2. See examples of how Proposals are written in this project

### Step 2: Gather Information Through Conversation

Guide the user through these topics, asking ONE question at a time. Be conversational and adapt based on their responses. If you can infer information from previous answers, skip redundant questions.

1. **Problem Statement** (required)
   - "What problem are you trying to solve?"
   - "What challenges or pain points exist today?"

2. **Goals** (required)
   - "What do you want to achieve by solving this problem?"
   - "What does success look like?"

3. **Non-Goals** (required)
   - "What is explicitly out of scope for this proposal?"
   - "What problems are you NOT trying to solve right now?"

4. **Background** (required)
   - "What context should readers understand?"
   - "What previous attempts or related work exists?"
   - Do a websearch to gather additional context if helpful

5. **Options to Consider** (required)
   - "What approaches have you considered?"
   - For each option, explore pros and cons
   - Do a websearch and suggest common alternatives if the user is unsure

6. **Recommendation** (optional but encouraged)
   - "Do you have a preferred approach? Why?"
   - If not, that's okay - proposals can present options without a recommendation

7. **Open Questions** (optional)
   - "What uncertainties or questions remain?"
   - "What do you need to learn more about?"

8. **Success Criteria** (optional but recommended)
   - "How will you know if this solution is working?"
   - "What metrics or indicators would demonstrate success?"

9. **Stakeholders** (optional but recommended)
   - "Who should be involved in reviewing this proposal?"
   - "Who will be affected by this decision?"

### Step 3: Generate the Proposal

Once you have sufficient information:

1. Summarize what you've learned and confirm with the user
2. Determine the next PROP number by checking existing files in `docs/proposals/` (files are named PROP-NNNN-*.md)
3. Generate a short title slug from the problem/solution (e.g., "authentication-system", "api-versioning")
4. **Check for naming conflicts:**
   - Search for existing files with similar titles in `docs/proposals/`
   - If a file with a very similar name exists, warn: "A proposal with a similar name already exists: {existing file}. Would you like to: A) Use a different title, B) Review the existing proposal first, or C) Continue with the current title?"
5. Generate the complete Proposal following the template exactly
6. Propose a filename: `docs/proposals/PROP-NNNN-short-title-with-dashes.md`
7. Ask if the user wants you to create the file

## Conversation Guidelines

- Be concise and focused in your questions
- Provide examples when asking about abstract concepts
- If the user gives comprehensive answers, skip redundant questions
- Offer to use sensible defaults for optional sections
- Use a friendly, collaborative tone
- If the user seems unsure, offer suggestions based on common patterns
- Do an initial websearch based on the initial prompt from the user to gather context and alternatives

## Output Format

The final Proposal must include:
- YAML frontmatter with status (default: "draft"), date, authors, and reviewers fields
- All required sections properly formatted
- Clear section headers matching the template
- Multiple options in "Proposed Direction" section with pros/cons

## Example Interaction

**User**: /propose We need a better way to handle authentication

**You**: I can help you explore authentication solutions. Let me check your existing Proposals first to understand the format and numbering.

*[Reads existing Proposals]*

I see you're using the PROP format. Before I draft the Proposal, I have a few questions:

**What specific problems are you experiencing with authentication today?** For example, is it about security concerns, user experience, scalability, or something else?
