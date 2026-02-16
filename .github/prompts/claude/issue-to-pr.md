# Claude Code Issue-to-PR Prompt

You have been invoked via `@claude` comment on a GitHub issue.

## Your Mission
Analyze the issue, implement a solution following the project's TDD and spec-driven approach, and open a pull request.

## Workflow

### Phase 1: Analysis
1. **Read the issue carefully**
   - What is the problem or feature request?
   - Is there enough information to proceed?
   - If unclear, ask clarifying questions in a comment

2. **Determine if a spec is needed**
   - **Need spec**: New features, architectural changes, multi-file changes
   - **No spec needed**: Bug fixes, small tweaks, chores
   - If unsure, err on the side of creating a spec

3. **Check related code**
   - Search for relevant files
   - Understand the current implementation
   - Identify what needs to change

### Phase 2: Specification (if needed)
1. **Create a mini-spec** in `.specify/specs/`
   - Number it sequentially (e.g., `002-fix-user-login`)
   - Write `spec.md` with acceptance criteria
   - Write `plan.md` with technical approach
   - Write `tasks.md` with implementation steps

2. **Comment on the issue** with spec link
   - "I've created a spec for this issue: `.specify/specs/002-fix-user-login/`"
   - Wait for human approval before proceeding (unless issue explicitly says to proceed)

### Phase 3: Implementation (TDD)
1. **Create a feature branch**
   - `feature/spec-{number}-{description}` for features
   - `fix/{issue-number}-{description}` for bugs

2. **For each task, follow TDD:**
   - Write tests FIRST
   - Run tests → they should FAIL (Red)
   - Implement minimum code to pass (Green)
   - Refactor while keeping tests green (Refactor)
   - Commit with message: `feat(scope): description [spec-NNN]`

3. **Ensure quality**
   - All tests pass
   - Coverage meets 80% threshold
   - No security vulnerabilities introduced
   - Follows architecture boundaries from CLAUDE.md

### Phase 4: Pull Request
1. **Open PR with proper format:**
   ```
   Title: feat: [Brief description] [spec-NNN]

   Body:
   Fixes #[issue-number]
   Spec: .specify/specs/NNN-name/

   ## Changes
   - [What was implemented]

   ## Testing
   - [How to test this]

   ## Spec Compliance
   - [x] All acceptance criteria met
   - [x] Tests written first (TDD)
   - [x] Coverage above 80%
   ```

2. **Comment on the original issue:**
   "I've opened a PR to address this: #[PR-number]"

## Important Rules
- **NEVER push directly to main**
- **ALWAYS write tests before implementation**
- **ALWAYS reference specs in PRs** (unless bug/chore/docs)
- **NEVER commit secrets or credentials**
- **ALWAYS follow the constitution** in `.specify/memory/constitution.md`

## When to Ask for Help
- If the issue is ambiguous or underspecified
- If you need to make architectural decisions
- If the issue requires breaking changes
- If you're unsure about the scope

## Context Files
- Read `CLAUDE.md` for project standards
- Read `.specify/memory/constitution.md` for governance rules
- Check existing patterns in the codebase

## Success Criteria
- PR passes all CI checks (Claude review, spec governance, TDD gate)
- PR is ready for human review
- Original issue will be closed when PR merges
