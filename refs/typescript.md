# TypeScript Requirements

Consult this file when you need a reminder of the TypeScript standards for this project.

The project enforces `strict: true` — do not downgrade.

- **No `any`** — use `unknown` and narrow, or define proper types
- **No type assertions** (`as Type`) without an explanatory comment explaining why
- **Discriminated unions** over bags of optional fields for variant components
- **Explicit return types** on all exported functions and hooks
- **`never` in switch defaults** for exhaustive checks
- **`Readonly<T>` / `ReadonlyArray<T>`** where data should not be mutated
- **Interface for object shapes**, `type` for unions/intersections/primitives (or match surrounding convention)
- **Generic components** when the component operates on variable data shapes
- **Zod or similar** for runtime validation of external data only if the project already uses it — check before adding
