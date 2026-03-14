---
name: anti-patterns
description: >
  React anti-patterns, self-review checklist, and linting gates.
  Use when implementing or reviewing React components to catch
  infinite loop risks, performance issues, hook violations, and
  state management mistakes.
user-invocable: false
---

# React Anti-Patterns, Self-Review Checklist & Linting Gates

## REACT ANTI-PATTERN ENFORCEMENT

Stop and refactor immediately if you catch yourself writing any of these.

### Infinite Loop Risks

- useEffect without a dependencies array
- useEffect with object/array dependencies created inline (useMemo the dependency, or use primitive values)
- setState called during render (outside event handlers or effects)
- useEffect that triggers state that re-triggers the same effect (use functional setState, or restructure to break the cycle)

### Performance

- Inline object/array/function creation in JSX props (extract to constants, useMemo, or useCallback)
- Monolithic components rendering huge trees (split into focused child components)
- Context providers wrapping the entire app unless truly global (scope providers to the subtree that needs the data)
- Heavy computation in render body without useMemo

### Hook Violations

- Hooks called conditionally or inside loops
- eslint-disable for exhaustive-deps (fix the deps instead)
- Stale closures in async callbacks without refs (use refs for values needed in async callbacks)

### State Management

- Prop drill more than 2-3 levels deep
- Store derived state in useState when it can be computed
- Multiple related useState calls for complex interdependent state (consolidate with useReducer)

### Additional Anti-Patterns

- Direct DOM manipulation (document.querySelector, innerHTML)
- Index as key in lists that can reorder, filter, or mutate
- Missing cleanup in useEffect (subscriptions, timers, abort controllers)
- Empty catch blocks or catch(() => {}) — handle errors explicitly
- Boolean && rendering that could render `0` or `""` (use ternary or check `value != null`)
- Fetching in useEffect without an AbortController

---

## SELF-REVIEW CHECKLIST

Before finalizing ANY component, verify every item:

- [ ] Strict TypeScript — no `any`, no untyped exports
- [ ] All hooks follow rules of hooks (no conditional/loop calls)
- [ ] No useEffect without deps array
- [ ] No inline object/array/function in JSX props on memoized children
- [ ] Loading, error, and empty states all handled
- [ ] Keyboard navigable — tab, enter, escape, arrows where appropriate
- [ ] Screen reader friendly — labels, roles, live regions
- [ ] Responsive — mobile, tablet, desktop considered
- [ ] Styling follows project conventions, className merging utility used correctly
- [ ] Cross-module imports validated for style compatibility
- [ ] No prop drilling beyond 2-3 levels
- [ ] Derived state is computed, not stored
- [ ] useEffect cleanup functions present for all side effects
- [ ] Code is testable — pure logic extracted, DI via props, states reachable via props
- [ ] ADAPT consumers documented in comments for the test agent
- [ ] Matches existing project conventions (file structure, naming, patterns)
- [ ] No new packages without justification and user approval
- [ ] AbortControllers on all fetch-in-effect patterns
- [ ] No boolean && that could render falsy primitives
- [ ] Any `@ts-nocheck` files reported to user
- [ ] Existing accessibility violations flagged (not fixed) in final summary

---

## LINTING & CODE QUALITY GATES

After implementation is complete:

1. **Lint every file you created or modified:**
   - Run the project's lint command scoped to only the files you touched
   - Fix all auto-fixable issues; manually fix the rest
   - Do NOT add eslint-disable comments — fix the root cause

2. **Report `@ts-nocheck` files:**
   - Scan every file you touched for `@ts-nocheck` or `@ts-ignore` at the file level
   - Report: file path, whether you added it (you should NOT have), what types are suppressed
   - Do NOT remove pre-existing `@ts-nocheck` — that may surface errors outside your scope
