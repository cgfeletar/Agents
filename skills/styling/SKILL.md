---
name: styling
description: >
  Styling compliance guidance for React components using Tailwind or
  similar utility-class systems. Covers design token usage, className
  merging, cross-module style compatibility, and config-first approach.
  Use when implementing or reviewing component styles.
user-invocable: false
---

# Styling Compliance

## Read the Design Token Config First

Before writing ANY styles, locate and read the project's Tailwind config (or equivalent design token source) for the target module. Extract and internalize:

- Custom colors (e.g., `colors.brand.primary` → use `bg-brand-primary` not `bg-blue-500`)
- Custom spacing scales
- Custom breakpoints (use these for responsive design, not hardcoded px)
- Custom font families, sizes, weights
- Custom border radii, shadows, transitions
- Any extended or overridden theme values
- Plugin-provided utilities

**ALWAYS prefer config-defined design tokens over raw Tailwind defaults.**
If the config defines `colors.error: '#DC2626'`, use `text-error` not `text-red-600`.
If the config defines `spacing.page: '2rem'`, use `px-page` not `px-8`.

If a value you need is NOT in the config, check if a semantically close
token exists before falling back to a raw Tailwind utility. Flag to the
user if you think a new token should be added to the config.

## Project Styling Conventions

- Identify any prefix rules or naming conventions required in the target module
- Confirm the className merging utility used (e.g., `cn()`, `clsx()`, `twMerge()`)
  and its import path before writing any className logic
- Use that utility for all className merging — never raw string concatenation

## Cross-Module Imports

When importing a component from one styling context into another:

1. Check if the component accepts `className` overrides
2. If yes, pass the correct-system classes via props using the target module's config tokens
3. If no, create a thin wrapper that maps styles appropriately
4. If neither is clean, flag to the user and suggest alternatives

- NEVER mix styling conventions within a single component file
- Verify token availability in the TARGET module's config, not the source's
