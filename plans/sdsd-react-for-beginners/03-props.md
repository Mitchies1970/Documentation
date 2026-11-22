# Props

- **Nav title:** Props
- **Page:** `docs/sdsd/guides/props.md`
- **Reader can:** Add a value or a callback to a component the way this app does, and read `children`.

## Explain simply

Props are the arguments. The parent passes them as attributes on the tag. The child receives one object and usually destructures it.

They are read-only inside the child. The child does not assign to `props.success`. If the screen must change, the parent holds the state and passes a new value on the next render, or the child calls a function the parent passed in. State is the next lesson; here, only show the callback shape.

Two kinds of prop in this app:

- Data: `success`, `error`, `status`, `title`.
- Functions: `onConfirm`, `onClose`. The `on` prefix is a convention, not a language rule.
- `children`: whatever the parent nested between the opening and closing tags. Type it as `ReactNode`.

Optional props use `?`. `Alerts` treats a missing or empty string as "show nothing" because `&&` stops on a falsy value.

## C# bridge

Props are method parameters, not constructor injection and not `ViewData`. A callback prop is an `Action` or `Func` the caller hands in. `children` is the closest thing to a Razor `@RenderBody()`, except the parent writes the inner UI in JavaScript.

## Examples

1. `SDSD/6.Frontend/src/components/Alerts.tsx` — whole file.
   - `AlertsProps`: `success?`, `warning?`, `error?`, each `string | null`.
   - Usage: `{success && ( <div className="alert alert-success">...`
   - The fragment `<>...</>` so three alerts can be returned without a wrapper div.
   - `role="status"` / `role="alert"` and `data-testid`. One sentence on why the alert is announced. Do not turn this into an accessibility guide.

2. `SDSD/6.Frontend/src/components/ConfirmModal.tsx` — the props only, not the whole modal.
   - `ConfirmModalSpec`: `title`, `bodyText`, `submitLabel`, optional `submitClassName`, `onConfirm: () => Promise<void>`.
   - `ConfirmModalProps`: `spec: ConfirmModalSpec | null`, `onClose: () => void`.
   - `if (!spec) return null` — a component may return nothing.
   - The submit button calls `spec.onConfirm()`. The parent decides what "confirm" means. That is how a reusable dialog stays free of team or admin logic.

3. `children`, from `ShellChrome` (composition lesson owns the layout story). Show only the prop types:

```tsx
notifications?: ReactNode
children: ReactNode
```

And the call in `Layout.tsx`: `<ShellChrome identity={identity} notifications={...}><Outlet /></ShellChrome>`. The nested `<Outlet />` arrives as `children`.

## Page outline

1. Props as the function argument. Show a tag and the matching parameter list side by side (`Alerts`).
2. Optional props and `&&`.
3. Callback props (`ConfirmModal`).
4. `children` and `ReactNode` (`ShellChrome` / `Layout`).
5. A rule of thumb: data flows parent to child. The child asks for a change by calling a function.

## Leave out

- How `useState` stores `spec`. The state lesson and the composition lesson cover the parents.
- `useId` inside the modal. The hooks reference can list it. One clause is enough if the snippet includes `labelId`: it generates a stable id for the label.

## Links

- Previous: Components and JSX.
- Next: Composition.
