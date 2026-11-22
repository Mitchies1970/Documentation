# Read a page

- **Nav title:** Read a page
- **Page:** `docs/sdsd/guides/read-a-page.md`
- **Reader can:** Open a screen they have not memorized and find the route, the state, the fetch, the events, and the child components.

## Explain simply

This is a capstone, not a new API. Walk one medium page in the order a reader should use on any future page. Then point at the dashboard as the same habits at a larger size. Then give a checklist.

The guided page is `PinLoginPage` because it is one file, it fetches, it holds a form, it renders a list, it posts, and it has a test. The dashboard is the second stop: too big to read line by line here, right size to practice the checklist.

## C# bridge

Reading `PinLoginPage.tsx` is like reading one MVC action plus its view, except both are in the function: the "action" is `onSubmit` and the effect, the "view" is the `return`. There is no separate `.cshtml`.

## Walk `src/pages/PinLoginPage.tsx`

Order to write, matching the file:

1. **Route.** `App.tsx` has `Account/PinLogin`. Public URL `/app/Account/PinLogin`. It sits under `Layout`, so the chrome is there too, but this page supplies its own centered card.
2. **Types at the top.** `PinUser`, `PinLoginResult`. Local interfaces, not shared DTOs.
3. **State.** `users`, `userId`, `pin`, `error`, `pending`, `loadError`. Say which are server data (`users`, `loadError`) and which are the form (`userId`, `pin`, `error`, `pending`).
4. **Effect.** GET `/api/auth/pin-users`, `cancelled` flag. On failure, a fixed string, not `ApiError` — the `catch` ignores the reason. Say that; do not "fix" it in the prose.
5. **Derived value.** `const selected = users.find(...)`. Not state. Recomputed every render. One sentence, because it is easy to miss and useful in jobs.
6. **Submit.** `preventDefault`, POST `/api/auth/pin-login` with `{ userId: Number(userId), pin }`. Branches: `pinChangeRequired` → `window.location.assign` to the change-PIN URL via `mvcUrl`; `nextUrl` → `mvcUrl`; else `assign('/app/')`. `catch`: clear PIN, `ApiError` message or `'Invalid login attempt.'`, `setPending(false)`. Note there is no `finally`. If the success path assigns `location`, pending stays true until the document unloads. Describe that; it is why the button can stay disabled after a successful submit.
7. **Markup.** Load error alert, form, error alert, `<select>` mapped from `users` with `key={user.userId}`, controlled PIN input, first-time hint when `selected && !selected.pinSet`, submit disabled while `pending`.
8. **Host links.** Google and Facebook are `<a href={mvcLoginUrl(...)}>`, not `Link`. They leave the SPA on purpose.

Keep the quotes short and in this order. The reader should have the file open.

## Second stop: the dashboard

Do not annotate `DashboardPage` line by line. Give the checklist filled in:

| Question | Dashboard answer |
| --- | --- |
| Route | index and `*` via `HomePage`, when the user has a team |
| Data state | `data`, `error`, `loading`, plus `actionPlanModal` |
| Fetch | `load` → `GET /api/dashboard` with optional year, month, owner |
| Why it refetches | `useSearchParams` is in the `useCallback` dependency list |
| Children | the section list from the composition lesson |
| A callback up | `onTimersChange={patchTimers}` |

## Checklist to leave on the page

When you open any file in `src/features/`:

1. Find its `<Route>` in `App.tsx`.
2. List every `useState`. Mark server data versus UI.
3. Find the `useEffect` or `useQuery`. Note the URL and the dependency array.
4. Find `api.post` / button handlers.
5. Find child components and the props passed down.
6. Search the file for `data-testid` if you are about to write a test.

Suggest one exercise, not a pile: use the checklist on `src/features/weighin/AddWeighInPage.tsx`. Name what they will find so the plan is honest: two steps in state (`'add' | 'reasons'`), several fields, `useNavigate`, and more than one effect. Do not walk that file in the lesson.

## Page outline

1. How to read, in one paragraph.
2. The PIN walk, eight steps.
3. The dashboard table.
4. The checklist and the weigh-in exercise.

## Leave out

- New concepts. If a step needs a definition, link the earlier lesson.

## Links

- Previous: TypeScript in components.
- Next: Test a component.
- Back links: state, effects, routing, API, composition.
