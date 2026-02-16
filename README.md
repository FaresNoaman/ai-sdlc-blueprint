# AI SDLC Blueprint

> An AI-assisted Software Development Lifecycle using **Claude Code**, **GitHub Spec Kit**, and **GitHub Actions** to enforce spec-driven, test-driven development practices.

---

## 🎯 What This Is

This project demonstrates a complete SDLC workflow for small teams (1-5 developers) that leverages:
- **Spec-driven development**: Every feature starts with a specification
- **Test-driven development**: Tests are written before implementation
- **AI assistance**: Claude Code helps with specification, planning, and implementation
- **Automated quality gates**: GitHub Actions enforce standards before merge

## 📖 Full Documentation

See **[sdlc-blueprint-claude-speckit-github.md](./sdlc-blueprint-claude-speckit-github.md)** for the complete blueprint with detailed instructions.

---

## 🚀 Quick Start

### Prerequisites

1. **Claude Code CLI** (requires Claude Pro or Max subscription)
   ```bash
   npm install -g @anthropic-ai/claude-code
   ```

2. **GitHub CLI**
   ```bash
   # macOS
   brew install gh

   # Linux
   sudo apt install gh

   # Authenticate
   gh auth login
   ```

3. **Node.js 20+** and **TypeScript**

### Setup

1. **Clone the repository**
   ```bash
   git clone <your-repo>
   cd ai-sdlc-blueprint
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure GitHub repository secrets**
   - Add `ANTHROPIC_API_KEY` to your repository secrets
   - Settings → Secrets and variables → Actions → New repository secret

4. **Set up branch protection** (recommended)
   - Protect `main` branch
   - Require PR reviews
   - Require status checks to pass

### Usage

#### Start a New Feature

1. **Launch Claude Code**
   ```bash
   claude
   ```

2. **Generate a specification**
   ```
   > /specify
   ```
   Describe your feature in natural language. Review and approve the generated spec.

3. **Generate implementation plan**
   ```
   > /plan
   ```
   Review the technical architecture and approach.

4. **Generate tasks**
   ```
   > /tasks
   ```
   Review the ordered task breakdown.

5. **Create feature branch**
   ```bash
   git checkout -b feature/spec-001-your-feature
   ```

6. **Implement with TDD**
   For each task:
   - Write tests FIRST
   - Run tests (they should FAIL)
   - Implement minimum code to pass
   - Refactor while keeping tests green
   - Commit

7. **Open Pull Request**
   ```bash
   gh pr create --title "feat: Your feature [spec-001]" \
     --body "Spec: .specify/specs/001-your-feature/"
   ```

8. **Automated checks run**
   - Claude PR review
   - Spec governance check
   - TDD gate (tests + coverage)

9. **Human review & merge**

---

## 🏗️ Project Structure

```
.
├── .github/
│   ├── workflows/          # CI/CD automation
│   │   ├── claude-pr-review.yml      # Auto PR review
│   │   ├── claude-issue-handler.yml  # @claude issue-to-PR
│   │   ├── spec-governance.yml       # Spec enforcement
│   │   └── tdd-gate.yml             # Test coverage enforcement
│   └── prompts/claude/     # Agent-specific prompts
│
├── .specify/
│   ├── memory/
│   │   └── constitution.md # Project governance rules (READ THIS!)
│   ├── specs/              # Feature specifications
│   ├── templates/          # Spec Kit templates
│   └── scripts/            # Helper scripts
│
├── src/                    # Application source code
├── tests/                  # Test files (unit, integration, e2e)
├── docs/                   # Documentation
│   ├── architecture.md     # System architecture
│   └── adr/                # Architecture Decision Records
│
├── CLAUDE.md               # Claude Code persistent context
├── .mcp.json               # MCP server configuration
├── README.md               # This file
└── package.json
```

---

## 🧪 Testing

```bash
# Run all tests
npm test

# Run with coverage (must be >= 80%)
npm run test:coverage

# Run integration tests
npm run test:int

# Run e2e tests
npm run test:e2e

