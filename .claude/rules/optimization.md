---
paths:
  - "src/modules/optimization/**/*.ts"
---

# Optimization / Context Trimmer Rules

- `ContextTrimmer.trim()` is the last step before returning data to the
  MCP client. It removes fields the LLM does not need (`transitionNodeID`,
  `isMask`, `exportSettings`, plugin metadata, etc.).
- The trimmer must be configurable via an allowlist of keys to keep, not a
  blocklist of keys to remove. New Figma API fields are excluded by default.
- Output must stay under the MCP output token budget (default 25 000 tokens).
  If the trimmed payload exceeds the limit, truncate the deepest tree levels
  first and append a `_truncated: true` flag.
- All trimming logic must be pure (no side effects, no I/O) so it is trivially
  unit-testable.
