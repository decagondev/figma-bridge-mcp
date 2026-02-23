---
paths:
  - "src/modules/mapping/**/*.ts"
---

# Token Mapping Rules

- `TokenMapper` converts the flat token map (semantic name → resolved value)
  into a Tailwind `theme.extend` object or CSS custom-property declarations,
  depending on the output format requested.
- Preserve the semantic naming hierarchy from Figma (e.g.,
  `colors/primary/bg` → `--color-primary-bg` or `colors.primary.bg` in
  Tailwind).
- Never emit raw hex values without an accompanying semantic key. The goal
  is zero hardcoded colors in generated code.
- When writing to `tailwind.config.js` or `theme.css`, read the existing
  file first and merge; never blindly overwrite user customizations outside
  the managed section.
