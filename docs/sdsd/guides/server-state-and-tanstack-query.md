# Server state with TanStack Query

`useEffect` plus `useState` keeps the JSON in the component. When the component unmounts, that data is gone. A second component that wants the same JSON fetches again. Nothing retries on an interval unless the effect says so.

TanStack Query is a cache in `QueryClient`. A component asks for a key. If the cache has data for that key, it can show it and refresh in the background. This app turns the aggressive defaults off. The notification bell is the one place that opts into a refresh.

!!! tip "If you know C#"
    `QueryClient` is an in-memory cache with a key, closer to `IMemoryCache` than to a repository. The component does not own the lifetime of the cached JSON. `queryKey: ['notifications', 'recent']` is the cache key. `queryFn` is the factory that fills it, and it calls the same `api.get` as everyone else.

## The provider

`src/main.tsx` creates one client and wraps the tree. `retry: false` and `refetchOnWindowFocus: false` are the defaults for every query. The provider has to wrap any component that calls `useQuery`. It wraps `App`, so the bell is covered.

```tsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: false,
      refetchOnWindowFocus: false
    }
  }
})
```

```tsx
<QueryClientProvider client={queryClient}>
```

`@tanstack/react-query` 5 is already a dependency. This lesson does not install it.

## The bell

`SDSD/6.Frontend/src/features/notifications/NotificationMenu.tsx` is the only `useQuery` in `src/`. The dashboard and the rules page do not use it. Copying Query into every new page would leave the pattern the rest of the app uses.

```tsx
const [open, setOpen] = useState(false)
const { data } = useQuery({
  queryKey: ['notifications', 'recent'],
  queryFn: () => api.get<NotificationsRecent>('/api/notifications/recent'),
  refetchInterval: 60_000
})

const unreadCount = data?.unreadCount ?? 0
const recent = data?.items ?? []
```

`queryKey` identifies the cache entry. `queryFn` fills it with `GET /api/notifications/recent`. `refetchInterval: 60_000` asks for a refresh every 60 seconds. That interval is this query's choice. The defaults above would not poll.

`useQuery` returns `{ data, ... }`. This menu only reads `data`. While `data` is still undefined, `unreadCount` is `0` and `recent` is `[]`, via `??`. There is no separate loading spinner in this component. The empty list renders "No new notifications", which the [lists](lists-and-conditions.md) lesson already quoted.

`open` is still `useState(false)`. "Is the dropdown open" is not server data. The click handler flips it:

```tsx
setOpen((current) => !current)
```

Clicking an item does not update the cache. `markReadAndNavigate` posts, then the browser navigates, so the menu unmounts:

```tsx
async function markReadAndNavigate(id: number, href: string | null | undefined) {
  try {
    await api.post(`/api/notifications/${id}/read`)
  } catch {
    // Navigation still proceeds; marking read can be retried on the page.
  }
  window.location.assign(href ?? notificationHref(null))
}
```

The file does not invalidate `['notifications', 'recent']`. A larger app would often invalidate that key instead of leaving the cache stale. That is not what this file does.

`useMutation` is not used. Writes elsewhere are `api.post` inside the event handler, as the [API](calling-the-api.md) lesson showed.

## The rest of the app, and a later job

`NotificationsPage` shows notifications too, and it does not use `useQuery`. It loads `GET /api/notifications` with `useState`, `useCallback`, and `useEffect`. The bell's URL is `GET /api/notifications/recent`. Same feature, and Query is still the exception.

Many React codebases use Query (or a similar library) for almost every GET. The ideas to take are `queryKey`, `queryFn`, and "the cache is outside the component." The idea to leave in this repo is "SDSD fetches in an effect." The hooks reference lists `useQuery` next to the other hooks.

## Continue

Previous: [Call the JSON API](calling-the-api.md).

Next: TypeScript in components.
