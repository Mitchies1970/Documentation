# Load data on mount

- **Nav title:** Load data on mount
- **Page:** `docs/sdsd/guides/effects-and-loading.md`
- **Reader can:** Read the usual SDSD page pattern: state for data, error, and loading; an effect that calls `api.get` once; a cleanup flag when the request can finish after the component is gone.

## Explain simply

The first render often has no server data. `useEffect` runs a function after that paint. The function starts the request. When the request finishes, a setter updates state, and React renders again with the data.

The dependency array says when to run again. `[]` means "after the first render only". `[load]` or `[searchParams]` means "again when that value changes". Leaving the array out means "after every render". `DashboardPage` has one effect with no array on purpose (a `history.replaceState` fix). Do not present that as the normal fetch. Point at it as a special case so the reader does not copy it.

Effects must not be `async` directly. The app either calls `.then()` or calls an async function from inside the effect (`void load()`).

If the component unmounts before the response arrives, calling a setter is wasted and, in StrictMode, the effect is set up and torn down once extra in development. The careful pattern stores `cancelled` and ignores the result in the cleanup function. Show both the simple and the careful versions, and say which files do which.

## C# bridge

This is not the controller constructor and not middleware. The first response (the empty shell) is already on screen. The effect is a follow-up call, closer to a page script that then calls `fetch`. The setter is what makes the result visible.

`HttpClient` would be called from an action before the view runs. Here the view runs first, with empty state.

## Examples

1. **Smallest fetch.** `SDSD/6.Frontend/src/features/rules/RulesPage.tsx`.
   - `useState<RulesResponse | null>(null)` and `useState<string | null>(null)`.
   - Effect with `[]`: `api.get<RulesResponse>('/api/team/rules').then(setRules).catch(...)`.
   - No `cancelled` flag and no loading flag. While `rules` is null and there is no error, the component `return null`. Say that plainly.
   - The `RulesResponse` interface at the top of the file is the client-side picture of the JSON. The API lesson compares it with a C# DTO. Here, one sentence.

2. **Cleanup.** `SDSD/6.Frontend/src/pages/PinLoginPage.tsx` lines for the user-list effect only.
   - `let cancelled = false` inside the effect.
   - `.then`: `if (!cancelled) setUsers(loaded)`.
   - Return `() => { cancelled = true }`.
   - Same idea in `AuthContext.tsx` for `/api/auth/me`. Mention it; the context lesson walks that file.

3. **Reload when something changes.** `DashboardPage.tsx` — the `load` callback and the effect that depends on `[load]`, not the whole page.
   - `useCallback` so `load` is stable except when `searchParams` changes.
   - `useRef` counter (`loadRequest`) so a slow response does not overwrite a newer one. Explain as "remember a number outside of render, and ignore a stale reply."
   - `setLoading` / `setError` / `setData`.
   - The `setTimeout(..., 0)` around `load` is in the file. Mention it in one sentence as "the fetch is scheduled immediately after the effect" without inventing a deeper reason if you cannot confirm one from a comment. There is a comment on the *other* effect (trailing slash), not on the timeout. Do not invent why the timeout is there. Say the fetch is started from the effect and the timeout defers it to the next turn of the event loop.

Most feature pages follow the Rules or PinLogin shape (`TeamIndexPage`, `LeaderboardPage`, `InsightsPage`, and others). The reader should recognize the shape, not memorize every page.

## Page outline

1. First render has no data.
2. `useEffect` and the dependency array, including the warning about a missing array.
3. `RulesPage` as the pattern to copy in your head.
4. The `cancelled` cleanup, with StrictMode named once: in development React mounts, unmounts, and mounts again, so a fetch without cleanup can finish twice.
5. Dashboard reload: callback, ref counter, search params. Mark it as the advanced form of the same idea.
6. What this app does *not* do: it does not put these fetches in TanStack Query. Next-but-one lesson after routing and context... actually the Query lesson is 11. Say "one screen uses a query cache; that lesson is later."

## Leave out

- The body of `apiFetch` (headers, XSRF, 401). API lesson.
- Router hooks.

## Links

- Previous: Lists and conditions.
- Next: Routing.
