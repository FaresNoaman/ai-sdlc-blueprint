# Architecture Overview

## Project: AI SDLC Blueprint

### Purpose
This project demonstrates an AI-assisted Software Development Lifecycle using Claude Code, GitHub Spec Kit, and GitHub Actions to enforce spec-driven, test-driven development practices.

---

## System Architecture

### High-Level Components

```
┌─────────────────────────────────────────────────────────────┐
│                      Development Flow                        │
│                                                              │
│  Specification → Planning → Tasks → Implementation → PR     │
│  (Spec Kit)      (Spec Kit) (Spec Kit) (Claude Code)  (GH) │
└─────────────────────────────────────────────────────────────┘
```

### Technology Stack

- **Frontend**: React / Next.js (when applicable)
- **Backend**: Node.js or Python
- **Infrastructure**: AWS CDK (Infrastructure as Code only)
- **Database**: PostgreSQL / DynamoDB
- **CI/CD**: GitHub Actions
- **AI Agent**: Claude Code (Sonnet 4.5)

---

## Directory Structure

```
.
├── .github/
│   ├── workflows/          # CI/CD automation
│   │   ├── claude-pr-review.yml
│   │   ├── claude-issue-handler.yml
│   │   ├── spec-governance.yml
│   │   └── tdd-gate.yml
│   └── prompts/claude/     # Agent-specific prompts
│
├── .specify/
│   ├── memory/
│   │   └── constitution.md # Project governance rules
│   ├── specs/              # Feature specifications
│   │   └── {number-name}/
│   │       ├── spec.md
│   │       ├── plan.md
│   │       └── tasks.md
│   ├── templates/          # Spec Kit templates
│   └── scripts/            # Helper scripts
│
├── src/                    # Application source code
│   ├── frontend/           # (if applicable)
│   ├── backend/            # (if applicable)
│   └── infrastructure/     # CDK stacks
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── docs/
│   ├── architecture.md     # This file
│   └── adr/                # Architecture Decision Records
│
├── CLAUDE.md               # Claude Code context
├── .mcp.json               # MCP server configuration
└── package.json
```

---

## Development Workflow

### 1. Specification Phase
- Developer writes feature idea in natural language
- Spec Kit generates structured requirements
- Human reviews and approves

### 2. Planning Phase
- Spec Kit generates technical plan
- Architecture and data models defined
- Human reviews and approves

### 3. Task Breakdown
- Spec Kit generates ordered tasks
- Dependencies identified
- Human reviews and approves

### 4. Implementation (TDD Loop)
- Claude Code writes tests FIRST
- Tests must FAIL (Red phase)
- Claude Code implements minimum code (Green phase)
- Claude Code refactors (Refactor phase)
- Commit and repeat for each task

### 5. Pull Request & Review
- PR opened with spec reference
- Automated checks run:
  - Claude PR review
  - Spec governance check
  - TDD gate (tests + coverage)
- Human review
- Merge to main

---

## Quality Gates

### 1. Spec Governance Gate
- Every PR must reference a spec in `.specify/specs/`
- Spec must have: spec.md, plan.md, tasks.md
- Exceptions: bug fixes, chores, docs (via labels)

### 2. TDD Gate
- Every source file must have a corresponding test
- All tests must pass
- Minimum 80% code coverage required

### 3. Claude PR Review
- Automated code review on every PR
- Checks against CLAUDE.md standards
- Identifies security concerns and architecture violations

---

## Architecture Principles

### 1. Separation of Concerns
- Frontend never directly accesses AWS SDK
- Backend handles all business logic
- Infrastructure defined exclusively in IaC

### 2. Test-Driven Development
- Tests written before implementation
- Tests drive the design
- Comprehensive coverage required

### 3. Spec-Driven Development
- Specifications are the source of truth
- Code serves the spec, not the reverse
- No implementation without approved spec

### 4. Infrastructure as Code
- All AWS resources defined in CDK/Terraform
- No manual resource creation via console
- Version controlled and reviewable

### 5. Security by Default
- No secrets in code
- Input validation required
- Authentication required unless explicitly public
- Least-privilege IAM policies

---

## Scalability Considerations

### Current State (1-5 developers)
- Simple monorepo structure
- Single CI/CD pipeline
- Synchronous workflows

### Future Growth (5-20 developers)
- Consider workspace/monorepo tools (Nx, Turborepo)
- Parallel CI/CD pipelines
- Multiple Claude Code agents in parallel
- Distributed spec management

---

## Integration Points

### GitHub Actions
- Triggered on PR events
- Runs all quality gates
- Posts results as PR comments

### MCP Servers
- GitHub integration for issue/PR management
- Extensible for Slack, Jira, databases, etc.

### Claude Code
- Local development assistant
- Autonomous GitHub Action agent
- Reads CLAUDE.md for context

---

## Decision Log

See `docs/adr/` for detailed Architecture Decision Records.

---

## Maintenance

This architecture document should be updated when:
- Major technology choices change
- New quality gates are added
- Directory structure significantly changes
- New integration points are added

Last updated: 2026-02-16