# Watch mode
npm test -- --watch
```

---

## 📋 Key Principles

### 1. Spec-First Development
Every feature MUST have an approved specification before implementation begins. No exceptions.

### 2. Test-Driven Development
All code MUST follow strict TDD:
1. Write tests FIRST
2. Tests must FAIL
3. Implement minimum code to pass
4. Refactor while keeping tests green

### 3. Architecture Boundaries
- Frontend never directly accesses AWS SDK
- Backend handles all business logic
- Infrastructure defined exclusively in CDK/IaC

### 4. Quality Gates
- Minimum 80% test coverage
- All tests must pass
- Every PR must reference a spec (except bugs/chores/docs)
- At least 1 human review required

See **[.specify/memory/constitution.md](./.specify/memory/constitution.md)** for full governance rules.

---

## 🤖 AI Workflows

### Automated PR Review
Every PR automatically receives an AI code review checking:
- Spec reference and compliance
- TDD compliance (tests written first)
- Security concerns
- Architecture adherence
- Code quality

### Issue-to-PR Automation
Comment `@claude` on any issue to trigger autonomous implementation:
1. Claude analyzes the issue
2. Creates a spec (if needed)
3. Implements with TDD
4. Opens a PR for review

---

## 🔒 Security

- **Never commit secrets**: Use environment variables or AWS Secrets Manager
- **Input validation required**: All user input must be validated
- **Authentication by default**: All API endpoints require auth unless explicitly public
- **Least-privilege IAM**: Infrastructure follows principle of least privilege

---

## 📚 Resources

- **[SDLC Blueprint](./sdlc-blueprint-claude-speckit-github.md)**: Complete workflow documentation
- **[Architecture](./docs/architecture.md)**: System architecture and design decisions
- **[Constitution](./.specify/memory/constitution.md)**: Project governance rules
- **[CLAUDE.md](./CLAUDE.md)**: Claude Code context and conventions

---

## 🛠️ Technology Stack

- **AI Agent**: Claude Code (Sonnet 4.5)
- **Frontend**: React / Next.js
- **Backend**: Node.js or Python
- **Infrastructure**: AWS CDK (IaC only)
- **Database**: PostgreSQL / DynamoDB
- **CI/CD**: GitHub Actions
- **Testing**: Jest with ts-jest
- **Language**: TypeScript (strict mode)

---

## 📊 Workflow Diagram

```
┌─────────────────────────────────────────────────────────┐
│                  SDLC PIPELINE                          │
│                                                         │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐          │
│  │ SPECIFY  │──▶│   PLAN   │──▶│  TASKS   │──▶       │
│  │ (Spec    │   │ (Tech    │   │ (Task    │          │
│  │  Kit)    │   │  Design) │   │  Break)  │          │
│  └────┬─────┘   └────┬─────┘   └────┬─────┘          │
│       │              │              │                  │
│    REVIEW         REVIEW         REVIEW                │
│                                                         │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐          │
│  │IMPLEMENT │──▶│    PR    │──▶│  MERGE   │          │
│  │  (TDD)   │   │ (Review) │   │          │          │
│  └──────────┘   └──────────┘   └──────────┘          │
│                       │                                │
│            ┌──────────┴──────────┐                    │
│            │  AUTOMATED GATES    │                    │
│            │  • Claude Review    │                    │
│            │  • Spec Check       │                    │
│            │  • TDD Gate         │                    │
│            └─────────────────────┘                    │
└─────────────────────────────────────────────────────────┘
```

---

## 🤝 Contributing

1. Read the [Constitution](./.specify/memory/constitution.md)
2. Create a spec for your feature
3. Follow TDD rigorously
4. Reference your spec in the PR
5. Wait for automated checks to pass
6. Request human review

---

## 📄 License

MIT (see LICENSE file)

---

## 🆘 Support

- GitHub Issues: Use issue templates
- Documentation: See `docs/` directory
- Blueprint: `sdlc-blueprint-claude-speckit-github.md`

---

**Last Updated**: 2026-02-16
