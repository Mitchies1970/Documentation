# Routing

- **Nav title:** Routing
- **Page:** `docs/sdsd/guides/routing.md`
- **Reader can:** Find which component a `/app/...` URL renders, read a path parameter and a query string, and tell a client link from a full page load.

## Explain simply

React Router picks a component from the URL. The browser does not ask the server for a new HTML document when the user follows a `Link`. The host already sent the shell; the router swaps the component inside `<Outlet />`.

`BrowserRouter` in `main.tsx` uses `basename="/app"`. A route path of `WeighIn/Add` is the public URL `/app/WeighIn/Add`. Paths in `App.tsx` do not repeat `/app`.

`App` renders `<Routes>`. One parent `<Route element={<Layout />}>` wraps the pages, so every page gets the chrome from the composition lesson. The index route and the `*` route both render `HomePage`. `HomePage` reads auth: a signed-in user with a team gets `DashboardPage`; otherwise `SelectTeamPage`.

Three ways the URL moves:

- `<Link to="/WeighIn/Add">` — client navigation. `ShellChrome` is full of these.
- `useNavigate()` — client navigation from code, after a successful save. `AddWeighInPage`, `CreateTeamWizard`, `InsightsPage`, `StartExperimentPage`.
- `window.location.assign(...)` — the browser loads a document. Used after PIN login, after setting a goal weight (`import.meta.env.BASE_URL`), and after logout in `ShellChrome`. Use it when the next page must be a full load (host login, or a return to the SPA entry that should remount). Do not tell the reader to convert these to `navigate`. Describe what the code does.

Some host URLs are real `<a href>` links (Google and Facebook on the PIN page) because those screens are MVC, not React routes. Link the reference page for the list of paths that stay on the host. Do not copy the table.

## C# bridge

`App.tsx` is the route table, like MVC conventional routes, but it runs in the browser. `:id` is a route parameter, like `{id}` on an action. `useSearchParams` is `Request.Query`. `useNavigate` is a redirect that does not send a new HTML document. `window.location.assign` is the redirect that does.

The server still has routes for `/api` and for OAuth. Both tables exist. The reference page is the map of which prefix is which.

## Examples

- `src/main.tsx` — `BrowserRouter basename={SPA_BASENAME}` only.
- `src/App.tsx` — the `HomePage` function and the opening of `<Routes>` through a handful of `<Route>` lines (`index`, `Account/PinLogin`, `WeighIn/Add`, `Team/Details/:id`, `*`). Say that many areas also register an `/Index` alias (`LeaderBoard/Index`). Do not paste all 60 routes. Link the reference, which already lists them by area.
- `src/features/team/TeamDetailsPage.tsx` — `const { id } = useParams()`. The id comes from `Team/Details/:id`. The load calls `api.get<TeamDetailsPayload>(\`/api/teams/${teamId}\`)`. Quote that line. Re-read how `id` becomes `teamId` before describing it.
- `src/features/dashboard/DashboardPage.tsx` — `useSearchParams`, and `buildDashboardQuery` reading `year`, `month`, and `ownerUserLoserGroupId`. Changing the query string changes `searchParams`, which changes `load`, which reruns the effect from the previous lesson.
- One `Link` from `ShellChrome` (`to="/WeighIn/Add"`) next to one `window.location.assign` (`logOut` or the goal-weight success). Two paragraphs, one each.
- `useLocation` is two different jobs. `TeamDetailsPage` reads a flash message: `(location.state as { success?: string } | null)?.success`. That is the router carrying a little data with the navigation. `CreateTeamWizard` reads `location.pathname` to decide which wizard step the URL is asking for (`stepFromPath`). Show both in a short subsection. The `as` cast belongs to the TypeScript lesson; name it and move on.

## Page outline

1. The URL chooses the component. Basename.
2. Nested routes: `Layout` and `Outlet` (already seen) plus the route table snippet.
3. `HomePage` as a tiny branch on auth. Forward pointer to context: `useAuth` is how it reads the session.
4. Path params and query params.
5. `Link`, `useNavigate`, and `location.assign`.
6. Host pages stay on `<a>`. Link out.

## Leave out

- The full route catalog.
- How the server falls back to `index.html` for a refresh. One sentence plus the reference link.

## Links

- Previous: Load data on mount.
- Next: Context.
- Reference: React frontend (routes and host paths).
