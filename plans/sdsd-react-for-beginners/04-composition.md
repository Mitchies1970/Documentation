# Composition

- **Nav title:** Composition
- **Page:** `docs/sdsd/guides/composition.md`
- **Reader can:** Follow a screen from the route layout down to a section component, and see a parent pass a slice of data plus a callback.

## Explain simply

Screens in this app are not one giant function. A parent renders child components and passes each child only what that child needs.

Three levels, in this order:

1. **Chrome around every page.** `Layout` waits for auth, then renders `ShellChrome`. The page itself is `children`. React Router fills that slot with `<Outlet />`, which is the matched child route.
2. **A page that owns the data.** `DashboardPage` loads one `DashboardApiResponse`, then renders many section components. Each section gets a field or two, not the whole response.
3. **A section that owns a small interaction.** `PersonalGoalTimerCluster` receives `timers` and `onTimersChange`. The dashboard keeps the response and patches one field when the child reports a change (`patchTimers`).

The parent does not reach into the child to change its variables. It passes new props, and React renders the child again.

## C# bridge

`Layout` + `Outlet` is a Razor `_Layout.cshtml` and `@RenderBody()`. The difference: the layout is a component, and which body appears is chosen by the client route table.

`DashboardPage` passing `statistics={data.statistics}` is a controller handing a small view model to a partial. The partials here are functions in their own files.

## Examples

- `SDSD/6.Frontend/src/components/Layout.tsx` — full file. Cover the loading spinner (`state.status === 'loading'`), then `identity` null vs the signed-in object, then `notifications={identity ? <NotificationMenu /> : null}`. A component can be passed as a prop. That prop is still `ReactNode`.
- `SDSD/6.Frontend/src/components/ShellChrome.tsx` — the props and the place it renders `{children}` and `{notifications}`. Do not walk every nav link. Point at one `Link` so the routing lesson has a forward reference.
- `SDSD/6.Frontend/src/features/dashboard/DashboardPage.tsx` — the return starting at the welcome heading, not the loader. Name the children in the order they appear: `PersonalGoalTimerCluster`, `CompetitionStatsHero`, `SeasonFinaleCard` or `WeeklyResultCard` + `TeamHonors`, `ProgressStatisticsCard`, `WeightHistoryCard`, `HeatMapSection`, `DailyChallengeSection`, `SeasonLeaderboard`, `ActionPlanCheckOffModal`, `FocusStrip`, `PersonalInsightSection`, `AccountabilitySection`.
- Show one patch function (`patchTimers` or `patchActionPlan`): `setData((prev) => prev ? { ...prev, personalGoalTimers: timers } : prev)`. Explain the spread as "copy the response, replace one field". This is the first immutable update in the path. Keep it short; the state lesson is next and uses a simpler `useState`.

Mermaid: `Layout` → `ShellChrome` → page, and `DashboardPage` → three example children (`CompetitionStatsHero`, `HeatMapSection`, `FocusStrip`). Not every child.

## Page outline

1. Why split a screen.
2. Layout, chrome, outlet. Include the loading branch.
3. The dashboard as an assembler. The diagram plus the prop examples `statistics={data.statistics}` and `onTimersChange={patchTimers}`.
4. Conditional children already on the dashboard: `data.seasonFinale ? <SeasonFinaleCard /> : ...` and `{data.teamHonors ? <TeamHonors /> : null}`. One paragraph. The lists lesson goes further.
5. Folder habit: shared chrome in `src/components/`, a product area in `src/features/<area>/`. A page file owns the fetch; a section file owns a block of the screen.

## Leave out

- The dashboard fetch, `useRef` request counter, and search params. Effects and routing cover those.
- The insides of each section.

## Links

- Previous: Props.
- Next: State and events.
