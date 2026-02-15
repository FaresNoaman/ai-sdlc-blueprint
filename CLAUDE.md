# CLAUDE.md — Project Context

This file defines persistent rules for Claude Code.

---

## Tech Stack

- Frontend: React / Next.js
- Backend: Node.js or Python
- Infrastructure: AWS CDK (IaC only)
- Database: PostgreSQL / DynamoDB
- CI/CD: GitHub Actions

---

## Development Rules

ALWAYS:
- Read the relevant spec in `.specify/specs/`
- Follow TDD (Red → Green → Refactor)
- Respect architecture boundaries
- Write minimal code to satisfy tests

NEVER:
- Write implementation before tests
- Create AWS resources manually
- Commit secrets
- Push directly to main

---

## Test Commands

npm run test
npm run test:coverage

Coverage must remain >= 80%.

---

## PR Requirements

Every PR must include:

Spec: .specify/specs/{number-name}/
