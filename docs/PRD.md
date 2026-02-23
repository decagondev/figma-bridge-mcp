# Product Requirements Document (PRD): Figma-Bridge MCP

## 1. Objective
To bridge the gap between Figma designs and Cursor for users on the Figma Personal/Starter tier, enabling "agentic" UI development where the LLM can autonomously pull resolved design tokens and layout structures.

## 2. Problem Statement
- **Tier Restrictions**: The official Figma MCP and REST Variables API are often gated behind Enterprise/Pro features.
- **Token Accuracy**: Standard JSON exports often provide raw hex codes but lose the "semantic" meaning (e.g., primary-button-bg), leading to inconsistent code.
- **Manual Friction**: Developers have to manually export files or copy-paste CSS constantly, breaking the "flow" of agentic coding.

## 3. Scope and Assumptions
- Target Users: Developers using Figma Personal/Starter tier and Cursor AI for code generation.
- Technologies: Node.js for MCP server, Figtree CLI for token extraction, MCP SDK for tool integration.
- Dependencies: Figma Personal Access Token (PAT) provided by user; no enterprise features required.
- Out of Scope: Full Figma file editing; support for non-Frame nodes; advanced animations or prototypes.
- Success Metrics:
  - Zero Hardcoding: No raw hex codes generated in Cursor; all values must come from the synced tokens.
  - Reduced Latency: Syncing a frame to a code-ready context should take < 3 seconds.
  - Adoption: Measured by successful tool calls in Cursor without errors.

## 4. Technical Principles
- **SOLID Principles**:
  - **Single Responsibility**: Each module/class handles one concern (e.g., token extraction separate from API calls).
  - **Open-Closed**: Modules extensible without modification (e.g., via interfaces for future extractors).
  - **Liskov Substitution**: Subclasses interchangeable (e.g., mock extractors for testing).
  - **Interface Segregation**: Small, focused interfaces (e.g., separate for token syncing and frame fetching).
  - **Dependency Inversion**: High-level modules depend on abstractions (e.g., inject dependencies via constructors).
- **Modular Design**: Break into independent modules (e.g., extraction, caching, mapping, server).
- **Code Style**: Use TypeScript for type safety. JSDoc-style comments for all functions/classes (no inline one-liners; comprehensive docs at top of functions).
- **Git Workflow with Spec-Driven Development**:
  - **Main Branch**: Stable production code.
  - **Feature Branches**: Branched from main (e.g., `feature/sync-design-context`).
  - **Workflow Steps**:
    1. Create feature branch.
    2. Write specs/tests first (using Jest for unit/integration tests).
    3. Implement code to pass specs.
    4. Commit granularly (e.g., "Add interface for TokenExtractor").
    5. Push branch, create PR with description linking to user story.
    6. Merge after review/tests pass.
    7. Use semantic commits: `feat:`, `fix:`, `docs:`, `test:`, `refactor:`.
  - **Branching Strategy**: Git Flow lite – main for releases, develop for integration if needed.
  - **CI/CD**: Assume basic (e.g., lint/test on PR).

## 5. Epics
The project is divided into epics, each representing a major milestone.

### Epic 1: Project Setup and Configuration
**Description**: Establish foundational setup, including config files, dependencies, and initial Git repo.
**User Stories**:
- **US1.1**: As a developer, I want to initialize the project structure so that I can start building modules modularly.
  - **Features**:
    - F1.1.1: Create Git repo with .gitignore, package.json, tsconfig.json.
    - F1.1.2: Install dependencies (Node.js, MCP SDK, Figtree CLI, Jest for testing).
    - F1.1.3: Set up modular directory structure (src/modules/extraction, src/modules/caching, etc.).
  - **Feature Branch**: `feature/project-setup`
  - **Commits and Sub-Tasks**:
    - Commit: `feat: Initialize Git repo and basic config files`
      - Sub-task: Create empty Git repo.
      - Sub-task: Add .gitignore for node_modules, dist, etc.
      - Sub-task: Run `npm init -y` and add TypeScript config (`tsconfig.json` with strict typing).
      - Sub-task: Add JSDoc config in tsconfig if needed.
    - Commit: `feat: Install core dependencies`
      - Sub-task: `npm install @modelcontextprotocol/sdk figtree-cli`
      - Sub-task: `npm install --save-dev typescript jest @types/jest ts-jest`
      - Sub-task: Configure Jest in package.json.
    - Commit: `feat: Set up directory structure`
      - Sub-task: Create src/index.ts as entry point.
      - Sub-task: Create src/modules/ with subdirs: extraction, caching, mapping, server, interfaces.
      - Sub-task: Add empty index.ts in each module for exports.
    - Commit: `test: Add initial setup tests`
      - Sub-task: Write spec: Test that tsconfig loads without errors.
      - Sub-task: Implement minimal code to pass spec.

