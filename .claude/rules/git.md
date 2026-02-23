# Git Workflow Rules

- Always work on a feature branch. Never commit directly to `main`.
- Branch naming: `feature/<short-description>`, `fix/<short-description>`,
  `refactor/<short-description>`, `test/<short-description>`,
  `docs/<short-description>`.
- One logical change per commit. Commit messages use semantic prefixes:
  `feat:`, `fix:`, `docs:`, `test:`, `refactor:`.
- Before committing: `npm run build && npm test` must both pass.
- Before merging to `main`: all CI checks (lint, type-check, tests) must
  be green.
- Never commit secrets (`.env`, PAT values). The `.gitignore` already
  excludes `.env*` (except `.env.example`), `dist/`, `node_modules/`,
  and `.figma-cache/`.
