---
trigger: model_decision
description: Apply TypeScript backend testing standards for maintainability and reliability WHEN writing TypeScript backend tests. Focus on setup/teardown hooks and avoiding repetition.
globs: *.spec.*, *.test.*
---

Setup and Teardown:
- Use setup hooks (`beforeEach`, `beforeAll`).
- Use teardown hooks (`afterEach`, `afterAll`).

Code Quality:
- Avoid code repetition in tests.
- Keep tests independent and focused.