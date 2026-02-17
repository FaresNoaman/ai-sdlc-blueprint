# SDLC Blueprint: Claude Code + GitHub Spec Kit + GitHub Actions

## For a small team (2–5) building full-stack apps with AWS infrastructure

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                        YOUR SDLC PIPELINE                           │
│                                                                     │
│  ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌───────────┐  │
│  │  SPECIFY   │───▶│   PLAN    │───▶│   TASKS   │───▶│ IMPLEMENT │  │
│  │           │    │           │    │           │    │           │  │
│  │ Spec Kit  │    │ Spec Kit  │    │ Spec Kit  │    │Claude Code│  │
│  │ /specify  │    │ /plan     │    │ /tasks    │    │ Terminal  │  │
│  └─────┬─────┘    └─────┬─────┘    └─────┬─────┘    └─────┬─────┘  │
│        │                │                │                │        │
│     HUMAN            HUMAN            HUMAN            HUMAN       │
│     GATE             GATE             GATE             GATE        │
│  (review &        (review &        (review &        (review PR,   │
│   approve)         approve)         approve)         merge)       │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │                    GITHUB ACTIONS (CI/CD)                     │   │
│  │  • Claude Code PR Review (auto on every PR)                  │   │
│  │  • @claude issue-to-PR (triggered by comment)                │   │
│  │  • TDD enforcement (tests must exist & pass before merge)    │   │
│  │  • Spec governance (PR must reference a spec)                │   │
│  └──────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Part 1: Prerequisites & Installation

### Accounts & Subscriptions

| Tool | Cost | What You Need |
|------|------|---------------|
| Claude Pro or Max | $20/mo or $100/mo per dev | Anthropic account, gives Claude Code CLI access |
| GitHub | Free (or Team at $4/user/mo for branch protection) | Repo with Actions enabled |
| AWS Account | Variable | For deployment; CLI configured locally |
| Spec Kit | Free (MIT) | Python 3.11+, uv, Git |

### Local Installation (Every Developer)

```bash
# 1. Claude Code CLI
# Install via npm (requires Node.js 18+)
npm install -g @anthropic-ai/claude-code

# Verify
claude --version

# 2. GitHub CLI
# macOS
brew install gh
# Linux
sudo apt install gh

# Authenticate
gh auth login

# 3. Spec Kit CLI (Specify)
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git

# Verify
specify check

# 4. AWS CLI (for infra work)
# Follow https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html
aws configure
```

---

## Part 2: Project Structure

After initialization, your repo should look like this:

```
your-project/
├── .github/
│   ├── workflows/
│   │   ├── claude-pr-review.yml      # Auto PR review
│   │   ├── claude-issue-handler.yml   # @claude issue-to-PR
│   │   ├── spec-governance.yml        # Block PRs without specs
│   │   └── tdd-gate.yml              # Test coverage enforcement
│   └── prompts/                       # Spec Kit agent prompts
│       └── claude/                    # Claude-specific prompt files
│
├── .specify/
│   ├── constitution.md                # Project governance rules
│   ├── specs/                         # Feature specifications
│   │   └── 001-user-auth/
│   │       ├── spec.md
│   │       ├── plan.md
│   │       └── tasks.md
│   └── scripts/                       # Spec Kit helper scripts
│
├── CLAUDE.md                          # Claude Code persistent context
│
├── .mcp.json                          # MCP server configuration
│
├── src/                               # Application source code
│   ├── frontend/
│   ├── backend/
│   └── infrastructure/                # CDK / Terraform / SST
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── docs/
│   ├── architecture.md
│   └── adr/                           # Architecture Decision Records
│       └── 001-database-choice.md
│
└── package.json / pyproject.toml
```

---

## Part 3: Initialize the Project

### Step 1: Bootstrap Spec Kit

```bash
cd your-project

# Initialize Spec Kit with Claude Code as the AI agent
specify init . --ai claude

# This creates:
# .github/prompts/claude/  (agent-specific prompt files)
# .specify/                 (specs, plans, tasks directory)
# .specify/scripts/         (helper scripts)
```

### Step 2: Write the Constitution

The constitution is the single most important file in your project. It governs every AI interaction. Open `.specify/constitution.md` and define your non-negotiable rules.

