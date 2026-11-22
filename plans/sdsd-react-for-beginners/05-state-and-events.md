# State and events

- **Nav title:** State and events
- **Page:** `docs/sdsd/guides/state-and-events.md`
- **Reader can:** Explain why typing in a controlled input updates the screen, using the goal-weight form.

## Explain simply

`useState` gives a component a value that survives from one call of the function to the next, and a setter that asks React to call the function again.

`const [goalWeightKg, setGoalWeightKg] = useState('')` means: start as `''`. Read `goalWeightKg` while rendering. Call `setGoalWeightKg(next)` to change it. Do not assign `goalWeightKg = next`. That local variable disappears when the function returns, and React would not know to render again.

A controlled input displays the state (`value={goalWeightKg}`) and writes back in `onChange`. The DOM is not the source of truth. The state is.

Events are functions passed to `onSubmit`, `onChange`, `onClick`. React calls them. `event.preventDefault()` stops the browser from doing a normal form POST, which would leave the single-page app.

`pending` disables the button for the duration of the request. `error` holds a message to show. Both are state.

## C# bridge

This is not a field on a controller, and it is not `ViewBag`. The controller instance is gone after the response. This value lives in the browser, tied to this component, until the component is removed.

`onSubmit={onSubmit}` is like a button click handler, but the handler closes over the current state (`goalWeightKg`, and the setters).

## Examples

`SDSD/6.Frontend/src/features/weighin/SetGoalWeightPage.tsx` — the whole file is the lesson.

Walk:

1. Three state pairs: `goalWeightKg` (`string`), `error` (`string | null`), `pending` (`boolean`). Why the weight is a string: the input's `value` is text. `Number(goalWeightKg)` happens at submit time.
2. `onSubmit(event: FormEvent)`. `preventDefault`, clear the error, set pending, `api.post('/api/weigh-ins/goal-weight', { goalWeightKg: Number(goalWeightKg) })`. The API lesson owns `api.post`. Here, say "this calls the JSON API and throws `ApiError` on failure."
3. Success uses `window.location.assign(import.meta.env.BASE_URL)`, a full navigation to `/app/`. One sentence: not every success uses the router. The routing lesson explains `Link` and `useNavigate`.
4. `catch`: `caught instanceof ApiError ? caught.message : 'Failed to set goal weight.'`
5. `finally`: `setPending(false)`.
6. The input: `value`, `onChange={(e) => setGoalWeightKg(e.target.value)}`, `required`, `data-testid="goalweight-input"`.
7. The button: `disabled={pending}`.
8. `{error ? <div className="text-danger mb-2">{error}</div> : null}` — forward pointer to the lists lesson.

State the render cycle in four beats: first render (empty string) → user types → `setGoalWeightKg` → React calls `SetGoalWeightPage` again with the new string → the input shows it.

## Page outline

1. What `useState` returns.
2. Why you must use the setter.
3. The goal-weight file, in the order above.
4. Controlled input, drawn as: keystroke → setter → render → `value`.
5. `pending` and `error` as the same mechanism, not a second concept.
6. One sentence pointing at `PinLoginPage`, which is the same pattern with more fields. Do not walk it here.

## Leave out

- `useEffect`. This page does not load anything on mount.
- The request body shape versus the C# command. The API lesson covers that.
- `useCallback` / functional updates. The dashboard's `setData(prev => ...)` was enough in composition.

## Links

- Previous: Composition.
- Next: Lists and conditions.
