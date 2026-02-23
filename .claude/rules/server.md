---
paths:
  - "src/server/**/*.ts"
---

# MCP Server Rules

- The server communicates exclusively over stdio transport. Never add
  HTTP/WebSocket listeners; clients (Cursor, Claude Code) spawn the process.
- Register tools via the MCP SDK `server.setRequestHandler` pattern.
  Each tool handler receives validated input and returns structured JSON.
- Inject all dependencies (extractor, mapper, cache, trimmer) through
  the constructor or a factory — never instantiate concrete classes inside
  handlers.
- Wrap every handler body in try/catch and return structured MCP error
  responses. Never let unhandled exceptions crash the server process.
- Keep tool handler functions thin: validate input, delegate to a module,
  post-process with ContextTrimmer, return result.
