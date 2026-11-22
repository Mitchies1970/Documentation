# Load data on mount

The first render often has no server data. `useEffect` runs a function after that paint. The function starts the request. When the request finishes, a setter updates state, and React renders again with the data.

!!! tip "If you know C#"
    This is not the controller constructor and not middleware. The first response, the empty shell, is already on screen. The effect is a follow-up call, closer to a page script that then calls `fetch`. An `HttpClient` call would run in an action before the view. Here the view runs first, with empty state. The setter is what makes the result visible.

## When the effect runs

The dependency array is the second argument.

| Array | When the effect runs |
| --- | --- |
| `[]` | After the first render only |
| `[load]` or `[searchParams]` | Again when that value changes |
| omitted | After every render |

Omitting the array is easy to do by mistake. `DashboardPage` has one effect with no array on purpose, and it is not a fetch. It repairs a trailing slash. Do not copy it as the way to load data:

```tsx
useEffect(() => {
  // React Router drops the trailing slash (`/app?year=`). The heat-map links are
  // compared with `/app/?year=`, so put the slash back without a document load.
  const url = new URL(window.location.href)
  const base = import.meta.env.BASE_URL.replace(/\/$/, '')
  if (base.length > 0 && url.pathname === base) {
    window.history.replaceState(window.history.state, '', `${base}/${url.search}${url.hash}`)
  }
})
```

The comment is on this effect, not on the fetch below. There is no second argument, so this function runs after every render.

An effect function must not be `async` itself. The app either calls `.then()` or calls an async function from inside the effect (`void load()`).

## The small fetch to remember

`SDSD/6.Frontend/src/features/rules/RulesPage.tsx` is the pattern to keep in your head. Two state values start empty. The effect has `[]`. There is no loading flag and no `cancelled` flag.

```tsx
const [rules, setRules] = useState<RulesResponse | null>(null)
const [error, setError] = useState<string | null>(null)

useEffect(() => {
  api
    .get<RulesResponse>('/api/team/rules')
    .then(setRules)
    .catch(() => setError('Competition rules could not be loaded.'))
}, [])
```

`RulesResponse` at the top of the file is the client-side picture of that JSON. The API lesson compares it with a C# DTO. While `rules` is still null and there is no error, the component `return null`: a blank content area. The [lists](lists-and-conditions.md) lesson shows those returns.

## Ignore a response after the component is gone

If the component unmounts before the response arrives, calling a setter is wasted. In development, StrictMode also sets the effect up, tears it down, and sets it up again, so a fetch without cleanup can finish twice.

`PinLoginPage` stores a flag inside the effect and ignores the result after cleanup. This is only the user-list effect:

```tsx
useEffect(() => {
  let cancelled = false
  api
    .get<PinUser[]>('/api/auth/pin-users')
    .then((loaded) => {
      if (!cancelled) setUsers(loaded)
    })
    .catch(() => {
      if (!cancelled) setLoadError('The sign-in list could not be loaded.')
    })
  return () => {
    cancelled = true
  }
}, [])
```

The function returned from the effect is the cleanup. React calls it before running the effect again, and when the component unmounts. `AuthContext` uses the same flag around `GET /api/auth/me`. The context lesson walks that file.

`TeamIndexPage` follows this PIN-page shape: `cancelled`, `.then()`, and `[]`. `LeaderboardPage` and `InsightsPage` also fetch in an effect and keep the JSON in state. Their effects schedule `load` with `setTimeout(..., 0)`, which is the dashboard timing below, not a second pattern to memorize.

## Reload when something changes

`DashboardPage` is the advanced form of the same idea. `load` is wrapped in `useCallback` so the function stays the same until `searchParams` changes. The effect depends on `[load]`, so a new query string (the [routing](routing.md) lesson's `useSearchParams`) builds a new `load`, and the effect runs again.

`loadRequest` is a `useRef`. It remembers a number outside of render and does not by itself cause a render. Each call takes the next number. A slow response whose number is no longer current is ignored, so it cannot overwrite a newer reply.

```tsx
const loadRequest = useRef(0)

const load = useCallback(async () => {
  const request = ++loadRequest.current
  setLoading(true)
  setError(null)
  try {
    const response = await api.get<DashboardApiResponse>(buildDashboardQuery(searchParams))
    if (request !== loadRequest.current) return
    setData(response)
  } catch (err) {
    if (request !== loadRequest.current) return
    setError(err instanceof ApiError ? err.message : 'Unable to load the dashboard.')
    setData(null)
  } finally {
    if (request === loadRequest.current) setLoading(false)
  }
}, [searchParams])

useEffect(() => {
  const timer = window.setTimeout(() => {
    void load()
  }, 0)
  return () => window.clearTimeout(timer)
}, [load])
```

The fetch is started from the effect. The timeout defers it to the next turn of the event loop. The file does not say why that deferral is there. The cleanup clears the timer if the effect re-runs first. The request number, not the timer, is what drops a stale JSON body.

## What this app does not do

These fetches are not stored in TanStack Query. One screen uses a query cache. That lesson comes after routing and context.

## Continue

Previous: [Lists and conditions](lists-and-conditions.md).

Next: [Routing](routing.md).
