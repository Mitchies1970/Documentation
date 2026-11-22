# React glossary

Definitions for the learning path, with the example taken from `SDSD/6.Frontend`. Each entry is the word, the file to open, and the guide that teaches it. Hosting, npm, and API prefixes stay in the [React frontend](react-frontend.md) reference.

## Basename

The prefix React Router adds to every route path. In `src/main.tsx`, `SPA_BASENAME` is `'/app'`, so a route of `WeighIn/Add` is the public URL `/app/WeighIn/Add`.

Guide: [Routing](../guides/routing.md).

## Children

The prop React fills with whatever a parent nests between the opening and closing tags. `ShellChrome` types `children` as `ReactNode` and renders it in `<main>`. `Layout` passes `<Outlet />` there, along with `FlashAlerts`.

Guides: [Props](../guides/props.md), [Composition](../guides/composition.md).

## Component

A function whose name starts with a capital letter and that returns UI. `ChallengeStatusBadge` is the small one: same `status`, same badge.

Guide: [Components and JSX](../guides/components-and-jsx.md).

## Context

A value any component under a provider can read, without a prop on every parent in between. This app's context is `AuthContext`. `useAuth()` reads it.

Guide: [Context](../guides/context.md).

## Controlled input

An input whose displayed text is React state. `SetGoalWeightPage` sets `value={goalWeightKg}` and writes `onChange` back through `setGoalWeightKg`. The DOM is not the source of truth.

Guide: [State and events](../guides/state-and-events.md).

## data-testid

An attribute this project adds so tests can find an element. It is not a React feature. The badge uses `data-testid="challenge-status"`. The PIN test looks up `pin-input` and `pin-login-error` the same way. Playwright can use the same id.

Guide: [Test a component](../guides/test-a-component.md).

## Dependency array

The second argument to `useEffect` (and to `useCallback` / `useMemo`). It says when to run again. `RulesPage` passes `[]`, so the fetch runs after the first render only. Leaving the array out means after every render.

Guide: [Load data on mount](../guides/effects-and-loading.md).

## Effect

A function React runs after paint. `RulesPage` and `PinLoginPage` use it to call `api.get`. The dependency array controls how often.

Guide: [Load data on mount](../guides/effects-and-loading.md).

## Hook

A function whose name starts with `use`, called at the top of a component or of another hook. `useState` in `SetGoalWeightPage` is the first one in the path.

Guide: [State and events](../guides/state-and-events.md).

## JSX

JavaScript that looks like HTML, returned from a component. The badge's `return` is a `<span className={...}>`. `class` is written `className`. JavaScript expressions sit in `{}`.

Guide: [Components and JSX](../guides/components-and-jsx.md).

## Key

The `key` prop on the element an array `map` returns. React uses it to match rows across renders. `PinLoginPage` uses `key={user.userId}`. Prefer an id from the data when the list can grow, shrink, or reorder.

Guide: [Lists and conditions](../guides/lists-and-conditions.md).

## Prop

An argument passed as an attribute on a component tag. `AlertsProps` names `success`, `warning`, and `error`. The child reads them. It does not assign to them.

Guide: [Props](../guides/props.md).

## Provider

The component that holds a context or a cache and renders the children that may read it. `AuthProvider` holds the session. `QueryClientProvider` in `main.tsx` holds the TanStack Query cache.

Guides: [Context](../guides/context.md), [Server state with TanStack Query](../guides/server-state-and-tanstack-query.md).

## Query

A cached GET, asked for by a key. `NotificationMenu` is the only `useQuery` in `src/`. The key is `['notifications', 'recent']`.

Guide: [Server state with TanStack Query](../guides/server-state-and-tanstack-query.md).

## Render

React calling a component function and updating the elements that changed. `createRoot(...).render(...)` in `src/main.tsx` mounts the tree once, on `#root`. Later renders are React calling the same functions again when state changes.

Guide: [Start here](../guides/learn-react.md).

## Route

A URL path paired with the component that should render. `App.tsx` lists them as `<Route>`. The path does not include `/app`, because the basename does.

Guide: [Routing](../guides/routing.md).

## State

A value that survives from one call of a component to the next. `useState` in `SetGoalWeightPage` holds the goal weight, the error, and `pending`. Setting it asks React to render again.

Guide: [State and events](../guides/state-and-events.md).

## Where to go next

The lessons, in order, start at [Start here](../guides/learn-react.md). Hosting and the HTTP prefixes are the [React frontend](react-frontend.md) reference.
