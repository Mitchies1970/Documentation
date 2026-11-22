# State and events

`useState` gives a component a value that survives from one call of the function to the next, and a setter that asks React to call the function again.

```tsx
const [goalWeightKg, setGoalWeightKg] = useState('')
```

That line means: start as `''`. Read `goalWeightKg` while rendering. Call `setGoalWeightKg(next)` to change it. Do not assign `goalWeightKg = next`. That local variable disappears when the function returns, and React would not know to render again.

!!! tip "If you know C#"
    This is not a field on a controller, and it is not `ViewBag`. The controller instance is gone after the response. This value lives in the browser, tied to this component, until the component is removed. `onSubmit={onSubmit}` is like a button click handler, but the handler closes over the current state (`goalWeightKg`, and the setters).

## The goal-weight form

`SDSD/6.Frontend/src/features/weighin/SetGoalWeightPage.tsx` is the lesson. It does not load anything when it appears. The only data is what the person types.

Three state pairs:

```tsx
const [goalWeightKg, setGoalWeightKg] = useState('')
const [error, setError] = useState<string | null>(null)
const [pending, setPending] = useState(false)
```

The weight is a string because the input's `value` is text. `Number(goalWeightKg)` happens at submit time. `error` holds a message or `null`. `pending` is true while the request is in flight.

`onSubmit` is the function React calls for the form event. The argument is typed `FormEvent`, from React.

```tsx
async function onSubmit(event: FormEvent) {
  event.preventDefault()
  setError(null)
  setPending(true)
  try {
    await api.post('/api/weigh-ins/goal-weight', { goalWeightKg: Number(goalWeightKg) })
    window.location.assign(import.meta.env.BASE_URL)
  } catch (caught) {
    setError(caught instanceof ApiError ? caught.message : 'Failed to set goal weight.')
  } finally {
    setPending(false)
  }
}
```

`event.preventDefault()` stops the browser from doing a normal form POST, which would leave the single-page app. The `api.post` line calls the JSON API and throws `ApiError` on failure. The [API](calling-the-api.md) lesson owns that function. On success the page calls `window.location.assign(import.meta.env.BASE_URL)`, a full navigation to `/app/`. Not every success uses the router. The [routing](routing.md) lesson explains `Link` and `useNavigate`.

The `catch` keeps the `ApiError` message when the failure is one of those, and otherwise shows `'Failed to set goal weight.'`. `finally` always runs `setPending(false)`, including after a success that is about to navigate away.

The input displays the state and writes back on each keystroke:

```tsx
<input
  type="number"
  step="0.1"
  className="form-control"
  data-testid="goalweight-input"
  value={goalWeightKg}
  onChange={(e) => setGoalWeightKg(e.target.value)}
  required
/>
```

The button reads the same kind of state. `disabled={pending}` stops a second submit while the request is running. The form is `onSubmit={onSubmit}`.

```tsx
{error ? <div className="text-danger mb-2">{error}</div> : null}
```

That ternary is a condition inside JSX. The [lists](lists-and-conditions.md) lesson covers `&&`, ternaries, and lists. `pending` and `error` are not a second idea. They are two more `useState` pairs.

## A controlled input

The DOM is not the source of truth. The state is. A controlled input displays the state (`value={goalWeightKg}`) and writes back in `onChange`.

1. First render: `goalWeightKg` is `''`, so the input is empty.
2. The user types. React calls `onChange`.
3. `setGoalWeightKg` stores the new string and asks for another render.
4. React calls `SetGoalWeightPage` again. The input's `value` is that string, so the field shows it.

Keystroke, setter, render, `value`. The same four beats apply when `setError` or `setPending` runs.

`PinLoginPage` is the same pattern with more fields. The "read a page" lesson walks it. This lesson stops at the goal weight.

## Continue

Previous: [Composition](composition.md).

Next: [Lists and conditions](lists-and-conditions.md).
