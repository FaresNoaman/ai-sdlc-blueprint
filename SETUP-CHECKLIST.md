# Setup Checklist

Use this checklist to ensure your AI SDLC Blueprint is fully configured.

---

## 📋 One-Time Project Setup (Team Lead)

### Repository Setup
- [x] Create GitHub repository
- [x] Initialize with Git
- [x] Set up `.gitignore`
- [ ] Add repository description and topics

### Spec Kit Setup
- [x] Create `.specify/` directory structure
- [x] Write `.specify/memory/constitution.md`
- [x] Create spec templates in `.specify/templates/`
- [x] Add helper scripts in `.specify/scripts/`
- [x] Create initial spec (`001-test-feature/`)

### Project Configuration
- [x] Write `CLAUDE.md` with project context
- [x] Create `.mcp.json` with GitHub MCP server
- [x] Set up `package.json` with all test scripts
- [x] Configure `jest.config.cjs`
- [x] Configure `tsconfig.json`

### GitHub Actions Workflows
- [x] Create `.github/workflows/claude-pr-review.yml`
- [x] Create `.github/workflows/claude-issue-handler.yml`
- [x] Create `.github/workflows/spec-governance.yml`
- [x] Create `.github/workflows/tdd-gate.yml`
- [x] Update workflows with correct model version
- [x] Add spec governance exceptions for bugs/chores/docs

### Claude Agent Prompts
- [x] Create `.github/prompts/claude/` directory
- [x] Write `pr-review.md` prompt
- [x] Write `issue-to-pr.md` prompt

### Documentation
- [x] Write `README.md`
- [x] Write `docs/architecture.md`
- [x] Create `docs/adr/` directory
- [x] Write first ADR (`001-enforce-tdd-via-ci.md`)

### Test Infrastructure
- [x] Create `tests/` directory structure (unit, integration, e2e)
- [x] Write `tests/README.md`
- [x] Add `.gitkeep` files to test directories
- [x] Create sample test file

### GitHub Repository Configuration
- [ ] **CRITICAL: Add `ANTHROPIC_API_KEY` secret**
  - Go to Settings → Secrets and variables → Actions
  - Click "New repository secret"
  - Name: `ANTHROPIC_API_KEY`
  - Value: Your Anthropic API key

- [ ] **Set up branch protection for `main`**
  - Go to Settings → Branches → Add rule
  - Branch name pattern: `main`
  - Enable:
    - [x] Require pull request before merging
    - [x] Require approvals (at least 1)
    - [x] Require status checks to pass before merging
      - [x] TDD Gate
      - [x] Spec Governance Check
      - [x] Claude PR Review
    - [x] Require branches to be up to date before merging
    - [x] Do not allow bypassing the above settings

- [ ] **Enable GitHub Actions**
  - Settings → Actions → General
  - Allow all actions and reusable workflows

- [ ] **Configure GitHub Environments** (optional, for deployment)
  - Settings → Environments
  - Add environment: `production`, `staging`, etc.

---

## 👤 Per-Developer Setup

### Local Tools Installation
- [ ] **Install Claude Code CLI**
  ```bash
  npm install -g @anthropic-ai/claude-code
  ```
  - Verify: `claude --version`
  - Requires Claude Pro or Max subscription

- [ ] **Install GitHub CLI**
  ```bash
  # macOS
  brew install gh

  # Linux
  sudo apt install gh

  # Windows
  winget install GitHub.cli
  ```
  - Authenticate: `gh auth login`
  - Verify: `gh auth status`

- [ ] **Install Node.js 20+**
  - Verify: `node --version`
  - Should be v20.x.x or higher

- [ ] **Install TypeScript globally** (optional)
  ```bash
  npm install -g typescript
  ```

### Repository Setup
- [ ] **Clone the repository**
  ```bash
  git clone <repo-url>
  cd ai-sdlc-blueprint
  ```

- [ ] **Install dependencies**
  ```bash
  npm install
  ```

- [ ] **Verify everything works**
  ```bash
  npm test
  npm run test:coverage
  ```

### Configuration
- [ ] **Set up Git identity**
  ```bash
  git config user.name "Your Name"
  git config user.email "your.email@example.com"
  ```

- [ ] **Configure Git to use main branch**
  ```bash
  git config --global init.defaultBranch main
  ```

- [ ] **Set up GitHub token** (for MCP)
  ```bash
  export GITHUB_TOKEN="your_github_personal_access_token"
  ```
  - Create token: https://github.com/settings/tokens
  - Scopes needed: `repo`, `read:org`

### Learning & Onboarding
- [ ] **Read the constitution**
  - Open `.specify/memory/constitution.md`
  - Understand non-negotiable principles

