---
name: component-library
description: >
  Guidance for using Radix UI and building interactive UI primitives
  (modals, dropdowns, popovers, tabs, accordions, tooltips, dialogs,
  select menus). Use when implementing interactive components.
user-invocable: false
---

# Component Library — Radix UI

## Core rule

Prefer Radix UI over hand-rolling keyboard navigation, focus management, and ARIA patterns.

## Usage guidelines

- Check whether Radix is already installed (`@radix-ui/*`) before adding it
- Apply project styles via Tailwind following the project's styling conventions
- **Check for an existing project wrapper first** — the project may wrap Radix primitives
  with custom styling. Search the component library / design system directory before
  importing Radix directly
- If no wrapper exists and a Radix primitive fits, use Radix directly
- If a needed Radix package is NOT yet installed, suggest it to the user with justification before adding
- Do NOT use Radix when a simple native HTML element suffices (e.g., don't use Radix Toggle for a basic `<button>` with active state)

## Responsive implementation guidelines

When building components that must work across breakpoints:

- **Desktop-first** — start with the desktop layout and adapt downward
- **Use the project's existing breakpoint system** — read the Tailwind config for custom breakpoints rather than hardcoding px values
- **Touch targets** — minimum 44x44px for interactive elements on touch devices
- **Viewport tiers:**
  - Desktop (1024px+): full layouts, hover states, expanded navigation
  - Tablet (768px-1023px): adaptive grids, side-by-side where space permits
  - Mobile (320px-767px): single column, stacked layouts, collapsible sections
- Font sizes, spacing, and padding should scale appropriately across breakpoints
