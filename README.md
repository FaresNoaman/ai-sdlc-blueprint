# AI SDLC Blueprint

> A complete AI-assisted Software Development Lifecycle template using **Claude Code**, **GitHub Spec Kit**, and **GitHub Actions** to enforce spec-driven, test-driven development.

[![TDD Enforcement](https://img.shields.io/badge/TDD-Enforced-success)](.)
[![Spec Driven](https://img.shields.io/badge/Spec--Driven-Enabled-blue)](.)
[![AI Reviews](https://img.shields.io/badge/AI-Claude%20Reviews-purple)](.)

---

## 🎯 What Is This?

This is a **production-ready template** for small teams (1-5 developers) that want to build software with:

- ✅ **AI-Powered Code Reviews** - Claude automatically reviews every PR
- ✅ **Spec-Driven Development** - No code without approved specifications
- ✅ **Test-Driven Development** - 80% test coverage enforced
- ✅ **Automated Quality Gates** - Tests, specs, and coverage checked automatically
- ✅ **Issue-to-PR Automation** - Comment `@claude` on issues for auto-implementation

---

## 🚀 Quick Start (3 Steps)

### **Step 1: Use This Template**

Click **"Use this template"** on GitHub, or clone it:

```bash
# Clone this repository
git clone https://github.com/YOUR_USERNAME/ai-sdlc-blueprint.git my-project
cd my-project

# Remove git history and start fresh
rm -rf .git
git init
git add .
git commit -m "Initial commit from AI SDLC Blueprint"

# Create new GitHub repository
gh repo create my-project --public --source=. --remote=origin --push
```

### **Step 2: Configure GitHub Repository**

#### **A. Add ANTHROPIC_API_KEY Secret**

1. Go to: `https://github.com/YOUR_USERNAME/my-project/settings/secrets/actions`
2. Click **"New repository secret"**
3. Add:
   - **Name:** `ANTHROPIC_API_KEY`
   - **Value:** Your Anthropic API key from https://console.anthropic.com

> ⚠️ **CRITICAL:** Without this secret, Claude PR reviews won't work!

#### **B. Create GitHub Labels** (Optional but recommended)

```bash
gh label create "chore" --description "Maintenance and setup" --color "d4c5f9"
gh label create "docs" --description "Documentation" --color "0075ca"
gh label create "test" --description "Testing" --color "bfd4f2"
```

These labels let you skip spec/TDD requirements for non-feature work.

### **Step 3: Customize for Your Project**

#### **Edit `CLAUDE.md`** (Required)

This file provides context to Claude Code. Update it with your project details:

```markdown
# CLAUDE.md — Project Context

## Project Overview
[YOUR PROJECT - what it does, who it's for]

## Tech Stack
- Frontend: [YOUR FRONTEND TECH]
- Backend: [YOUR BACKEND TECH]
- Database: [YOUR DATABASE]

## Development Rules
- ALWAYS read specs in `.specify/specs/` before coding
- ALWAYS write tests FIRST (TDD)
- NEVER commit secrets
- NEVER push directly to `main`
```

#### **Edit `.specify/memory/constitution.md`** (Required)

This defines your project's governance rules:

```markdown
# Project Constitution

## Identity
- Project: [YOUR PROJECT NAME]
- Stack: [YOUR TECH STACK]
- Team: [TEAM SIZE] developers

## Non-Negotiable Principles
[YOUR PROJECT-SPECIFIC RULES]
```

#### **Update `package.json`** (If using Node.js)

```json
{
  "name": "your-project-name",
  "version": "1.0.0",
  "description": "Your project description",
  "scripts": {
    "test": "jest",
    "test:coverage": "jest --coverage --coverageReporters=json-summary --coverageReporters=text"
  }
}
```

---

## 📖 How to Use the Workflow

### **1. Create a Feature Specification**

```bash
# Start Claude Code
claude

# Inside Claude Code terminal
> /specify
```

Claude will ask you to describe your feature. Example:
```
"Create a user authentication API with email/password login, JWT tokens, and password reset functionality."
```

**Result:** Creates `.specify/specs/001-user-auth/spec.md` with user stories and acceptance criteria.

**Your job:** Review the spec, add edge cases, refine requirements.

---

### **2. Generate Implementation Plan**

```bash
# In Claude Code
> /plan
```

**Result:** Creates `.specify/specs/001-user-auth/plan.md` with:
- Architecture design
- API contracts
- Database schema
- Technology choices

**Your job:** Review architecture, verify tech choices, approve design.

---

### **3. Break Down Into Tasks**

```bash
# In Claude Code
> /tasks
```

**Result:** Creates `.specify/specs/001-user-auth/tasks.md` with ordered, dependency-aware tasks.

**Your job:** Review task sequence and granularity.

---

### **4. Implement with TDD**

```bash
# Create feature branch
git checkout -b feature/spec-001-user-auth

# In Claude Code, for EACH task:
> Read task 1 from .specify/specs/001-user-auth/tasks.md
> Write the tests for this task ONLY. Do not implement yet.
```

**Claude writes tests → You review tests → Tests FAIL (Red)**

```bash
# Run tests to verify they fail
npm test
```

**Then implement:**
```bash
# In Claude Code
> Now implement the minimum code to make these tests pass
```

**Tests PASS (Green) → Refactor → Commit**

```bash
npm test  # Should pass
git add .
git commit -m "feat(auth): implement login endpoint [spec-001]"
```

**Repeat for each task.**

---

### **5. Open Pull Request**

```bash
# Push your branch
git push origin feature/spec-001-user-auth

# Create PR with spec reference
gh pr create \
  --title "feat: User authentication [spec-001]" \
  --body "Spec: .specify/specs/001-user-auth/

## Changes
- Implemented email/password login
- JWT token generation
- Password reset flow

## Testing
- All tests passing
- Coverage: 85%

## Checklist
- [x] Tests written first (TDD)
- [x] All tests passing
- [x] Spec compliance verified"
```

---

### **6. Automated Checks Run**

GitHub Actions will automatically:

#### ✅ **Claude PR Review**
- Reads your CLAUDE.md standards
- Checks spec reference
- Verifies TDD compliance
- Identifies security issues
- Posts review comments

#### ✅ **Spec Governance Check**
- Ensures PR references a spec
- Verifies spec files exist (spec.md, plan.md, tasks.md)
- **Skips** for PRs labeled: `bug`, `chore`, `docs`

#### ✅ **TDD Gate**
- Checks every source file has tests
- Runs all tests
- Enforces 80% coverage minimum
- **Skips** for PRs labeled: `chore`, `docs`, `test`

---

### **7. Review & Merge**

1. **Read Claude's review** - Address any issues
2. **Wait for all checks** - Must be green
3. **Human review** - At least 1 approval
4. **Merge!**

```bash
gh pr merge --squash
```

---

## 🤖 Bonus: Issue-to-PR Automation

Create an issue and let Claude implement it:

```bash
# Create issue
gh issue create \
  --title "Add password strength validator" \
  --body "Requirements:
- Check password length (min 8 chars)
- Require uppercase, lowercase, number, symbol
- Return validation errors
- Add tests"

# Comment to trigger Claude
gh issue comment <ISSUE_NUMBER> --body "@claude please implement this"
```

**Claude will:**
1. Analyze the issue
2. Create a specification (if needed)
3. Create a feature branch
4. Implement with TDD
5. Open a PR
6. Link back to the issue

---

## 📁 Project Structure

```
your-project/
├── .github/
│   ├── workflows/              # CI/CD automation
│   │   ├── claude-pr-review.yml       # Automated PR reviews
│   │   ├── claude-issue-handler.yml   # @claude issue-to-PR
│   │   ├── spec-governance.yml        # Spec enforcement
│   │   └── tdd-gate.yml              # Test coverage enforcement
│   └── prompts/claude/         # Claude agent prompts
│       ├── pr-review.md              # PR review instructions
│       └── issue-to-pr.md            # Issue handling instructions
│
├── .specify/
│   ├── memory/
│   │   └── constitution.md     # Project governance rules
│   ├── specs/                  # Feature specifications
│   │   └── {number-name}/      # Each feature gets a directory
│   │       ├── spec.md         # Requirements & user stories
│   │       ├── plan.md         # Technical design
│   │       └── tasks.md        # Implementation tasks
│   └── templates/              # Spec Kit templates
│
├── src/                        # Your application code
│
├── tests/                      # Test files
│   ├── unit/                   # Unit tests
│   ├── integration/            # Integration tests
│   └── e2e/                    # End-to-end tests
│
├── docs/
│   ├── architecture.md         # System architecture
│   └── adr/                    # Architecture Decision Records
│
├── CLAUDE.md                   # Claude Code context
├── .mcp.json                   # MCP server configuration
├── .gitignore                  # Git ignore patterns
├── package.json                # Dependencies & scripts
├── jest.config.cjs             # Test configuration
└── tsconfig.json               # TypeScript configuration
```

---

## 🛠️ Technology Stack

### **Required**
- **Claude Pro or Max** - $20-100/month per developer (for Claude Code CLI)
- **GitHub** - Free tier works, Team tier recommended for branch protection
- **Git** - Version control

### **Optional (depending on your project)**
- **Node.js 20+** - If building Node.js applications
- **TypeScript** - Strongly recommended for type safety
- **Jest** - Test runner (included in template)
- **AWS Account** - If deploying to AWS

---

## ⚙️ Configuration Files

### **CLAUDE.md** (Required)
Provides context to Claude Code in every session. Update with your project details.

### **.specify/memory/constitution.md** (Required)
Defines non-negotiable project rules. Claude follows these principles in all work.

### **.mcp.json** (Optional)
Configures MCP (Model Context Protocol) servers for external integrations like GitHub, Slack, databases.

### **GitHub Actions Workflows** (Required)
- All in `.github/workflows/`
- Pre-configured and ready to use
- Only needs `ANTHROPIC_API_KEY` secret

---

## 🎨 Labels for Smart Workflow Control

Use GitHub labels to control which checks run:

| Label | Effect |
|-------|--------|
| `chore` | Skips spec governance + TDD gate |
| `docs` | Skips spec governance + TDD gate |
| `test` | Skips TDD gate only |
| `bug` | Skips spec governance only |

**Example:**
```bash
gh pr create --title "docs: Update README" --label "docs"
# Spec and TDD checks will be skipped
```

---

## 📊 Quality Standards Enforced

| Standard | Requirement | Enforced By |
|----------|-------------|-------------|
| Test Coverage | ≥ 80% | TDD Gate workflow |
| Test Existence | Every source file needs tests | TDD Gate workflow |
| Spec Reference | Every feature PR must reference spec | Spec Governance workflow |
| Spec Completeness | Must have spec.md, plan.md, tasks.md | Spec Governance workflow |
| Code Review | Claude reviews every PR | Claude PR Review workflow |
| Human Review | At least 1 approval | GitHub branch protection |

---

## 🔍 Troubleshooting

### **Claude PR Review Not Running**

**Check:**
1. `ANTHROPIC_API_KEY` secret exists in repository settings
2. Workflow file uses correct model name (`claude-sonnet-4-5-20250929`)
3. GitHub Actions are enabled (Settings → Actions → Allow all actions)

**View logs:**
```bash
gh run list --workflow="claude-pr-review.yml"
gh run view <RUN_ID> --log
```

### **TDD Gate Failing**

**Common causes:**
- Source file added without corresponding test file
- Tests not running properly
- Coverage below 80%

**Debug:**
```bash
# Run tests locally
npm test
npm run test:coverage

# Check which files need tests
git diff main --name-only | grep '^src/'
```

**Solution:**
- Add test file for each source file
- Or add `chore`/`test` label to skip check

### **Spec Governance Blocking PR**

**Cause:** PR description doesn't reference a spec

**Solution:**
```bash
# Add spec reference to PR description
gh pr edit --body "Spec: .specify/specs/001-feature-name/

[rest of description]"

# OR add exemption label
gh pr edit --add-label "chore"  # or "docs", "bug"
```

---

## 📚 Additional Resources

- **[COMPLETE-GUIDE.md](./COMPLETE-GUIDE.md)** - Detailed 800-line guide with examples
- **[SETUP-CHECKLIST.md](./SETUP-CHECKLIST.md)** - Step-by-step setup checklist
- **[docs/architecture.md](./docs/architecture.md)** - System architecture documentation
- **[tests/README.md](./tests/README.md)** - Testing guidelines and best practices

### **External Links**
- [Claude Code Documentation](https://docs.anthropic.com/claude-code)
- [GitHub Spec Kit](https://github.com/github/spec-kit)
- [Jest Documentation](https://jestjs.io)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

---

## 🎯 Success Checklist

After setup, verify everything works:

- [ ] ANTHROPIC_API_KEY secret added
- [ ] CLAUDE.md customized for your project
- [ ] Constitution updated with your rules
- [ ] Created first spec with `/specify`
- [ ] Generated plan with `/plan`
- [ ] Created tasks with `/tasks`
- [ ] Implemented feature with TDD
- [ ] Opened PR and saw Claude review
- [ ] All checks passed (Claude review, spec governance, TDD gate)
- [ ] Merged PR successfully

---

## 🆘 Getting Help

- **Issues:** Open an issue in this repository
- **Discussions:** Use GitHub Discussions
- **Documentation:** Check COMPLETE-GUIDE.md for detailed explanations

---

## 📄 License

MIT License - Use this template freely for your projects

---

## 🙏 Credits

- **Claude Code** by Anthropic
- **GitHub Spec Kit** by GitHub
- **Template** assembled and battle-tested by the community

---

**Ready to build with AI assistance?** Start with `/specify` and let Claude help you build better software! 🚀