```markdown
# Project Constitution

## Identity
- Project: [Your Project Name]
- Stack: React/Next.js frontend, Node.js/Python backend, AWS (CDK)
- Team: 2-5 developers using Claude Code

## Non-Negotiable Principles

### 1. Spec-First Development
No implementation work begins without an approved specification.
Every PR must reference a spec file in `.specify/specs/`.
Specs are the source of truth — code serves the spec, not the reverse.

### 2. Test-Driven Development
All implementation MUST follow strict TDD:
1. Write tests FIRST (unit + integration where applicable)
2. Tests must be reviewed and approved by a human
3. Tests must FAIL before implementation begins (Red phase)
4. Implement minimum code to pass tests (Green phase)
5. Refactor while keeping tests green (Refactor phase)

No implementation code shall be written before tests exist.

### 3. Architecture Boundaries
- Frontend: React components, no direct AWS SDK calls
- Backend: API layer handles all business logic
- Infrastructure: All AWS resources defined in CDK/IaC — no console clicks
- Data: All database changes require migration scripts

### 4. Code Quality Standards
- TypeScript strict mode (no `any` types)
- All public functions must have JSDoc/docstrings
- Maximum file length: 300 lines (split if larger)
- All API endpoints must have OpenAPI/Swagger documentation

### 5. Security
- No secrets in code (use environment variables / AWS Secrets Manager)
- All user input must be validated and sanitized
- Authentication required on all API endpoints (unless explicitly public)
- Infrastructure follows least-privilege IAM policies

### 6. Git Workflow
- Feature branches from `main`: `feature/spec-{number}-{short-description}`
- Bug fix branches: `fix/{issue-number}-{short-description}`
- All changes via PR — no direct pushes to `main`
- PRs require: passing tests, spec reference, at least 1 human review
```

### Step 3: Create CLAUDE.md

This file loads automatically into every Claude Code session. It's your team's shared context.

```markdown
# CLAUDE.md — Project Context for Claude Code

## Project Overview
[2-3 sentences: what this project does, who it's for]

## Tech Stack
- Frontend: React 18 + Next.js 14, TypeScript, Tailwind CSS
- Backend: Node.js 20 + Express (or Python 3.12 + FastAPI)
- Infrastructure: AWS CDK v2 (TypeScript)
- Database: PostgreSQL (RDS) + DynamoDB for session/cache
- Auth: AWS Cognito
- CI/CD: GitHub Actions

## Repository Structure
- `src/frontend/` — Next.js application
- `src/backend/` — API service
- `src/infrastructure/` — CDK stacks
- `tests/` — All tests (unit, integration, e2e)
- `.specify/` — Specifications, plans, and tasks
- `docs/adr/` — Architecture Decision Records

## Development Rules
- ALWAYS read the relevant spec in `.specify/specs/` before implementing
- ALWAYS write tests FIRST (TDD: Red → Green → Refactor)
- NEVER create AWS resources outside of CDK
- NEVER commit secrets, API keys, or credentials
- NEVER push directly to `main` — always use feature branches and PRs

## Testing Commands
```bash
npm run test           # Unit tests
npm run test:int       # Integration tests
npm run test:e2e       # End-to-end tests
npm run test:coverage  # Coverage report (minimum 80%)
```

## Common Patterns
- API routes follow RESTful conventions: `/api/v1/{resource}`
- Error responses use standard format: `{ error: { code, message, details } }`
- Database queries use repository pattern (never raw SQL in route handlers)
- All async operations use proper error handling (no unhandled promises)

## Before Opening a PR
1. Ensure all tests pass
2. Ensure the PR description references the spec: `Spec: .specify/specs/{name}/`
3. Run linting: `npm run lint`
4. Verify no secrets are committed: `npm run check-secrets`
```

### Step 4: Configure MCP Servers

Create `.mcp.json` at the project root for external tool integrations:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

Add more MCP servers as needed (Slack, Jira, AWS, PostgreSQL, etc.).

---

## Part 4: GitHub Actions Workflows

### Workflow 1: Automated PR Review

Every PR gets an AI code review automatically.

