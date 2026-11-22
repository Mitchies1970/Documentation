# Routing

React Router picks a component from the URL. The browser does not ask the server for a new HTML document when the user follows a `Link`. The host already sent the shell. The router swaps the component inside `<Outlet />`, which the [composition](composition.md) lesson followed through `Layout`.

A refresh still needs the host to return that shell. That fallback is described in [How a request is served](../reference/react-frontend.md#how-a-request-is-served).

!!! tip "If you know C#"
    `App.tsx` is the route table, like MVC conventional routes, but it runs in the browser. `:id` is a route parameter, like `{id}` on an action. `useSearchParams` is `Request.Query`. `useNavigate` is a redirect that does not send a new HTML document. `window.location.assign` is the redirect that does. The server still has routes for `/api` and for OAuth. Both tables exist. The [React frontend](../reference/react-frontend.md) reference is the map of which prefix is which.

## The basename

`BrowserRouter` in `src/main.tsx` uses `basename="/app"`:

```tsx
const SPA_BASENAME = '/app'
```

```tsx
<BrowserRouter basename={SPA_BASENAME}>
```

A route path of `WeighIn/Add` is the public URL `/app/WeighIn/Add`. Paths in `App.tsx` do not repeat `/app`.

## The route table

`App` renders `<Routes>`. One parent `<Route element={<Layout />}>` wraps the pages, so every page gets the chrome. The index route and the `*` route both render `HomePage`.

```tsx
function HomePage() {
  const { state } = useAuth()
  if (state.status === 'authenticated' && identityHasTeam(state.identity)) {
    return <DashboardPage />
  }
  return <SelectTeamPage />
}
```

`useAuth` is how the page reads the session. The [context](context.md) lesson walks that hook. `identityHasTeam` lives next to it:

```tsx
export function identityHasTeam(identity: AuthIdentity): boolean {
  if (identity.authStatus) return identity.authStatus === 'inTeam'
  return identity.loserGroupId !== null
}
```

If `authStatus` is set, the user has a team only when it is `'inTeam'`. Otherwise the fallback is `loserGroupId !== null`.

The opening of the table, then two later lines, show the shape. The file has many more `<Route>` entries. The [route catalog](../reference/react-frontend.md#routes) lists them by area.

```tsx
<Routes>
  <Route element={<Layout />}>
    <Route index element={<HomePage />} />
    <Route path="Login/SelectTeam" element={<SelectTeamPage />} />
    <Route path="Login/LoginPrompt" element={<SelectTeamPage />} />
    <Route path="Account/PinLogin" element={<PinLoginPage />} />
    <Route path="Account/ChangePin" element={<ChangePinPage />} />
    <Route path="Account/ResetPin" element={<ResetPinPage />} />
    <Route path="Account/AccessDenied" element={<AccessDeniedPage />} />
    <Route path="Home/Privacy" element={<PrivacyPage />} />
    <Route path="Home/DataDeletion" element={<DataDeletionPage />} />
    <Route path="Home/Rules" element={<RulesPage />} />
    <Route path="Home/MoveWeighIns" element={<MoveWeighInsPage />} />
    <Route path="WeighIn/Add" element={<AddWeighInPage />} />
```

Many areas also register an `/Index` alias. Leaderboard is one:

```tsx
<Route path="LeaderBoard" element={<LeaderboardPage />} />
<Route path="LeaderBoard/Index" element={<LeaderboardPage />} />
```

The team page takes an id. Anything that matches no earlier path renders `HomePage` again:

```tsx
<Route path="Team/Details/:id" element={<TeamDetailsPage />} />
<Route path="*" element={<HomePage />} />
```

## Path params and the query string

`TeamDetailsPage` reads `:id` and turns it into a number before the request:

```tsx
const { id } = useParams()
const teamId = Number(id)
```

```tsx
const payload = await api.get<TeamDetailsPayload>(`/api/teams/${teamId}`)
```

`DashboardPage` reads the query string with `useSearchParams`. `buildDashboardQuery` copies `year`, `month`, and `ownerUserLoserGroupId` onto `/api/dashboard`:

```tsx
function buildDashboardQuery(searchParams: URLSearchParams): string {
  const params = new URLSearchParams()
  const year = searchParams.get('year')
  const month = searchParams.get('month')
  const owner = searchParams.get('ownerUserLoserGroupId')
  if (year) params.set('year', year)
  if (month) params.set('month', month)
  if (owner) params.set('ownerUserLoserGroupId', owner)
  const query = params.toString()
  return query ? `/api/dashboard?${query}` : '/api/dashboard'
}
```

Changing the query string changes `searchParams`, which changes `load`, which reruns the effect from the [load data](effects-and-loading.md) lesson.

## `Link`, `useNavigate`, and a full page load

`<Link>` is client navigation. `ShellChrome` is full of these. The weigh-in icon is:

```tsx
<Link className="nav-link px-2 text-dark" to="/WeighIn/Add" title="Weigh-In">
```

`useNavigate()` is client navigation from code. `CreateTeamWizard` uses it after a successful create, and it can carry a little data on `state`:

```tsx
navigate(`/Team/Details/${result.loserGroupId}`, { state: { success: result.successMessage } })
```

`AddWeighInPage`, `InsightsPage`, and `StartExperimentPage` also call `navigate`. One weigh-in case is not a save: if the form response says there is no goal weight, `AddWeighInPage` calls `navigate('/WeighIn/SetGoalWeight')`.

`window.location.assign(...)` loads a document. Logout does that on purpose. `logOut` posts to the API, then sends the browser to the host's team-selection URL:

```tsx
async function logOut() {
  await api.post('/api/auth/logout')
  window.location.assign('/Login/SelectTeam')
}
```

Setting a goal weight does the same kind of navigation with `window.location.assign(import.meta.env.BASE_URL)`, back to `/app/`. PIN login assigns a URL too. Use this when the next page must be a full load. These calls are what the code does.

## Two uses of `useLocation`

`useLocation` is the current URL, plus anything `navigate` passed as `state`.

`TeamDetailsPage` reads the flash from the wizard. The `as` cast tells TypeScript which shape to expect. The TypeScript lesson covers casts. The line is:

```tsx
const flashedSuccess = (location.state as { success?: string } | null)?.success ?? null
```

`CreateTeamWizard` reads `location.pathname` to decide which step the URL is asking for:

```tsx
const requestedStep = stepFromPath(location.pathname, isEdit)
```

```tsx
function stepFromPath(pathname: string, isEdit: boolean): number {
  if (isEdit) {
    if (pathname.includes('EditCadence')) return 2
    if (pathname.includes('EditTimeline')) return 3
    if (pathname.includes('EditExtras')) return 4
    return 1
  }
  if (pathname.includes('CreateTimeZone')) return 2
  if (pathname.includes('CreateCadence')) return 3
  if (pathname.includes('CreateTimeline')) return 4
  if (pathname.includes('CreateExtras')) return 5
  return 1
}
```

## Host pages stay on `<a>`

Google and Facebook on the PIN page are ordinary links, because those screens are MVC, not React routes:

```tsx
href={mvcLoginUrl('/Account/Login', window.location, webAppOrigin())}
```

```tsx
href={mvcLoginUrl('/Account/FacebookLogin', window.location, webAppOrigin())}
```

The list of paths that stay on the host is in [Paths that stay on the host](../reference/react-frontend.md#paths-that-stay-on-the-host).

## Continue

Previous: [Load data on mount](effects-and-loading.md).

Next: [Context](context.md).
