# Read a page

This is a capstone, not a new API. Open `SDSD/6.Frontend/src/pages/PinLoginPage.tsx` beside this page. The order below is the order to use on any later file: route, types, state, effect, derived values, submit, markup, links that leave the app. Then the dashboard is the same habits at a larger size.

!!! tip "If you know C#"
    Reading `PinLoginPage.tsx` is like reading one MVC action plus its view, except both are in the function. The "action" is `onSubmit` and the effect. The "view" is the `return`. There is no separate `.cshtml`.

## 1. Route

`App.tsx` has `Account/PinLogin`. The public URL is `/app/Account/PinLogin`. The route sits under `Layout`, so the chrome from the [composition](composition.md) lesson is there too. This page supplies its own centered card.

```tsx
<Route path="Account/PinLogin" element={<PinLoginPage />} />
```

## 2. Types at the top

`PinUser` and `PinLoginResult` are local interfaces, not shared DTOs. `PinUser` is one row of the sign-in list. `PinLoginResult` is the POST body the page branches on: `pinChangeRequired` and `nextUrl`.

## 3. State

Six `useState` pairs. Two hold server data. Four hold the form. The [state](state-and-events.md) lesson is the goal-weight version of the same split.

| State | Kind |
| --- | --- |
| `users`, `loadError` | Server data. The list, and a message if loading it failed. |
| `userId`, `pin`, `error`, `pending` | The form. The selected user, the typed PIN, a submit error, and whether a submit is in flight. |

## 4. Effect

After the first paint the page GETs `/api/auth/pin-users`. The `cancelled` flag ignores a response that arrives after the component is gone. That pattern is the [effects](effects-and-loading.md) lesson.

On failure the `catch` ignores the reason and sets a fixed string: `'The sign-in list could not be loaded.'` It does not read `ApiError`.

## 5. A value that is not state

```tsx
const selected = users.find((user) => String(user.userId) === userId)
```

`selected` is recomputed every render from `users` and `userId`. It is not a seventh `useState`. That is easy to miss, and it is the usual way to derive a row from an id.

## 6. Submit

`preventDefault` stops a normal form POST. The body is a plain object. `userId` is text in state, so `Number(userId)` runs at submit time. The [API](calling-the-api.md) lesson owns `api.post` and `ApiError`.

```tsx
const result = await api.post<PinLoginResult>('/api/auth/pin-login', {
  userId: Number(userId),
  pin
})
```

Success does not call `navigate`. It assigns a new document, which the [routing](routing.md) lesson separated from `Link`:

- `pinChangeRequired` assigns the change-PIN URL from `mvcUrl`.
- `nextUrl` assigns that URL from `mvcUrl`.
- Otherwise it assigns `'/app/'`.

The `catch` clears the PIN, then sets `error` from `ApiError.message` or `'Invalid login attempt.'`, then `setPending(false)`. There is no `finally`. If a success path assigns `location`, `pending` stays true until the document unloads. That is why the button can stay disabled after a successful submit.

## 7. Markup

The return is the patterns from earlier lessons, in this order:

- `{loadError && (...)}` shows the load failure.
- The form is `onSubmit={onSubmit}` with `data-testid="pin-login-form"`.
- `{error && (...)}` is the submit alert, `data-testid="pin-login-error"`.
- The `<select>` is a controlled input (`value={userId}`) and maps `users` with `key={user.userId}`.
- The PIN input is controlled the same way: `value={pin}`.
- `{selected && !selected.pinSet && (...)}` shows "First-time PIN. You will be asked to choose a new one."
- The submit button is `disabled={pending}`.

## 8. Host links

Google and Facebook are `<a href={mvcLoginUrl(...)}>`, not `<Link>`. They leave the SPA on purpose. The [routing](routing.md) lesson points at the host path list.

## The dashboard, with the same questions

`DashboardPage` is too big to read line by line here. Fill the checklist in and compare.

| Question | Dashboard answer |
| --- | --- |
| Route | The index route and `*` both render `HomePage`. `HomePage` renders `DashboardPage` when the user is authenticated and `identityHasTeam`. |
| Data state | `data`, `error`, `loading`, plus `actionPlanModal` for the check-off dialog. |
| Fetch | `load` calls `GET /api/dashboard`, with optional `year`, `month`, and `ownerUserLoserGroupId`. |
| Why it refetches | `searchParams` from `useSearchParams()` is in the `useCallback` dependency list for `load`. The effect depends on `[load]`. |
| Children | The section list in the [composition](composition.md) lesson. |
| A callback up | `onTimersChange={patchTimers}` |

## Checklist

When you open any file in `src/features/`:

1. Find its `<Route>` in `App.tsx`.
2. List every `useState`. Mark server data versus UI.
3. Find the `useEffect` or `useQuery`. Note the URL and the dependency array.
4. Find `api.post` and the button handlers.
5. Find child components and the props passed down.
6. Search the file for `data-testid` if you are about to write a test.

One exercise: use the checklist on `src/features/weighin/AddWeighInPage.tsx`. You will find a step union (`'add' | 'reasons'`), several fields, `useNavigate`, and more than one effect. The [TypeScript](typescript-in-react.md) lesson already quoted the step line. Do not expect this page to walk the weigh-in file.

## Continue

Previous: [TypeScript in components](typescript-in-react.md).

Next: [Test a component](test-a-component.md).

Back to: [state](state-and-events.md), [effects](effects-and-loading.md), [routing](routing.md), [the API](calling-the-api.md), [composition](composition.md).
