# Lists and conditions

- **Nav title:** Lists and conditions
- **Page:** `docs/sdsd/guides/lists-and-conditions.md`
- **Reader can:** Choose among `&&`, a ternary, an early `return`, and `map` with `key`, using files that already do each.

## Explain simply

JSX is an expression. Anything that produces a value can sit inside `{}`. A condition that produces no element uses `null` or a falsy `&&`.

A list is `array.map(...)`. Each returned element needs a stable `key` so React can match rows across renders. The key goes on the element `map` returns, and it should be an id from the data, not the array index, when the rows have ids.

An early `return` is the same as an `if` before the main markup. Use it when the rest of the function should not run (no data yet, or an error).

## C# bridge

`&&` and the ternary are the JSX form of `@if` / `@else` around a partial. `map` is a `foreach` that builds elements instead of writing into a stream. `key` has no Razor equivalent; it is there because React updates an existing tree instead of sending a new page.

## Examples

Show each pattern once, from a real file. Short snippets.

| Pattern | File | What to quote |
| --- | --- | --- |
| `&&` | `src/components/Alerts.tsx` | `{success && ( <div ...> )}` |
| Ternary vs `null` | `SetGoalWeightPage.tsx` | `{error ? <div>...</div> : null}` |
| Ternary with two components | `DashboardPage.tsx` | `data.seasonFinale ? <SeasonFinaleCard /> : <>...</>` |
| Early return | `RulesPage.tsx` | `if (error) return <div className="alert...">`; `if (!rules) return null`; `if (!rules.success) return ...` |
| `map` + `key` | `src/pages/PinLoginPage.tsx` | `users.map((user) => <option key={user.userId} value={user.userId}>...)` |
| Empty vs items | `src/features/notifications/NotificationMenu.tsx` | `recent.length === 0 ? ( <li>No new notifications</li> ) : ( recent.map(...) )` with `key={notification.userNotificationId}` |
| Hide a whole block | `ShellChrome.tsx` | `{inTeam && ( <>...</> )}` |

On keys: `user.userId` and `userNotificationId` are the right examples. Say not to use the index when the list can grow, shrink, or reorder. Do not claim this app never uses an index; only show these id keys.

`RulesPage` returning `null` while `rules` is still null is the loading behavior of that page: a blank content area, not a spinner. Contrast with `Layout`, which renders a spinner while auth is `'loading'`, and `DashboardPage`, which renders a spinner while `loading && !data`. Three honest variants. The effects lesson explains how `rules` becomes non-null.

## Page outline

1. JSX expressions can be conditional.
2. `&&`, ternary, `null`.
3. Early return, with the three loading shapes.
4. `map` and `key`, user list then notification list.
5. A block hidden with `inTeam &&`.

## Leave out

- How `users` was fetched.
- The notification query. Lesson 11.

## Links

- Previous: State and events.
- Next: Load data on mount.
