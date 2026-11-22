# Lists and conditions

JSX is an expression. Anything that produces a value can sit inside `{}`. A condition that produces no element uses `null`, or a falsy `&&`.

!!! tip "If you know C#"
    `&&` and the ternary are the JSX form of `@if` / `@else` around a partial. `map` is a `foreach` that builds elements instead of writing into a stream. `key` has no Razor equivalent. It is there because React updates an existing tree instead of sending a new page.

## `&&`, a ternary, and `null`

`Alerts` shows a message only when the string is non-empty. A missing or empty value is falsy, so the `<div>` is skipped:

```tsx
{success && (
  <div className="alert alert-success" role="status" data-testid="success-message">
    {success}
  </div>
)}
```

`SetGoalWeightPage` has two branches, and the empty branch is `null` so the form keeps no extra node:

```tsx
{error ? <div className="text-danger mb-2">{error}</div> : null}
```

`DashboardPage` chooses between two pieces of UI. When `data.seasonFinale` is set, the finale card renders. Otherwise the weekly result renders, and honors render only when that field is set:

```tsx
{data.seasonFinale ? (
  <SeasonFinaleCard seasonFinale={data.seasonFinale} />
) : (
  <>
    <WeeklyResultCard weeklyResult={data.weeklyResult} />
    {data.teamHonors ? <TeamHonors teamHonors={data.teamHonors} /> : null}
  </>
)}
```

The [composition](composition.md) lesson shows where this sits on the page. The condition is the point here.

## Early return

An early `return` is an `if` before the main markup. Use it when the rest of the function should not run.

`RulesPage` does this three times, after its `useState` and `useEffect` calls. The hooks stay at the top. The returns come next:

```tsx
if (error) {
  return <div className="alert alert-danger">{error}</div>
}

if (!rules) {
  return null
}

if (!rules.success) {
  return (
    <div className="alert alert-warning">
      <span className="lead">
        {rules.errorMessage ??
          'You are not assigned to a team. Please contact your competition organizer.'}
      </span>
    </div>
  )
}
```

Returning `null` while `rules` is still null is this page's loading behavior: a blank content area, not a spinner. The effects lesson is what makes `rules` non-null.

Two other pages are honest about a different wait:

- `Layout` renders a spinner while auth status is `'loading'`. See the [composition](composition.md) lesson.
- `DashboardPage` renders a spinner while `loading && !data`:

```tsx
if (loading && !data) {
  return (
    <div className="text-center py-5" role="status">
      <div className="spinner-border text-primary" />
    </div>
  )
}
```

Same idea, three shapes: blank, spinner for auth, spinner for the dashboard.

## `map` and `key`

A list is `array.map(...)`. Each element `map` returns needs a stable `key` so React can match rows across renders. Put `key` on the element `map` returns. Prefer an id from the data. Do not use the array index when the list can grow, shrink, or reorder. These examples use ids. This page does not claim the rest of the app never uses an index.

`PinLoginPage` builds the user `<select>` from `users`. The key is `user.userId`:

```tsx
{users.map((user) => (
  <option key={user.userId} value={user.userId}>
    {user.displayName}
  </option>
))}
```

`NotificationMenu` shows a sentence when the list is empty, and otherwise maps each row. The key is `notification.userNotificationId`:

```tsx
{recent.length === 0 ? (
  <li>
    <span className="dropdown-item-text text-muted">No new notifications</span>
  </li>
) : (
  recent.map((notification) => {
    const href = notificationHref(notification.linkUrl)
    return (
      <li key={notification.userNotificationId}>
        <a
          className="dropdown-item d-flex flex-column py-2"
          href={href}
          data-notification-item
          data-notification-id={notification.userNotificationId}
          data-testid="notification-dropdown-item"
          onClick={(event) => {
            event.preventDefault()
            void markReadAndNavigate(notification.userNotificationId, href)
          }}
        >
          <span className="fw-semibold">{notification.title}</span>
          <span className="small text-muted text-wrap">{notification.message}</span>
          <span className="small text-muted">{formatNotificationTime(notification.createdAtUtc)}</span>
        </a>
      </li>
    )
  })
)}
```

How `recent` is loaded is the TanStack Query lesson. How `users` is loaded is the effects lesson. The click handler is not the list lesson; the `key` and the empty branch are.

## Hide a whole block

`ShellChrome` wraps several navbar items in one condition. When `inTeam` is falsy, none of them render:

```tsx
{inTeam && (
  <>
    <li className="nav-item">
      <span className="nav-link text-dark">
        <strong data-testid="app-team-name">{identity?.loserGroupName}</strong>
      </span>
    </li>
    {notifications}
    <li className="nav-item">
      <button type="button" className="btn btn-link text-dark p-0" title="Switch team" onClick={() => void switchTeam()}>
        <i className="bi bi-arrow-left-right" />
      </button>
    </li>
    <li className="nav-item">
      <span className="nav-link text-dark">
        Hello, <span data-testid="app-user-name">{identity?.userName}</span>
      </span>
    </li>
  </>
)}
```

The fragment is what `&&` returns: several `<li>` elements, and no extra wrapper in the `<ul>`.

## Continue

Previous: [State and events](state-and-events.md).

Next: Load data on mount.