```yaml
# .github/workflows/claude-pr-review.yml
name: Claude PR Review

on:
  pull_request:
    types: [opened, synchronize]

permissions:
  contents: read
  pull-requests: write
  issues: write

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: anthropics/claude-code-action@v1
        with:
          model: "claude-sonnet-4-20250514"
          prompt: |
            Review this PR against our project standards in CLAUDE.md.
            Check for:
            1. Does the PR reference a spec in .specify/specs/?
            2. Are there tests written BEFORE the implementation?
            3. Are there any security concerns?
            4. Does the code follow our architecture boundaries?
            5. Are there any TypeScript `any` types?
            Provide specific, actionable feedback.
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

### Workflow 2: Issue-to-PR Automation

Comment `@claude` on an issue to trigger autonomous implementation.

```yaml
# .github/workflows/claude-issue-handler.yml
name: Claude Issue Handler

on:
  issue_comment:
    types: [created]

permissions:
  contents: write
  pull-requests: write
  issues: write

jobs:
  handle:
    if: contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: anthropics/claude-code-action@v1
        with:
          model: "claude-sonnet-4-20250514"
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          # Claude reads the issue, creates a branch, implements, opens PR
```

### Workflow 3: Spec Governance Gate

Blocks PRs that don't reference a specification.

```yaml
# .github/workflows/spec-governance.yml
name: Spec Governance Check

on:
  pull_request:
    types: [opened, edited, synchronize]

jobs:
  check-spec:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check for spec reference
        run: |
          PR_BODY="${{ github.event.pull_request.body }}"
          # Check PR body references a spec
          if ! echo "$PR_BODY" | grep -qiE "spec:|specification:|\.specify/specs/"; then
            echo "❌ PR must reference a specification."
            echo "Add 'Spec: .specify/specs/{name}/' to your PR description."
            exit 1
          fi

          # Check that referenced spec directory exists
          SPEC_PATH=$(echo "$PR_BODY" | grep -oP '\.specify/specs/[^\s]+' | head -1)
          if [ -n "$SPEC_PATH" ] && [ ! -d "$SPEC_PATH" ]; then
            echo "❌ Referenced spec path does not exist: $SPEC_PATH"
            exit 1
          fi

          echo "✅ Spec reference found."

      - name: Verify spec completeness
        run: |
          SPEC_PATH=$(echo "${{ github.event.pull_request.body }}" | grep -oP '\.specify/specs/[^\s/]+' | head -1)
          if [ -n "$SPEC_PATH" ]; then
            for file in spec.md plan.md tasks.md; do
              if [ ! -f "$SPEC_PATH/$file" ]; then
                echo "⚠️ Missing $file in $SPEC_PATH"
                echo "Spec should have: spec.md, plan.md, and tasks.md"
                exit 1
              fi
            done
            echo "✅ Spec is complete (spec + plan + tasks)."
          fi
```

### Workflow 4: TDD Enforcement

Ensures tests exist and pass before merge.

```yaml
# .github/workflows/tdd-gate.yml
name: TDD Gate

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  test-gate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - run: npm ci

      - name: Check new code has tests
        run: |
          # Get changed source files (not test files)
          CHANGED_SRC=$(git diff --name-only origin/main...HEAD \
            | grep -E '^src/' \
            | grep -v '\.test\.' \
            | grep -v '\.spec\.' \
            | grep -v '__tests__')

          if [ -z "$CHANGED_SRC" ]; then
            echo "✅ No source changes detected."
            exit 0
          fi

          # For each changed source file, check a corresponding test exists
          MISSING_TESTS=""
          for file in $CHANGED_SRC; do
            TEST_FILE=$(echo "$file" | sed 's/\.ts/.test.ts/' | sed 's/\.tsx/.test.tsx/')
            ALT_TEST=$(echo "$file" | sed 's|src/|tests/|' | sed 's/\.ts/.test.ts/')
            if [ ! -f "$TEST_FILE" ] && [ ! -f "$ALT_TEST" ]; then
              MISSING_TESTS="$MISSING_TESTS\n  ❌ $file (no test found)"
            fi
          done

          if [ -n "$MISSING_TESTS" ]; then
            echo "❌ TDD violation: Source files changed without corresponding tests:"
            echo -e "$MISSING_TESTS"
            echo ""
            echo "Every source file must have a test file. Write tests FIRST."
            exit 1
          fi

          echo "✅ All changed source files have corresponding tests."

      - name: Run tests with coverage
        run: |
          npm run test:coverage -- --ci

      - name: Check coverage threshold
        run: |
          # Fail if coverage drops below 80%
          COVERAGE=$(npx nyc report --reporter=json-summary \
            | python3 -c "import sys,json; print(json.load(sys.stdin)['total']['lines']['pct'])")
          if (( $(echo "$COVERAGE < 80" | bc -l) )); then
            echo "❌ Test coverage is ${COVERAGE}% (minimum: 80%)"
            exit 1
          fi
          echo "✅ Test coverage: ${COVERAGE}%"
