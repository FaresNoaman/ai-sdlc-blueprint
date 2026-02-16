# ADR 001: Enforce Test-Driven Development via CI/CD

## Status
Accepted

## Date
2026-02-16

## Context
We need a way to ensure that all code contributions follow Test-Driven Development (TDD) practices. Without enforcement, developers might skip writing tests or write tests after implementation, defeating the purpose of TDD.

TDD requires:
1. Writing tests FIRST
2. Watching tests FAIL
3. Implementing minimum code to pass
4. Refactoring while keeping tests green

## Decision
We will enforce TDD through a GitHub Actions workflow (`tdd-gate.yml`) that:

1. **Checks that tests exist for all source files**
   - For every changed file in `src/`, a corresponding test file must exist
   - Tests can be co-located (e.g., `src/foo.ts` → `src/foo.test.ts`)
   - Or in a separate tests directory (e.g., `src/foo.ts` → `tests/foo.test.ts`)

2. **Runs all tests and requires they pass**
   - Uses Jest as the test runner
   - Tests must pass before PR can be merged

3. **Enforces minimum code coverage of 80%**
   - Coverage measured by Jest's built-in coverage tool
   - Checks lines, branches, functions, and statements
   - Fails CI if coverage drops below threshold

## Consequences

### Positive
- **Guaranteed test coverage**: Every source file has tests
- **Prevents test-after-implementation**: Can't merge without tests
- **Maintains quality bar**: 80% coverage threshold prevents regressions
- **Visible in PR**: Developers see test failures immediately
- **Blocks merge**: Bad PRs can't be merged accidentally

### Negative
- **Strict enforcement might feel heavy-handed**: Some developers may resist
- **80% threshold is arbitrary**: May need adjustment based on project needs
- **Doesn't prevent gaming**: Developers could write meaningless tests to pass threshold
- **Build time increases**: Running tests and coverage adds CI time

### Mitigations
- Constitution clearly explains the "why" behind TDD
- Exemptions available for certain changes (via PR labels)
- Coverage threshold can be adjusted if needed
- Test quality reviewed by humans in PR review (Claude + human)

## Alternatives Considered

### 1. Rely on developer discipline
**Rejected**: Too easy to skip tests under pressure

### 2. Make tests optional but encouraged
**Rejected**: Doesn't align with our strict TDD principle

### 3. Lower coverage threshold (e.g., 60%)
**Rejected**: 80% is industry standard for TDD projects

### 4. Use pre-commit hooks instead of CI
**Rejected**: Hooks can be bypassed; CI is authoritative

## Implementation
- Workflow file: `.github/workflows/tdd-gate.yml`
- Test runner: Jest with ts-jest
- Configuration: `jest.config.cjs`
- Coverage reporters: json-summary and text

## Notes
- This ADR is part of the initial project setup
- If TDD enforcement proves too strict, we can revisit and adjust
- Consider adding property-based testing in the future for critical paths

## References
- Project Constitution: `.specify/memory/constitution.md`
- TDD Gate Workflow: `.github/workflows/tdd-gate.yml`
- SDLC Blueprint: `sdlc-blueprint-claude-speckit-github.md`
