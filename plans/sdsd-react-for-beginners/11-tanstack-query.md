# Server state with TanStack Query

- **Nav title:** Server state with TanStack Query
- **Page:** `docs/sdsd/guides/server-state-and-tanstack-query.md`
- **Reader can:** Read the notification bell's `useQuery`, and say why the rest of the app still uses `useEffect`.

## Explain simply

`useEffect` plus `useState` keeps the JSON in the component. When the component unmounts, the data is gone. A second component that wants the same JSON fetches again. Nothing retries on an interval unless the effect says so.

TanStack Query is a cache in `QueryClient`. A component asks for a key. If the cache has data for that key, it can show it and refresh in the background. This app turns the aggressive defaults off: `retry: false` and `refetchOnWindowFocus: false` in `main.tsx`. The bell opts into a 60-second refresh with `refetchInterval: 60_000`.

`useQuery` returns `{ data, ... }`. This menu only reads `data`. While `data` is still undefined, `unreadCount` becomes `0` and `recent` becomes `[]` via `??`. There is no separate loading spinner in this component.

This is the only `useQuery` in `src/`. Do not imply the dashboard or the rules page use it. A reader who copies Query into every new page would be leaving the pattern the rest of the app uses. Say that directly.

Also say the other direction for a job: many React codebases use Query (or a similar library) for almost every GET. The concepts to take are `queryKey`, `queryFn`, and "the cache is outside the component." The concept to leave here is "SDSD fetches in an effect."

## C# bridge

`QueryClient` is an in-memory cache with a key, closer to `IMemoryCache` than to a repository. The component does not own the lifetime of the cached JSON. `queryKey: ['notifications', 'recent']` is the cache key. `queryFn` is the factory that fills it, and it calls the same `api.get` as everyone else.

There is no MediatR equivalent on the client. Do not force that analogy.

## Examples

- `src/main.tsx` — `new QueryClient({ defaultOptions: { queries: { retry: false, refetchOnWindowFocus: false } } })` and `<QueryClientProvider client={queryClient}>`. The provider must wrap any component that calls `useQuery`. It wraps `App`, so the bell is covered.
- `src/features/notifications/NotificationMenu.tsx` — the `useQuery` block and the `data?.unreadCount ?? 0` lines. Also the local `useState(false)` for `open`. That is still component state: "is the dropdown open" is not server data. The contrast is the point of the page.
- `markReadAndNavigate` calls `api.post(\`/api/notifications/${id}/read\`)` and then `window.location.assign`. It does not update the query cache. Say so. After a click the browser navigates, so the menu unmounts. Do not suggest `queryClient.invalidateQueries` as a change to make. It is a job-relevant idea; one sentence: "a larger app would often invalidate the `['notifications', 'recent']` key instead of leaving the cache stale." Mark it as not what this file does.
- `NotificationsPage.tsx` uses `useState` + `useEffect` + `useCallback`, not `useQuery`, even though it shows the same resource. Mention it as evidence that Query is the exception. Do not walk the page.

## Page outline

1. Component state versus a shared cache.
2. The provider and the two default options.
3. The bell: key, function, interval, and how undefined data is rendered.
4. Dropdown open/closed stays `useState`.
5. The rest of SDSD, and what a job interview will still expect you to recognize.

## Leave out

- `useMutation`. The app does not use it. Naming it as absent is enough.
- Installing the package. It is already a dependency (`@tanstack/react-query` 5, see `package.json`).

## Links

- Previous: Call the JSON API.
- Next: TypeScript in components.
- Hooks reference: `useQuery` row.