```

---

## Part 5: The Human Workflow (Day-to-Day)

### The Complete Feature Lifecycle

```
 YOU                          TOOLS                         GITHUB
  │                             │                             │
  │  1. Write feature idea      │                             │
  │──────────────────────────▶  │                             │
  │     (natural language)      │                             │
  │                             │                             │
  │  ◀──────────────────────────│                             │
  │  2. Spec Kit generates      │                             │
  │     requirements & stories  │                             │
  │                             │                             │
  │  3. REVIEW & REFINE         │                             │
  │     (you push back,         │                             │
  │      add edge cases,        │                             │
  │      catch assumptions)     │                             │
  │                             │                             │
  │  4. Approve spec ✓          │                             │
  │──────────────────────────▶  │                             │
  │                             │                             │
  │  ◀──────────────────────────│                             │
  │  5. Spec Kit generates      │                             │
  │     technical plan          │                             │
  │                             │                             │
  │  6. REVIEW ARCHITECTURE     │                             │
  │     (does this make sense?  │                             │
  │      right tech choices?    │                             │
  │      scalability concerns?) │                             │
  │                             │                             │
  │  7. Approve plan ✓          │                             │
  │──────────────────────────▶  │                             │
  │                             │                             │
  │  ◀──────────────────────────│                             │
  │  8. Spec Kit generates      │                             │
  │     task breakdown          │                             │
  │                             │                             │
  │  9. REVIEW TASKS            │                             │
  │     (right sequence?        │                             │
  │      right granularity?)    │                             │
  │                             │                             │
  │  10. Approve tasks ✓        │                             │
  │──────────────────────────▶  │                             │
  │                             │                             │
  │  11. Create feature branch  │                             │
  │──────────────────────────▶  │                             │
  │                             │  ──────────────────────────▶│
  │                             │     Branch created           │
  │                             │                             │
  │  12. For each task:         │                             │
  │      a. Claude writes TESTS │                             │
  │      b. YOU review tests    │                             │
  │      c. Tests FAIL (Red)    │                             │
  │      d. Claude implements   │                             │
  │      e. Tests PASS (Green)  │                             │
  │      f. Claude refactors    │                             │
  │      g. Commit              │                             │
  │                             │                             │
  │  13. Open PR                │                             │
  │──────────────────────────▶  │  ──────────────────────────▶│
  │                             │     PR opened                │
  │                             │                             │
  │                             │  ◀──────────────────────────│
  │                             │  Auto: Claude reviews PR     │
  │                             │  Auto: Spec governance check │
  │                             │  Auto: TDD gate (tests+cov)  │
  │                             │  ──────────────────────────▶│
  │                             │     Checks posted            │
  │                             │                             │
  │  ◀──────────────────────────────────────────────────────── │
  │  14. FINAL REVIEW           │     PR ready for review      │
  │      (read Claude's review, │                             │
  │       check all gates pass, │                             │
  │       human code review)    │                             │
  │                             │                             │
  │  15. MERGE ✓                │                             │
  │──────────────────────────────────────────────────────────▶│
  │                             │     Merged to main           │
```

### Step-by-Step Commands

#### Phase 1: Specify (in Claude Code terminal)

```bash
# Start Claude Code in your project
cd your-project
claude

# Inside Claude Code, run the Spec Kit specify command
> /speckit.specify

# Claude will ask you to describe the feature.
# Example: "User authentication with email/password and social login (Google, GitHub).
#           Must support MFA. Sessions stored in DynamoDB. JWT tokens for API auth."

# Spec Kit generates .specify/specs/002-user-auth/spec.md
# with user stories and acceptance criteria.
```

**YOUR JOB at this gate:**
- Read every user story. Does it capture what you actually want?
- Check acceptance criteria. Are edge cases covered? (What happens on invalid email? Rate limiting? Account lockout after failed attempts?)
- Push back on assumptions. If Claude assumed something about your auth flow, correct it now.
- Only proceed when you'd be comfortable handing this spec to a developer.

#### Phase 2: Plan

```bash
> /speckit.plan

