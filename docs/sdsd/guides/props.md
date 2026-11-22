# Props

Props are the arguments. The parent passes them as attributes on the tag. The child receives one object and usually destructures it.

They are read-only inside the child. The child does not assign to `props.success`. If the screen must change, the parent holds the value and passes a new one on the next render, or the child calls a function the parent passed in. How the parent stores that value is the next lesson. Here, the callback shape is enough.

!!! tip "If you know C#"
    Props are method parameters. They are not constructor injection and they are not `ViewData`. A callback prop is an `Action` or `Func` the caller hands in. `children` is the closest thing to a Razor `@RenderBody()`, except the parent writes the inner UI in JavaScript.

Two kinds of prop show up constantly in this app, plus one special name:

- Data: `success`, `error`, `status`, `title`.
- Functions: `onConfirm`, `onClose`. The `on` prefix is a convention, not a language rule.
- `children`: whatever the parent nested between the opening and closing tags. The type is `ReactNode`.

## Data props on `Alerts`

`SDSD/6.Frontend/src/components/Alerts.tsx` is the whole file. The tag and the parameter list are the same three names.

`NotificationsPage` renders:

```tsx
<Alerts success={success} error={error} />
```

The function receives them here:

```tsx
export default function Alerts({ success, warning, error }: AlertsProps) {
```

`warning` is missing on that tag, which is legal because each field is optional (`?`). `PasskeyManagePage` passes only `error`. A missing prop arrives as `undefined`.

```tsx
interface AlertsProps {
  success?: string | null
  warning?: string | null
  error?: string | null
}

export default function Alerts({ success, warning, error }: AlertsProps) {
  return (
    <>
      {success && (
        <div className="alert alert-success" role="status" data-testid="success-message">
          {success}
        </div>
      )}
      {warning && (
        <div className="alert alert-warning" role="alert" data-testid="warning-message">
          {warning}
        </div>
      )}
      {error && (
        <div className="alert alert-danger" role="alert" data-testid="error-message">
          {error}
        </div>
      )}
    </>
  )
}
```

`success && (...)` renders the `<div>` only when `success` is a non-empty string. `null`, `undefined`, and `''` are falsy, so `&&` stops and React draws nothing. That is why an omitted or empty message leaves no alert.

The fragment `<>...</>` lets the function return three alerts without a wrapper `<div>`. The success alert uses `role="status"`; the warning and the error use `role="alert"`, so assistive technology can announce them. `data-testid` is the same test hook as on the badge.

## A callback prop on `ConfirmModal`

`SDSD/6.Frontend/src/components/ConfirmModal.tsx` keeps the dialog reusable by refusing to know what "confirm" means. The parent passes a spec object. The spec carries the words and the function.

```tsx
export interface ConfirmModalSpec {
  title: string
  bodyText: string
  submitLabel: string
  submitClassName?: string
  onConfirm: () => Promise<void>
}

interface ConfirmModalProps {
  spec: ConfirmModalSpec | null
  onClose: () => void
}
```

`onConfirm` returns a promise (`Promise<void>`). `onClose` returns nothing (`void`). `submitClassName` is optional.

When there is nothing to confirm, the component returns nothing:

```tsx
if (!spec) return null
```

The submit button's click handler awaits the parent's function:

```tsx
await spec!.onConfirm()
```

The `!` is there because that line sits in a nested function, and TypeScript does not keep the `if (!spec) return` narrowing for it. The button itself is `onClick={submit}`. Closing uses the other callback: `onClick={onClose}`.

`TeamDetailsPage` is one parent that decides the meaning. Resetting a PIN fills the spec like this:

```tsx
setConfirm({
  title: `Reset PIN for ${member.displayName}?`,
  bodyText: 'They will be prompted to set a new PIN on next login.',
  submitLabel: 'Reset PIN',
  submitClassName: 'btn btn-warning',
  onConfirm: async () => {
    const result = await api.post<MutationResponse>(
      `/api/teams/${teamId}/members/${member.userId}/reset-pin`
    )
    setSuccess(result.message)
    await load()
  }
})
```

The modal never mentions teams or PINs. It only calls `onConfirm`. The same page renders:

```tsx
<ConfirmModal spec={confirm} onClose={() => setConfirm(null)} />
```

`onClose` asks the parent to clear the spec. The `api.post` inside `onConfirm` is the JSON API lesson. The point here is which file owns the action.

## `children`

`ShellChrome` types two slots as markup. Open `SDSD/6.Frontend/src/components/ShellChrome.tsx`:

```tsx
import type { ReactNode } from 'react'

interface ShellChromeProps {
  identity: AuthIdentity | null
  notifications?: ReactNode
  children: ReactNode
}
```

`ReactNode` means an element, a string, `null`, or a list of those. `notifications` is optional. `children` is required: it is the special prop React fills from whatever sits between the tags.

`Layout.tsx` passes both:

```tsx
<ShellChrome identity={identity} notifications={identity ? <NotificationMenu /> : null}>
  <FlashAlerts error={identity?.errorMessage} success={identity?.successMessage} />
  <Outlet />
</ShellChrome>
```

`NotificationMenu` is passed as a prop. That value is still a `ReactNode`. `FlashAlerts` and `Outlet` arrive together as `children`. `ShellChrome` renders `{notifications}` in the navbar and `{children}` inside `<main>`. The [composition](composition.md) lesson follows this layout down to a page. This lesson only needs the prop.

## The direction of data

Data flows from parent to child. The child asks for a change by calling a function the parent passed in. The child does not reach up and rewrite the parent's variables.

## Continue

Previous: [Components and JSX](components-and-jsx.md).

Next: [Composition](composition.md).
