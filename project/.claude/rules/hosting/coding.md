---
paths:
  - src/**/*.{ts,tsx}
  - src/**/*.test.{ts,tsx}
---

Before making any code changes, please check the existing code with `pnpm compile`.

- After modifying production code (non-test code), please check the updated code with `pnpm compile` and `pnpm test`. However, the tests should target the entire codebase, not specific files.
- After modifying test code, please check the updated code with `pnpm test`. However, the tests should target the entire codebase, not specific files.

Furthermore, when creating plans in Plan mode, please explicitly state that these commands should be executed.
