---
paths:
  - "**/*.test.ts"
  - "**/*.spec.ts"
  - "**/__tests__/**"
---

# Testing Rules

- Write specs first (spec-driven development). Define the interface contract
  and expected behavior before writing the implementation.
- Use Jest with ts-jest. Tests live next to the module they cover
  (`Foo.ts` → `Foo.test.ts`) or in a `__tests__/` directory.
- Mock external boundaries: Figtree CLI output, Figma REST API responses,
  and filesystem operations. Never make real network calls in tests.
- Target 80 %+ line coverage for all modules. Integration tests must cover
  the extraction → caching → mapping → trimming pipeline end-to-end.
- Use `jest.spyOn` or dependency injection to swap concrete classes for
  mocks — never patch private internals.
