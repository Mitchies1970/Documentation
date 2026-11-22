# Context

Props are the normal way to pass data. The [composition](composition.md) lesson did that. Identity is needed in the layout, on the home page, and on the team page. Threading an `identity` prop through every parent would add it to components that do not care about it.

Context is a value any component under a provider can read. `AuthProvider` holds the session and renders `<AuthContext.Provider value={value}>`. `useAuth()` calls `useContext(AuthContext)`. If there is no provider above the caller, it throws `"useAuth must be used within an AuthProvider"`.

This app has this one context. Feature screens do not each get their own.

!!! tip "If you know C#"
    `useAuth()` is the closest thing to reading `HttpContext.User` from a filter or a view. You opt in by calling the hook, and only components under `AuthProvider` can see it. It is not dependency injection. A new component does not receive it from a constructor. The state union is a small state machine: three variants, checked with `status` instead of pattern matching on types.

## The state union

`SDSD/6.Frontend/src/auth/AuthContext.tsx` is the lesson. The state is a discriminated union. Callers narrow with `state.status === 'authenticated'` before reading `state.identity`. TypeScript then knows `identity` exists. The [TypeScript](typescript-in-react.md) lesson points back at this.

```tsx
export type AuthState =
  | { status: 'loading' }
  | { status: 'anonymous' }
  | { status: 'authenticated'; identity: AuthIdentity }
```

`AuthIdentity` has more fields than the UI branches on. These are the ones the shell and the home page actually use:

| Field | What branches on it |
| --- | --- |
| `userName` | The hello line in `ShellChrome` |
| `loserGroupId` | Team links, and the fallback when `authStatus` is absent |
| `isAdmin` | The admin links, and `TeamDetailsPage` |
| `authStatus` | `'inTeam'` versus a signed-in user with no team |
| `isImpersonating` | The "Stop impersonating" button |

`ShellChrome` does not call `useAuth`. `Layout` passes `identity` in as a prop. The bar computes its own flag:

```tsx
const inTeam = identity?.authStatus === 'inTeam' || (identity != null && identity.loserGroupId !== null)
```

`identityHasTeam`, which `HomePage` uses, is stricter. If `authStatus` is set, only `'inTeam'` counts, even when `loserGroupId` is also set:

```tsx
export function identityHasTeam(identity: AuthIdentity): boolean {
  if (identity.authStatus) return identity.authStatus === 'inTeam'
  return identity.loserGroupId !== null
}
```

The file starts with an ESLint disable because it exports a hook and `identityHasTeam` next to the provider. That is a tooling warning about React Fast Refresh. It is not a React rule.

## How the status gets a value

The provider starts as `{ status: 'loading' }`. The first effect loads `GET /api/auth/me`. A success becomes `'authenticated'`. A failed call becomes `'anonymous'`, not an error thrown onto the screen. The `cancelled` flag is the same cleanup as the [PIN login effect](effects-and-loading.md).

```tsx
const [state, setState] = useState<AuthState>({ status: 'loading' })

useEffect(() => {
  let cancelled = false

  api
    .get<AuthIdentity>('/api/auth/me')
    .then((identity) => {
      if (!cancelled) setState({ status: 'authenticated', identity })
    })
    .catch(() => {
      if (!cancelled) setState({ status: 'anonymous' })
    })

  return () => {
    cancelled = true
  }
}, [])
```

A later `401` from `apiFetch` fires the `auth:unauthorized` window event. A second effect sets `'anonymous'`. The request sends the auth cookie because `apiFetch` uses `credentials: 'same-origin'`. Cookie flags and antiforgery live in the [reference](../reference/react-frontend.md#session-and-api-calls).

```tsx
useEffect(() => {
  const handleUnauthorized = () => setState({ status: 'anonymous' })
  window.addEventListener(AUTH_UNAUTHORIZED_EVENT, handleUnauthorized)
  return () => window.removeEventListener(AUTH_UNAUTHORIZED_EVENT, handleUnauthorized)
}, [])
```

`signOutLocally` sets `'anonymous'` without a request. The shell's Logout button does not call it. That button posts `/api/auth/logout` and then loads a new document, which the [routing](routing.md) lesson showed.

```tsx
const value = useMemo<AuthContextValue>(
  () => ({
    state,
    signOutLocally: () => setState({ status: 'anonymous' })
  }),
  [state]
)

return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>
```

`useMemo` keeps the same `value` object when `state` has not changed, so consumers do not render only because the provider function ran. The [hooks reference](../reference/react-hooks-in-this-app.md) defines `useMemo`. It is not the topic here.

```tsx
export function useAuth(): AuthContextValue {
  const context = useContext(AuthContext)
  if (!context) throw new Error('useAuth must be used within an AuthProvider')
  return context
}
```

In `src/main.tsx`, `AuthProvider` wraps `App`, inside the router. `useAuth` works in `App` and below. A component rendered outside the provider cannot call it.

## Where it is read

Three components call `useAuth()`. The test file calls it too.

| Caller | What it does with `state` |
| --- | --- |
| `Layout` | Spinner while `'loading'`. `identity` is `state.identity` when `'authenticated'`, otherwise `null`. |
| `HomePage` in `App.tsx` | `DashboardPage` when authenticated and `identityHasTeam`; otherwise `SelectTeamPage`. |
| `TeamDetailsPage` | `const isAdmin = state.status === 'authenticated' && state.identity.isAdmin` |

`Layout`'s narrowing is the line the composition lesson already rendered:

```tsx
const identity = state.status === 'authenticated' ? state.identity : null
```

The `'authenticated'` check is what makes `state.identity` legal. The other two statuses have no `identity` field.

## Continue

Previous: [Routing](routing.md).

Next: [Call the JSON API](calling-the-api.md).
