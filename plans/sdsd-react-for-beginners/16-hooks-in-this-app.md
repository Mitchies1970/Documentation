# Hooks in this app

- **Nav title:** Hooks in this app
- **Page:** `docs/sdsd/reference/react-hooks-in-this-app.md`
- **Reader can:** See which hooks this codebase uses, what each one is for in a sentence, and one file to open.

## What this page is

A lookup table. The lessons teach `useState`, `useEffect`, `useContext` (through `useAuth`), and `useQuery` properly. This page lists those plus the hooks that appear without their own lesson, so a reader is not surprised in a file.

Rules to state once, before the table:

- Call hooks at the top level of the component or of another hook. Not inside `if`, `for`, or a nested function. (The `RulesPage` early returns come *after* the `useState` / `useEffect` calls. That is the example.)
- Names start with `use`.
- This app's own hooks are `useAuth` only. Other `use*` names in the table are from React, React Router, or TanStack Query.

## Table

Confirm every row with a search at write time. The November 2026 survey:

| Hook | From | One-line role | Open |
| --- | --- | --- | --- |
| `useState` | react | A value that causes a new render when set | `SetGoalWeightPage.tsx` |
| `useEffect` | react | Run after paint; fetch or subscribe | `RulesPage.tsx`, `PinLoginPage.tsx` |
| `useContext` | react | Read a provider. Only via `useAuth` | `AuthContext.tsx` |
| `useMemo` | react | Cache a computed value until its inputs change | `AuthContext.tsx` (`value`), `SeasonLeaderboard.tsx` (`weeksByMember`) |
| `useCallback` | react | Cache a function so an effect can depend on it | `DashboardPage.tsx` `load` |
| `useRef` | react | A mutable box that does not render by itself | `DashboardPage.tsx` `loadRequest`; `WeightHistoryCard.tsx` canvas ref |
| `useId` | react | A unique id for accessibility attributes | `ConfirmModal.tsx` `labelId` |
| `useParams` | react-router | Read `:id` from the path | `TeamDetailsPage.tsx` |
| `useSearchParams` | react-router | Read and write the query string | `DashboardPage.tsx` |
| `useNavigate` | react-router | Change the URL without a full document load | `AddWeighInPage.tsx` |
| `useLocation` | react-router | The current URL. Team details reads `location.state` for a success flash. The team wizard reads `location.pathname` to pick the step | `TeamDetailsPage.tsx`, `CreateTeamWizard.tsx` |
| `useQuery` | `@tanstack/react-query` | Cached GET, used once | `NotificationMenu.tsx` |

`useMemo` and `useCallback` need a shared warning in one paragraph under the table: they are not a default. Copy them when a dependency array or an expensive calculation already forced the issue, which is what the dashboard and the auth provider do. Do not tell the reader to wrap every handler.

`useRef` has two different jobs in this repo. Keep both examples. The number ref is "remember this across renders without showing it." The canvas ref is "reach the real DOM node that Chart.js needs." `WeightHistoryCard` is the only Chart.js mount to mention. Do not explain Chart.js.

## Not in this app

A short list, each one line, so a job reader knows the gap:

- `useReducer` — not used. Multi-field state is several `useState` calls.
- `useMutation` — not used. Writes are `api.post` inside the event handler.
- Custom hooks other than `useAuth`.
- A global store (Redux, Zustand, and similar).

No tutorials for those.

## Page outline

1. The two rules.
2. The table.
3. The `useMemo` / `useCallback` caution.
4. The two `useRef` jobs.
5. The "not in this app" list, with links back to the effect lesson and the API lesson for the patterns used instead.

## Links

- Glossary.
- Lessons: state, effects, routing, context, query.
