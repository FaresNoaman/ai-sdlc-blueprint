# Project Constitution

## Project Identity
- Name: AI SDLC Blueprint
- Purpose: AI-assisted, spec-driven, TDD-enforced development system
- Team Size: 1–5 developers
- Cloud: AWS (Infrastructure as Code only)

---

## 1. Spec-First Development (NON-NEGOTIABLE)

- No feature implementation begins without an approved specification.
- Every feature must live under `.specify/specs/{number-name}/`
- Each spec must include:
  - spec.md
  - plan.md
  - tasks.md
- Every Pull Request MUST reference its spec:
  Spec: .specify/specs/{number-name}/

Code serves the spec. The spec is the source of truth.

---

## 2. Mandatory Test-Driven Development (TDD)

All features must follow strict TDD:

1. Write tests FIRST
2. Tests must FAIL
3. Implement minimum code to pass
4. Refactor while keeping tests green

Rules:
- No implementation code before tests exist.
- Every new source file must have a test file.
- Minimum coverage: 80%.

---

## 3. Architecture Boundaries

- Frontend never directly accesses AWS SDK.
- Backend handles all business logic.
- Infrastructure must be defined using IaC (CDK/Terraform).
- No AWS Console manual resource creation.
- All DB schema changes require migrations.

---

## 4. Code Quality Standards

- TypeScript strict mode (no `any`)
- All public functions documented
- Max file length: 300 lines
- No unhandled async promises

---

## 5. Security Rules

- No secrets committed to repo.
- Use environment variables or AWS Secrets Manager.
- All API endpoints require authentication unless explicitly public.
- Input validation required on all user input.

---

## 6. Git Workflow

- No direct push to main.
- All work via feature branches:
  feature/spec-{number}-{name}
- All changes require:
  - Passing CI checks
  - Passing TDD gate
  - 1 human review