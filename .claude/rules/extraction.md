---
paths:
  - "src/modules/extraction/**/*.ts"
  - "src/interfaces/**/*.ts"
---

# Extraction Module Rules

- `FigtreeTokenExtractor` implements `ITokenExtractor`. It shells out to the
  Figtree CLI via `child_process.execFile` (never `exec` with string
  concatenation — command-injection risk).
- `FigmaFrameFetcher` implements `IFrameFetcher`. It calls the Figma REST API
  with the PAT from config; use axios or Node fetch, never hardcode URLs.
- Both classes receive their configuration through constructor injection
  (`ConfigLoader` instance), not by reading env vars directly.
- Flatten token output to a single-level key-value map (semantic name → resolved
  value). Strip collection metadata the LLM does not need.
- When simplifying frame trees, recurse depth-first and keep only layout-
  relevant properties: Auto Layout direction, padding, gap, alignment,
  sizing mode, and child order. Drop decorative metadata (`isMask`,
  `transitionNodeID`, etc.).
