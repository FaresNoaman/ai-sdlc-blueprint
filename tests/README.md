# Tests Directory

This directory contains all tests organized by type.

## Structure

```
tests/
├── unit/           # Unit tests (isolated, fast)
├── integration/    # Integration tests (multiple components)
├── e2e/            # End-to-end tests (full system)
└── README.md       # This file
```

## Test Location Strategy

You can choose one of two approaches:

### Option 1: Co-located Tests (Current)
- Tests live alongside source files in `src/`
- Example: `src/foo.ts` → `src/foo.test.ts`
- **Pros**: Easy to find related tests, promotes test coverage
- **Cons**: Mixes test and source files

### Option 2: Separate Test Directory (This directory)
- Tests live in this `tests/` directory mirroring `src/` structure
- Example: `src/foo.ts` → `tests/unit/foo.test.ts`
- **Pros**: Clean separation, easier to exclude from builds
- **Cons**: Need to maintain parallel structure

**Current Project**: Uses co-located tests in `src/`. This `tests/` directory is for integration and e2e tests.

## Running Tests

```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run only integration tests
npm run test:int

# Run only e2e tests
npm run test:e2e

# Run in watch mode (during development)
npm test -- --watch

# Run specific test file
npm test -- path/to/test.test.ts
```

## Test Structure

### Unit Tests (`tests/unit/`)
- Test individual functions/classes in isolation
- Mock external dependencies
- Fast execution (< 1 second per test)
- Should be the majority of your tests

Example:
```typescript
// tests/unit/calculator.test.ts
import { add } from '../../src/calculator';

describe('add', () => {
  it('should add two positive numbers', () => {
    expect(add(2, 3)).toBe(5);
  });

  it('should handle negative numbers', () => {
    expect(add(-1, -2)).toBe(-3);
  });
});
```

### Integration Tests (`tests/integration/`)
- Test multiple components working together
- May use real external services (database, APIs)
- Slower than unit tests
- Verify component interactions

Example:
```typescript
// tests/integration/user-service.test.ts
import { UserService } from '../../src/services/user-service';
import { database } from '../../src/database';

describe('UserService Integration', () => {
  beforeAll(async () => {
    await database.connect();
  });

  afterAll(async () => {
    await database.disconnect();
  });

  it('should create and retrieve a user', async () => {
    const user = await UserService.create({ name: 'Test' });
    const retrieved = await UserService.getById(user.id);
    expect(retrieved.name).toBe('Test');
  });
});
```

### E2E Tests (`tests/e2e/`)
- Test the entire system from user perspective
- Use real services or staging environment
- Slowest tests
- Verify critical user journeys

Example:
```typescript
// tests/e2e/user-registration.test.ts
import { startServer } from '../../src/server';

describe('User Registration Flow', () => {
  let server;

  beforeAll(async () => {
    server = await startServer();
  });

  afterAll(async () => {
    await server.close();
  });

  it('should complete full registration flow', async () => {
    // 1. Submit registration
    const response = await fetch('/api/register', {
      method: 'POST',
      body: JSON.stringify({ email: 'test@example.com' }),
    });
    expect(response.status).toBe(201);

    // 2. Verify email sent
    // 3. Click confirmation link
    // 4. Verify account active
  });
});
```

## Coverage Requirements

- **Minimum overall coverage**: 80%
- **Unit tests**: Should achieve 90%+ coverage
- **Integration tests**: Focus on critical paths
- **E2E tests**: Cover main user journeys

Coverage is enforced by the TDD Gate CI workflow.

## Best Practices

### 1. Test Naming
- Use descriptive test names: "should do X when Y"
- Group related tests with `describe` blocks
- Use `it` or `test` for individual test cases

### 2. Test Independence
- Each test should be runnable in isolation
- Use `beforeEach`/`afterEach` to reset state
- Don't rely on test execution order

### 3. Test Data
- Use factories/fixtures for test data
- Avoid hardcoding magic values
- Make test data intention-revealing

### 4. Mocking
- Mock external dependencies in unit tests
- Use real services in integration tests when possible
- Don't over-mock (leads to false positives)

### 5. Assertions
- One logical assertion per test (can be multiple `expect` calls)
- Test both success and failure cases
- Test edge cases and boundary conditions

## TDD Workflow

1. **Red**: Write a failing test
2. **Green**: Write minimum code to pass
3. **Refactor**: Improve code while keeping tests green

## Resources

- Jest Documentation: https://jestjs.io/
- Testing Best Practices: `docs/architecture.md`
- Project Constitution: `.specify/memory/constitution.md`