- [ ] **Read CLAUDE.md**
  - Open `CLAUDE.md`
  - Learn project standards and patterns

- [ ] **Read the blueprint**
  - Open `sdlc-blueprint-claude-speckit-github.md`
  - Understand the full workflow

- [ ] **Review architecture docs**
  - Open `docs/architecture.md`
  - Understand system design

- [ ] **Do a practice run**
  1. Start Claude Code: `claude`
  2. Create a test spec: `/specify`
  3. Generate plan: `/plan`
  4. Generate tasks: `/tasks`
  5. Create a small feature with TDD
  6. Open a PR
  7. See automated checks in action

---

## 🔄 Per-Feature Workflow (Repeatable)

### Specification Phase
- [ ] Launch Claude Code: `claude`
- [ ] Generate spec: `/specify`
- [ ] Review generated spec
- [ ] Refine and approve spec

### Planning Phase
- [ ] Generate plan: `/plan`
- [ ] Review architecture and approach
- [ ] Refine and approve plan

### Task Breakdown
- [ ] Generate tasks: `/tasks`
- [ ] Review task order and granularity
- [ ] Refine and approve tasks

### Implementation Phase
- [ ] Create feature branch
  ```bash
  git checkout -b feature/spec-NNN-description
  ```

- [ ] For each task:
  - [ ] Write tests FIRST
  - [ ] Run tests → verify they FAIL (Red)
  - [ ] Implement minimum code to pass (Green)
  - [ ] Refactor while keeping tests green
  - [ ] Run tests → verify they PASS
  - [ ] Commit with proper message

- [ ] Verify all tests pass
  ```bash
  npm test
  ```

- [ ] Verify coverage meets threshold
  ```bash
  npm run test:coverage
  ```

### Pull Request Phase
- [ ] Push to remote
  ```bash
  git push -u origin feature/spec-NNN-description
  ```

- [ ] Open PR with proper format
  ```bash
  gh pr create --title "feat: Description [spec-NNN]" \
    --body "Spec: .specify/specs/NNN-name/

  ## Changes
  - [What was implemented]

  ## Spec Compliance
  - [x] All acceptance criteria met
  - [x] Tests written first (TDD)
  - [x] Coverage above 80%"
  ```

- [ ] Wait for automated checks
  - [ ] Claude PR review posted
  - [ ] Spec governance check passed
  - [ ] TDD gate passed

- [ ] Address any feedback from automated review

- [ ] Request human review
  ```bash
  gh pr review --request @teammate
  ```

### Merge Phase
- [ ] All checks green
- [ ] At least 1 approval received
- [ ] Merge PR
  ```bash
  gh pr merge --squash
  ```

- [ ] Delete feature branch
  ```bash
  git branch -d feature/spec-NNN-description
  ```

- [ ] Pull latest main
  ```bash
  git checkout main
  git pull
  ```

---

## 🐛 Bug Fix Workflow

### Quick Fixes (No Spec Needed)
- [ ] Create bug fix branch
  ```bash
  git checkout -b fix/issue-123-description
  ```

- [ ] Write failing test that reproduces the bug

- [ ] Fix the bug (minimum code)

- [ ] Verify test now passes

- [ ] Open PR with `bug` label
  ```bash
  gh pr create --title "fix: Description" \
    --label bug \
    --body "Fixes #123"
  ```

---

## 🚨 Troubleshooting

### Tests Failing in CI but Pass Locally
- Ensure you've committed all files
- Check that dependencies match (`npm ci` in CI uses package-lock.json)
- Verify Node version matches CI

### Coverage Failing
- Run `npm run test:coverage` locally
- Check which files are uncovered
- Add tests to reach 80% threshold

### Spec Governance Failing
- Ensure PR body contains: `Spec: .specify/specs/NNN-name/`
- Verify the spec directory exists
- Verify spec has spec.md, plan.md, and tasks.md

### Claude PR Review Not Running
- Check that `ANTHROPIC_API_KEY` is set in repo secrets
- Verify workflow file has correct permissions
- Check GitHub Actions logs for errors

---

## ✅ Success Criteria

You're fully set up when:
- [x] All GitHub Actions workflows exist and are valid
- [x] All required directories exist
- [x] Documentation is complete
- [ ] Repository secrets are configured
- [ ] Branch protection is enabled
- [ ] All developers have tools installed
- [ ] Everyone has read the constitution
- [ ] At least one test feature has been completed end-to-end

---

**Need Help?**
- Check `README.md` for quick reference
- Read `sdlc-blueprint-claude-speckit-github.md` for details
- Open a GitHub issue with questions
