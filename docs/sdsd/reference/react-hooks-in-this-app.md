# Hooks in this app

A lookup table. The lessons teach `useState`, `useEffect`, `useContext` (through `useAuth`), and `useQuery`. This page lists those plus the hooks that show up without their own lesson.

Call a hook at the top level of the component, or at the top level of another hook. Not inside `if`, `for`, or a nested function. `RulesPage` returns early when `error` is set, when `rules` is still null, and when `rules.success` is false. Those `if`s come after the `useState` and `useEffect` calls. Names start with `use`.

This app's own hooks are `useAuth` and `useLiveElapsed`. Every other `use*` name below is from React, React Router, or TanStack Query.

| Hook | From | One-line role | Open |
| --- | --- | --- | --- |
| `useState` | `react` | A value that causes a new render when set | `SetGoalWeightPage.tsx` |
| `useEffect` | `react` | Run after paint; fetch or subscribe | `RulesPage.tsx`, `PinLoginPage.tsx` |
| `useContext` | `react` | Read a provider. Only via `useAuth` | `AuthContext.tsx` |
| `useMemo` | `react` | Cache a computed value until its inputs change | `AuthContext.tsx` (`value`), `SeasonLeaderboard.tsx` (`weeksByMember`) |
| `useCallback` | `react` | Cache a function so an effect can depend on it | `DashboardPage.tsx` `load` |
| `useRef` | `react` | A mutable box that does not render by itself | `DashboardPage.tsx` `loadRequest`; `WeightHistoryCard.tsx` canvas ref |
| `useId` | `react` | A unique id for accessibility attributes | `ConfirmModal.tsx` `labelId` |
| `useParams` | `react-router` | Read `:id` from the path | `TeamDetailsPage.tsx` |
| `useSearchParams` | `react-router` | Read and write the query string | `DashboardPage.tsx` |
| `useNavigate` | `react-router` | Change the URL without a full document load | `AddWeighInPage.tsx` |
| `useLocation` | `react-router` | The current URL. Team details reads `location.state` for a success flash. The team wizard reads `location.pathname` to pick the step | `TeamDetailsPage.tsx`, `CreateTeamWizard.tsx` |
| `useQuery` | `@tanstack/react-query` | Cached GET, used once | `NotificationMenu.tsx` |
| `useAuth` | this app | The session state from `AuthProvider` | `AuthContext.tsx` |
| `useLiveElapsed` | this app | A seconds counter that ticks while a goal timer is on screen | `PersonalGoalTimerCluster.tsx` |

`useContext` appears only inside `useAuth`. Screens call `useAuth()`, not `useContext` directly. The [context](../guides/context.md) lesson is that hook.

`useSearchParams` on the dashboard only reads. `HeatMapSection` and `WeighInHistoryPage` also call the setter, so the query string can change without a new document.

`useLiveElapsed` is not exported. `PersonalGoalTimerCluster.tsx` defines it, and `TimerOrb` and `TimerDetailsModal` in that file call it. It is `useState` plus a `useEffect` that starts a one-second interval and clears it on cleanup.

## `useMemo` and `useCallback` are not the default

Copy them when a dependency array or an expensive calculation already forced the issue. That is what the dashboard does with `load`, and what the auth provider does with `value`. `SeasonLeaderboard` memos `weeksByMember` from the member list. Two other calls exist: `DailyChallengeDetailsPage` memos a challenge id parsed from the path, and `MoveWeighInsPage` memos the initial query params. Do not wrap every handler.

## Two jobs for `useRef`

The number ref remembers a value across renders without showing it. `DashboardPage` stores `loadRequest` that way so a slow response can be ignored. The [effects](../guides/effects-and-loading.md) lesson walks that counter.

The canvas ref reaches the real DOM node. `WeightHistoryCard` keeps `canvasRef` and passes `canvasRef.current` to Chart.js. That is the only Chart.js mount to know about here.

`ConfirmModal` uses `useId()` for `labelId`, then sets `aria-labelledby={labelId}` on the dialog. The id stays stable across renders.

## Not in this app

- `useReducer`. Multi-field state is several `useState` calls. The goal-weight form is the small example in the [state](../guides/state-and-events.md) lesson.
- `useMutation`. Writes are `api.post` inside the event handler. The [API](../guides/calling-the-api.md) lesson is that call. The bell's `useQuery` does not have a matching mutation. The [server-state](../guides/server-state-and-tanstack-query.md) lesson says so.
- A global store such as Redux or Zustand. Shared session state is `AuthProvider`.

## Related pages

[Glossary](react-glossary.md). Lessons: [state](../guides/state-and-events.md), [effects](../guides/effects-and-loading.md), [routing](../guides/routing.md), [context](../guides/context.md), [server state](../guides/server-state-and-tanstack-query.md).
