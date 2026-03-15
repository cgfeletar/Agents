---
name: error-handling
description: >
  Error handling patterns for React components: error boundaries,
  loading/error/empty states, retry logic, and error logging conventions.
  Use when implementing or reviewing async components or error flows.
user-invocable: false
---

# Error Handling Patterns

## The Three States Rule

Every component with an async dependency MUST handle all three states explicitly. Missing any one is a P1.

- **Loading** — Skeleton loaders matching the layout (not generic spinners); `aria-busy="true"` and `aria-live="polite"`
- **Error** — User-friendly message + retry where applicable; `aria-live="assertive"`; log via the project's error tracking utility
- **Empty** — Helpful guidance with a suggested action, not just "No data"

These states must be reachable via props or simple setup — not hidden behind complex async chains.

## Error Boundaries

Error boundaries catch render-time errors that `try/catch` cannot.

- Place error boundaries at meaningful boundaries: route level, feature level, and around third-party widgets
- Do NOT wrap every individual component — too granular boundaries mask systemic issues
- Provide a useful fallback UI with a retry action where possible
- Log the error in `componentDidCatch` using the project's error tracking utility
- Check if the project has an existing shared `ErrorBoundary` component before writing a new one

## Fetch Error Handling

For every fetch:

1. **Non-OK HTTP responses** — Check `response.ok` and handle explicitly; do not assume a non-throwing fetch succeeded
2. **Network errors** — Caught in `catch`; show error state, log if unexpected
3. **AbortError (timeout / unmount cleanup)** — Do NOT log or report to error tracking; this is expected operational noise
4. **All other errors** — Log via the project's error tracking utility

**Pattern for fetch-in-effect:**

```ts
useEffect(() => {
  let isMounted = true
  const controller = new AbortController()

  async function fetchData() {
    try {
      const res = await fetch(url, { signal: controller.signal })
      if (!res.ok) throw new Error(`HTTP ${res.status}`)
      const data = await res.json()
      if (isMounted) setData(data)
    } catch (err) {
      if (err instanceof Error && err.name === 'AbortError') return // expected
      if (isMounted) setError(err)
      // log via project's error tracking utility
    }
  }

  fetchData()
  return () => { isMounted = false; controller.abort() }
}, [url])
```

## Error Logging

- Use the project's established error tracking utility — grep for existing usage to confirm the calling convention before writing new logging code
- Never use `console.error` directly in production code — it bypasses rate limiting, context enrichment, and alerting
- Never use raw third-party SDK calls (`Sentry.captureException`, `datadogLogs.logger.error`) directly — the project's utility wraps these
- Never swallow errors silently: `catch (err) {}` or `catch(() => {})` is always wrong

## Retry Logic

- Offer a retry action for transient failures (network errors, 5xx responses)
- Do NOT auto-retry without a backoff — retrying immediately on a 429 or 503 makes things worse
- Cap retries (max 3) and show a final "something went wrong" state with a support link if applicable
- Reset error state before retrying — don't accumulate stale error messages

## Never Fail Silently

- Every `catch` block must do something: set error state, log, or re-throw
- Empty catch blocks are always wrong
- Optional chaining (`?.`) is fine for safe access but should not be used to silently swallow missing data that indicates a real problem