- **US1.2**: As a user, I want to configure Figma access so that the agent can authenticate securely.
  - **Features**:
    - F1.2.1: Create figtree.config.json for variable mapping.
    - F1.2.2: Store Figma PAT securely (e.g., via env vars).
  - **Feature Branch**: `feature/figma-config`
  - **Commits and Sub-Tasks**:
    - Commit: `feat: Add figtree config template`
      - Sub-task: Create config/figtree.config.json with mappings (e.g., Figma var to CSS var).
      - Sub-task: /** @module config */ JSDoc in file.
    - Commit: `feat: Implement PAT handling`
      - Sub-task: Create src/modules/config/index.ts with loadConfig function.
      - Sub-task: Use process.env for PAT.
      - Sub-task: Add validation for required fields.
    - Commit: `test: Specs for config loading`
      - Sub-task: Write spec: Test config loads mappings correctly.
      - Sub-task: Mock env vars and implement to pass.

### Epic 2: Design Token Extraction and Syncing
**Description**: Build tools to extract and sync design tokens using Figtree CLI.
**User Stories**:
- **US2.1**: As an agent (Cursor), I want to sync design context so that I have up-to-date tokens.
  - **Features**:
    - F2.1.1: Implement sync_design_context tool.
    - F2.1.2: Flatten and optimize JSON for LLM (remove redundants).
    - F2.1.3: Cache results locally in .figma-cache/.
  - **Feature Branch**: `feature/sync-design-context`
  - **Commits and Sub-Tasks**:
    - Commit: `test: Write specs for TokenExtractor interface`
      - Sub-task: Define ITokenExtractor interface in src/interfaces/.
      - Sub-task: Spec: Extractor should resolve tokens via CLI.
    - Commit: `feat: Implement FigtreeTokenExtractor class`
      - Sub-task: Create src/modules/extraction/FigtreeTokenExtractor.ts.
      - Sub-task: Use child_process to exec Figtree CLI pull.
      - Sub-task: Flatten output: /** @function flattenTokens */ with JSDoc.
      - Sub-task: Apply SOLID: Depend on abstraction (inject CLI path).
    - Commit: `feat: Add local caching module`
      - Sub-task: Create src/modules/caching/LocalCache.ts.
      - Sub-task: Use fs to write/read JSON in .figma-cache/.
      - Sub-task: Implement cache invalidation (e.g., timestamp check).
    - Commit: `refactor: Integrate extraction with caching`
      - Sub-task: In sync_design_context, check cache first, extract if stale.
    - Commit: `test: Integration tests for sync`
      - Sub-task: Mock CLI output, test flattened JSON.

- **US2.2**: As a developer, I want automatic token mapping to Tailwind/CSS so that code uses semantic classes.
  - **Features**:
    - F2.2.1: Map Figma vars to tailwind.config.js format.
    - F2.2.2: Generate theme.css if needed.
  - **Feature Branch**: `feature/token-mapping`
  - **Commits and Sub-Tasks**:
    - Commit: `test: Specs for TokenMapper`
      - Sub-task: Define ITokenMapper interface.
      - Sub-task: Spec: Map should convert 'primary-bg' to Tailwind extend.
    - Commit: `feat: Implement TokenMapper class`
      - Sub-task: src/modules/mapping/TokenMapper.ts.
      - Sub-task: Parse flattened JSON, output Tailwind config snippet.
      - Sub-task: /** @method mapToTailwind */ JSDoc.
    - Commit: `feat: Export to files`
      - Sub-task: Write to tailwind.config.js or theme.css.
      - Sub-task: Handle overwrites carefully.
    - Commit: `test: End-to-end mapping tests`
      - Sub-task: Use sample JSON, assert output files.

