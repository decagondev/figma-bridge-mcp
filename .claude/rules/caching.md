---
paths:
  - "src/modules/caching/**/*.ts"
---

# Caching Module Rules

- Cache files live in `.figma-cache/` at the project root (git-ignored).
  Each entry is a JSON file keyed by a deterministic hash of the request
  parameters (file ID, frame ID, collection name).
- Every cache entry includes a `cachedAt` ISO-8601 timestamp. Invalidation
  is timestamp-based: entries older than the configured TTL are treated as
  stale and re-fetched.
- The `refresh_cache` tool must call `LocalCache.invalidate()` for all keys,
  then trigger a fresh extraction pass.
- Use `fs/promises` (async) for all file I/O — never block the event loop
  with synchronous reads/writes.
- Cache reads that fail (missing file, corrupt JSON) must return a cache miss,
  not throw. Log the error and proceed to a fresh fetch.