# Generates .specify/specs/002-user-auth/plan.md
# with architecture, data models, API contracts, tech choices
```

**YOUR JOB at this gate:**
- Does the architecture align with your existing system?
- Are the technology choices correct? (e.g., did it pick Cognito like you want, or did it suggest something else?)
- Check the data model — are the database tables/schemas sensible?
- Verify API contracts match your conventions.
- Look for over-engineering. A small feature shouldn't need 5 new services.

#### Phase 3: Tasks

```bash
> /speckit.tasks

# Generates .specify/specs/002-user-auth/tasks.md
# with ordered, dependency-aware implementation tasks
```

**YOUR JOB at this gate:**
- Are tasks in the right order? (Infrastructure before backend, backend before frontend)
- Is each task small enough to implement and verify in one session?
- Are there tasks for tests? (There should be — TDD means test tasks come before implementation tasks)

#### Phase 4: Implement (TDD Loop)

```bash
# Create feature branch
git checkout -b feature/spec-002-user-auth

# For each task, work with Claude Code interactively:
claude

# Step A: Write tests first
> Read the task in .specify/specs/002-user-auth/tasks.md (task 1).
> Write the tests for this task ONLY. Do not write any implementation yet.
> Follow the testing patterns in CLAUDE.md.

# Step B: Review the tests (YOU)
# - Do the tests actually verify the acceptance criteria?
# - Are edge cases covered?
# - Are the tests meaningful or just checking the happy path?

# Step C: Verify tests fail
> Run the tests. They should all fail since there's no implementation yet.

# Step D: Implement
> Now implement the minimum code to make all tests pass.
> Follow the architecture in .specify/specs/002-user-auth/plan.md.

# Step E: Verify tests pass
> Run the tests again. All should pass now.

# Step F: Refactor
> Refactor the implementation for clarity and quality.
> Keep all tests passing.

# Step G: Commit
> Commit this task's changes with message:
> "feat(auth): implement [task description] [spec-002]"
```

#### Phase 5: PR & Review

```bash
# Push and open PR
git push origin feature/spec-002-user-auth
gh pr create --title "feat: User authentication [spec-002]" \
  --body "Spec: .specify/specs/002-user-auth/

## Changes
- [summary of what was implemented]

