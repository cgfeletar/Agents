---
name: performance
description: >
  React performance patterns and optimization guidance. Use when
  implementing or reviewing components that involve expensive renders,
  long lists, large data sets, or frequent re-renders.
user-invocable: false
---

# React Performance Patterns

## The Golden Rule

Only optimize when there is a measurable problem. Premature optimization adds complexity and obscures intent. Profile first, then optimize.

## Memoization

**`React.memo`** — Prevents re-render when a parent re-renders but props haven't changed.
- Use for components that render often with the same props
- Do NOT use for every component — the comparison cost can exceed the render cost for cheap components
- Useless if parent passes new object/array/function references on every render (fix the parent first)

**`useMemo`** — Memoizes an expensive computed value.
- Use when the computation is genuinely expensive (complex transforms, large array operations)
- Do NOT use for simple calculations (`a + b`, string concatenation, boolean checks)
- Dependencies must be stable — an unstable dep defeats the purpose

**`useCallback`** — Memoizes a function reference.
- Use when passing callbacks to memoized children (otherwise React.memo is defeated)
- Use for event handlers in hook return values (keeps references stable across renders)
- Do NOT use for inline event handlers that don't flow into memoized children

## Re-render Prevention

**Context** — Context re-renders every consumer when the value changes.
- Split contexts by update frequency: put fast-changing values in a separate context from slow-changing ones
- Memoize context values with `useMemo` when the object is created inline
- Consider using an external store (Redux, Zustand, Jotai) for high-frequency updates

**State placement** — Keep state as close to where it's used as possible.
- State high in the tree causes wide re-renders on every update
- Lift only when genuinely needed; prefer co-location

**Key stability** — Unstable keys (index, `Math.random()`) cause unnecessary unmount/remount cycles.

## Long Lists

For lists with 50+ items, virtualize — only render what's in the viewport.

- Check if the project already has a virtualization library (`@tanstack/react-virtual`, `react-window`, `react-virtuoso`) before adding one
- Virtualization adds complexity — confirm the list is actually causing performance issues first
- Fixed-size rows are simpler; variable-size rows require measuring

## Code Splitting & Lazy Loading

**`React.lazy` + `Suspense`** — Defer loading a component until it's needed.
- Use for heavy components behind a route, modal, or tab that isn't visible on initial load
- Always provide a meaningful `fallback` in `Suspense` (skeleton, not null)
- Do NOT lazy-load small components — the network round-trip cost exceeds the bundle savings

**Dynamic `import()`** — Split large utility libraries (chart libraries, rich text editors, etc.).
- Import at the call site rather than at the module top when the import is conditional

## Bundle Impact

Before adding a dependency:
- Check bundle size at bundlephobia.com or equivalent
- Prefer tree-shakeable packages
- Import only what you need: `import { debounce } from 'lodash-es'` not `import _ from 'lodash'`

## Network Performance

**Debounce** user input before triggering fetches (search, autocomplete).
**Deduplicate** — never fire the same request twice in parallel; use the shared cache or a ref flag.
**Prefetch** data that is very likely to be needed next (next page, hover intent).

## What NOT to Do

- Do not add `useMemo`/`useCallback` everywhere "just in case" — measure first
- Do not use `React.memo` without also stabilizing the props passed to it
- Do not split context just because you've heard it's slow — profile first
- Do not virtualize lists with fewer than ~50 items
