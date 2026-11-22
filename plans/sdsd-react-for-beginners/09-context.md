# Context

- **Nav title:** Context
- **Page:** `docs/sdsd/guides/context.md`
- **Reader can:** Explain why identity is not passed through every prop, and read `useAuth()` safely.

## Explain simply

Props are the normal way to pass data (composition lesson). Identity is needed in the layout, the home page, and many screens. Threading it through every parent would add an `identity` prop to components that do not care about it.

Context is a value that any component under a provider can read. `AuthProvider` holds the session state and renders `<AuthContext.Provider value={value}>`. `useAuth()` calls `useContext(AuthContext)`. If there is no provider above the caller, it throws `"useAuth must be used within an AuthProvider"`.

The state is a discriminated union:

- `{ status: 'loading' }`
- `{ status: 'anonymous' }`
- `{ status: 'authenticated'; identity: AuthIdentity }`

Callers narrow with `state.status === 'authenticated'` before reading `state.identity`. TypeScript then knows `identity` exists. That is the main TypeScript point on this page; the later TypeScript lesson can point back here.

The provider loads `GET /api/auth/me` in an effect (the reader has seen this pattern). A failed call becomes `'anonymous'`, not a thrown error on the screen. A later `401` from `apiFetch` fires the `auth:unauthorized` window event, and a second effect sets `'anonymous'`. `signOutLocally` does the same without a request.

`useMemo` around `value` keeps the same object when `state` has not changed, so consumers do not render only because the provider function ran. One sentence. The hooks reference can define `useMemo`. Do not make memoization the topic.

## C# bridge

`useAuth()` is the closest thing to reading `HttpContext.User` from a filter or a view. The difference: you opt in by calling the hook, and only components under `AuthProvider` can see it. It is not dependency injection. A new component does not get it from a constructor.

The union is a small state machine, like a C# `abstract record` with three variants, checked with `status` instead of pattern matching on types.

## Examples

- `src/auth/AuthContext.tsx` — the union, the provider, both effects, `useAuth`. The file is the lesson. Skip nothing important, but do not paste the `AuthIdentity` field list in full; a short table of the fields a UI actually branches on is enough: `userName`, `loserGroupId`, `isAdmin`, `authStatus`, `isImpersonating`. Re-read the navbar if you name a field, so the table matches real branches (`ShellChrome` uses `authStatus`, `loserGroupId`, and likely admin / impersonation).
- `src/components/Layout.tsx` — `const { state } = useAuth()` and the three branches (loading spinner, anonymous with `identity = null`, authenticated).
- `src/App.tsx` `HomePage` — `identityHasTeam`. Show the helper: it prefers `authStatus === 'inTeam'`, otherwise `loserGroupId !== null`.
- `src/main.tsx` — `AuthProvider` wraps `App`, inside the router. One sentence: `useAuth` works in `App` and below, not in a component rendered outside the provider.

`identityHasTeam` and the context live in one module. The file disables `react-refresh/only-export-components` because it exports a hook and a helper next to the provider. Mention that only if the snippet includes the eslint comment, and explain it as a tooling warning, not a React rule.

## Page outline

1. The prop-drilling problem, in two sentences, tied to `Layout` and `HomePage`.
2. Provider, context, hook.
3. The union and narrowing.
4. How the status gets its first value, and how a 401 clears it.
5. Where it is read: `Layout`, `HomePage`. Invite the reader to search for `useAuth(` to see the other call sites. Do not list them all unless a quick search at write time shows only a handful; if the list is short, a table is better than "go search".

## Leave out

- The cookie and antiforgery design. The reference already has it. One line: the request sends the auth cookie because `apiFetch` uses `credentials: 'same-origin'`.
- Building a second context. This app has this one. Say that, so the reader does not expect a context per feature.

## Links

- Previous: Routing.
- Next: Call the JSON API.