## Spec Compliance
- [ ] All acceptance criteria met
- [ ] All tasks completed
- [ ] Tests written first (TDD)
- [ ] Coverage above 80%"
```

GitHub Actions automatically:
1. Runs Claude Code PR review → posts comments
2. Runs spec governance check → verifies spec reference
3. Runs TDD gate → verifies tests exist and coverage meets threshold

**YOUR JOB**: Read Claude's review comments, verify all automated checks pass, do your own human review, then merge.

---

## Part 6: Team Conventions

### Branch Naming

```
feature/spec-{number}-{short-description}   # New features
fix/{issue-number}-{short-description}       # Bug fixes
chore/{description}                          # Maintenance (no spec needed)
```

### Commit Messages

```
feat(scope): description [spec-NNN]     # Feature work
fix(scope): description [#issue]        # Bug fixes
test(scope): add tests for X [spec-NNN] # Test-only commits
docs: update architecture docs          # Documentation
chore: upgrade dependencies             # Maintenance
```

### Spec Numbering

Sequential, zero-padded: `001`, `002`, `003`, etc. Each spec gets its own directory under `.specify/specs/`.

### Who Reviews What

| Artifact | Primary Reviewer | What to Check |
|----------|-----------------|---------------|
| Spec (requirements) | Product-minded team member | Does it solve the right problem? Edge cases? |
| Plan (architecture) | Most senior engineer | Fits existing system? Over/under-engineered? |
| Tasks (breakdown) | Implementing developer | Right sequence? Right granularity? |
| Tests (TDD) | Any team member | Meaningful? Covers acceptance criteria? |
| PR (code) | Any team member + Claude auto-review | Quality, security, matches spec? |

---

## Part 7: Handling Bugs & Maintenance

### Bug Workflow (Issue → PR)

```
1. Someone files a GitHub issue describing the bug
2. A developer comments: @claude analyze this bug and suggest a fix
3. Claude Code Action reads the issue, analyzes the codebase, posts analysis
4. If fix is straightforward:
   - Comment: @claude implement a fix for this
   - Claude creates branch, writes tests, implements fix, opens PR
5. If fix is complex:
   - Create a mini-spec: specify the expected behavior, write failing test, then fix
6. PR goes through same review pipeline (auto-review, TDD gate, human review)
```

### When You DON'T Need a Full Spec

Not everything needs the full specify → plan → tasks ceremony:

- **Bug fixes**: Issue description + failing test is sufficient
- **Chores** (dependency upgrades, CI config, linting): No spec needed
- **Documentation**: No spec needed
- **Tiny changes** (copy text, config values): No spec needed, but still need a PR

Add exceptions to your spec governance workflow:

```yaml
# In spec-governance.yml, skip for certain PR labels
if: |
  !contains(github.event.pull_request.labels.*.name, 'bug') &&
  !contains(github.event.pull_request.labels.*.name, 'chore') &&
  !contains(github.event.pull_request.labels.*.name, 'docs')
```

---

## Part 8: Setup Checklist

### One-Time Setup (Team Lead)

- [ ] Create GitHub repository
- [ ] Run `specify init . --ai claude` to bootstrap Spec Kit
- [ ] Write `.specify/constitution.md` (adapt template above)
- [ ] Write `CLAUDE.md` (adapt template above)
- [ ] Create `.mcp.json` with GitHub MCP server
- [ ] Add GitHub Actions workflows (all 4 YAML files above)
- [ ] Add `ANTHROPIC_API_KEY` to GitHub repo secrets
- [ ] Configure branch protection on `main`:
  - Require PR reviews (at least 1)
  - Require status checks (all 4 workflows)
  - No direct pushes
- [ ] Write initial `docs/architecture.md`

### Per-Developer Setup

- [ ] Install Claude Code CLI: `npm install -g @anthropic-ai/claude-code`
- [ ] Install GitHub CLI: `gh auth login`
- [ ] Install Spec Kit: `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git`
- [ ] Clone the repo
- [ ] Run `specify check` to verify tools
- [ ] Read the constitution and CLAUDE.md
- [ ] Do a practice run: create a small spec, implement it with TDD, open a PR

### Per-Feature Workflow (Repeatable)

- [ ] `/speckit.specify` → write & review spec
- [ ] `/speckit.plan` → review architecture
- [ ] `/speckit.tasks` → review task breakdown
- [ ] Create feature branch
- [ ] For each task: write tests → verify fail → implement → verify pass → commit
- [ ] Open PR with spec reference
- [ ] Wait for automated checks (Claude review, spec gate, TDD gate)
- [ ] Human review & merge

---

## Part 9: Growing the System

### Phase 1 (Now): Foundation
Get the basics running — Spec Kit, Claude Code, GitHub Actions, TDD enforcement. This is what's documented above.

### Phase 2 (Month 2-3): Richer Automation
- Add MCP servers for Slack notifications on PR status
- Add MCP server for your database (read-only queries for Claude)
- Create custom Claude Code slash commands for your team's patterns
- Add architecture decision records (ADRs) to the workflow

### Phase 3 (Month 4+): Advanced Patterns
- Multi-agent orchestration: parallel Claude Code sessions on different tasks using git worktrees
- Claude Code sub-agents: specialized agents for frontend, backend, infrastructure
- Spec drift detection: CI job that validates implementation still matches spec
- Property-based testing: add hypothesis/fast-check style tests for critical business logic
- Cost estimation hooks: have Claude estimate AWS cost impact of infrastructure changes

---

## Quick Reference Card

| I want to... | Command / Action |
|---|---|
| Start a new feature | `claude` → `/speckit.specify` |
| Generate technical plan | `/speckit.plan` |
| Break work into tasks | `/speckit.tasks` |
| Implement with TDD | `claude` → "Write tests for task N, then implement" |
| Open a PR | `gh pr create --title "..." --body "Spec: .specify/specs/..."` |
| Get Claude to review a PR | Automatic on PR open (GitHub Action) |
| Get Claude to fix an issue | Comment `@claude` on the GitHub issue |
| Check project governance | `specify check` |
| Run all tests | `npm run test:coverage` |
| View spec status | Browse `.specify/specs/` directory |
