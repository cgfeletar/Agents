---
name: accessibility
description: >
  WCAG 2.1 AA accessibility requirements for React components.
  Use when implementing or reviewing components that need keyboard
  navigation, ARIA attributes, semantic HTML, or form accessibility.
user-invocable: false
---

# Accessibility Requirements (WCAG 2.1 AA)

## Semantic HTML

- Use correct elements: `<button>` for actions, `<a>` for navigation, `<nav>`, `<main>`, `<section>`, `<article>`, `<aside>` as appropriate
- Never use `<div>` or `<span>` for interactive elements
- Use heading hierarchy correctly — no skipping levels within a component's context

## Keyboard Navigation

- All interactive elements must be focusable and operable via keyboard
- Tab order must match visual order (DOM order = visual order)
- Visible focus indicators required — never `outline: none` without a replacement
- Escape closes modals, dropdowns, and popovers
- Arrow keys navigate menus, tabs, listboxes, and similar composites
- No keyboard traps

## ARIA

- Use ARIA only when semantic HTML is insufficient
- Pair `aria-expanded` with the controlling element for collapsibles
- Use `aria-live` for dynamic content updates (`polite` for non-urgent, `assertive` for errors)
- Provide `aria-label` or `aria-labelledby` for icon-only buttons and landmarks
- Use `aria-describedby` for supplementary descriptions (e.g., error messages on inputs)
- Never use `aria-hidden="true"` on focusable elements

## Visual

- Minimum 4.5:1 contrast ratio for normal text, 3:1 for large text
- Never rely on color alone to convey meaning — pair with icons, text, or patterns
- Wrap animations in `prefers-reduced-motion` media query check
- Support text resizing up to 200% without content loss

## Forms

- Every input must have a visible `<label>` (or `aria-label` if visually hidden)
- Group related inputs with `<fieldset>` and `<legend>`
- Associate error messages via `aria-describedby`, announced via `aria-live="assertive"`
- Do not disable submit buttons — show validation messages instead

## Existing Component Violations

If you find an existing component (REUSE or ADAPT) that fails WCAG 2.1 AA:

- Do NOT fix it as part of this implementation — it may affect other consumers
- Flag it in your final summary: file path, specific violation, severity (critical/major/minor)
- Accessibility fixes to shared components should be a separate dedicated effort

## Testing accessibility

- Use automated a11y tooling (`jest-axe`, `vitest-axe`) if installed in the project
- If not installed, cover a11y via manual RTL assertions:
  (`getByRole`, `getByLabelText`, keyboard interaction tests)
- Radix UI and headlessui components handle most keyboard/focus/ARIA patterns automatically —
  prefer these wrappers over hand-rolling interactive primitives
