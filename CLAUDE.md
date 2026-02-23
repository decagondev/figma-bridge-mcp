# Figma-Bridge MCP

## Overview

Node.js MCP server that gives Cursor and Claude Code access to Figma design
tokens and frame structures on the Personal/Starter tier. The agent calls
`sync_design_context`, `get_frame_structure`, or `refresh_cache` over stdio
and receives optimized, semantically named JSON it can use to generate code
without hardcoded hex values.

## Stack

- Language: TypeScript (strict mode)
- Runtime: Node.js >= 18
- MCP SDK: `@modelcontextprotocol/sdk`
- Token extraction: Figtree CLI
- Figma access: REST API with a Personal Access Token (env var `FIGMA_PAT`)
- Testing: Jest + ts-jest
- Package manager: npm

## Project Structure

- `src/index.ts` — entry point
- `src/server/` — MCP server setup, tool registration
- `src/interfaces/` — `ITokenExtractor`, `IFrameFetcher` (abstraction contracts)
- `src/modules/extraction/` — `FigtreeTokenExtractor`, `FigmaFrameFetcher`
- `src/modules/caching/` — `LocalCache` (.figma-cache/ JSON files)
- `src/modules/mapping/` — `TokenMapper` (Figma vars → Tailwind/CSS)
- `src/modules/optimization/` — `ContextTrimmer` (strip redundant metadata)
- `src/modules/config/` — config loader (PAT, figtree.config.json)
- `config/figtree.config.json` — Figtree variable-mapping config

## Commands

- `npm install` — install dependencies
- `npm start` — build and start the MCP server
- `npm run build` — compile TypeScript to dist/
- `npm test` — run all Jest tests
- `npm run test:watch` — Jest in watch mode
- `npm run test:coverage` — Jest with coverage report
- `npm run lint` — run linter

## Verification

After making changes, always run:
1. `npm run build` (must compile without errors)
2. `npm test` (all tests must pass)

## Architecture Principles

- **SOLID everywhere** — single responsibility per module, depend on
  abstractions (`ITokenExtractor`, `IFrameFetcher`), inject via constructors.
- **Modular boundaries** — extraction, caching, mapping, optimization, and
  server are independent modules with minimal public APIs.
- **Client-agnostic** — the server uses MCP stdio transport; never couple to
  Cursor- or Claude-Code-specific APIs.

## Code Conventions

- TypeScript strict mode; no `any` unless explicitly justified.
- JSDoc on every exported class, function, and method (`@param`, `@returns`,
  `@throws`). No single-line `//` comments in production code.
- Validate and sanitize all external input (Figma API responses, env vars,
  CLI output). Never trust third-party data.
- Secrets (`FIGMA_PAT`) live in env vars only; never commit them.

## Git Workflow

- Branch from `main`: `feature/`, `fix/`, `refactor/`, `test/`, `docs/`
- Semantic commit prefixes: `feat:`, `fix:`, `docs:`, `test:`, `refactor:`
- Write specs/tests first, then implement to pass them.
- All tests and type-checks must pass before merge.

## Reference Docs

Read whichever of these are relevant before starting a task:

- `docs/PRD.md` — full PRD with epics, user stories, feature branches, and
  commit plans
- `docs/DIAGRAMS..md` — architecture flowcharts, sequence diagrams, class
  diagram (Mermaid)
- `README.md` — setup instructions for Cursor and Claude Code, MCP tool
  reference, project structure