### Epic 3: Frame Structure Fetching
**Description**: Enable fetching and simplifying Figma frame hierarchies.
**User Stories**:
- **US3.1**: As an agent, I want to get frame structure so that I can generate layout code.
  - **Features**:
    - F3.1.1: Implement get_frame_structure tool using Figma REST API.
    - F3.1.2: Simplify tree (focus on Auto Layout: padding, gap, alignment).
    - F3.1.3: Cache frame data to avoid rate limits.
  - **Feature Branch**: `feature/get-frame-structure`
  - **Commits and Sub-Tasks**:
    - Commit: `test: Specs for FrameFetcher`
      - Sub-task: IFrameFetcher interface in interfaces/.
      - Sub-task: Spec: Fetch should return simplified tree.
    - Commit: `feat: Implement FigmaFrameFetcher`
      - Sub-task: src/modules/extraction/FigmaFrameFetcher.ts.
      - Sub-task: Use axios for REST API call with PAT.
      - Sub-task: /** @function fetchFrame */ JSDoc.
      - Sub-task: Simplify: Recurse tree, trim metadata (isMask, etc.).
    - Commit: `feat: Integrate with caching`
      - Sub-task: Cache per frame_id.
      - Sub-task: Check cache before API.
    - Commit: `test: API mock tests`
      - Sub-task: Mock axios, test simplification logic.

### Epic 4: MCP Server Development
**Description**: Build the Node.js MCP server to expose tools.
**User Stories**:
- **US4.1**: As Cursor, I want to call tools via MCP so that I can integrate into workflows.
  - **Features**:
    - F4.1.1: Set up MCP server with SDK.
    - F4.1.2: Register sync_design_context and get_frame_structure tools.
    - F4.1.3: Handle errors and latency optimization.
  - **Feature Branch**: `feature/mcp-server`
  - **Commits and Sub-Tasks**:
    - Commit: `test: Specs for MCP tools`
      - Sub-task: Define tool schemas (inputs/outputs).
      - Sub-task: Spec: Tool call should invoke extractor.
    - Commit: `feat: Implement MCP server`
      - Sub-task: src/server/index.ts.
      - Sub-task: Use MCP SDK to create server.
      - Sub-task: Register tools with handlers.
      - Sub-task: Inject dependencies (extractor, mapper).
    - Commit: `feat: Error handling and logging`
      - Sub-task: Add try-catch, return structured errors.
      - Sub-task: Optimize: Parallelize if possible, but sequential for accuracy.
    - Commit: `test: Server integration tests`
      - Sub-task: Mock SDK, test tool endpoints.

### Epic 5: Optimization and Iteration
**Description**: Refine for performance and extensibility.
**User Stories**:
- **US5.1**: As a developer, I want context-trimming to keep LLM under token limits.
  - **Features**:
    - F5.1.1: Implement trimming logic in outputs.
    - F5.1.2: Ensure <3s latency.
  - **Feature Branch**: `feature/optimization`
  - **Commits and Sub-Tasks**:
    - Commit: `test: Specs for Trimmer`
      - Sub-task: IContextTrimmer interface.
      - Sub-task: Spec: Remove redundant fields.
    - Commit: `feat: Implement ContextTrimmer`
      - Sub-task: src/modules/optimization/ContextTrimmer.ts.
      - Sub-task: Filter keys like transitionNodeID.
      - Sub-task: /** @method trim */ JSDoc.
    - Commit: `refactor: Integrate trimming into tools`
      - Sub-task: Apply post-processing in tool handlers.
    - Commit: `test: Performance benchmarks`
      - Sub-task: Use Jest for timing asserts.

- **US5.2**: As a user, I want easy iteration on design changes.
  - **Features**:
    - F5.2.1: Add refresh command in MCP.
    - F5.2.2: Document user workflow.
  - **Feature Branch**: `feature/iteration-support`
  - **Commits and Sub-Tasks**:
    - Commit: `feat: Add refresh tool`
      - Sub-task: New tool: refresh_cache.
      - Sub-task: Invalidate and re-sync.
    - Commit: `docs: User guide`
      - Sub-task: README.md with setup/workflow.
    - Commit: `test: Workflow simulation`
      - Sub-task: End-to-end test simulating Cursor calls.

## 6. Implementation Roadmap
- **Phase 1**: Epic 1 (Setup) – 1 week.
- **Phase 2**: Epics 2-3 (Extraction/Fetching) – 2 weeks.
- **Phase 3**: Epic 4 (Server) – 1 week.
- **Phase 4**: Epic 5 (Optimization) – 1 week.
- **Testing**: Unit (80% coverage), Integration, E2E.
- **Deployment**: Local run via `npm start`; package as CLI if needed.

## 7. Risks and Mitigations
- Risk: Figma API changes – Mitigate: Use abstractions for easy swaps.
- Risk: Rate limits – Mitigate: Aggressive caching.
- Risk: Security (PAT) – Mitigate: Env vars only, no storage.
