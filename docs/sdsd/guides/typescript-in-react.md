# TypeScript in components

This is not a TypeScript course. It is the handful of types that show up because the file is React. The earlier lessons already used these files. This page names the types.

You can already read `interface`, `type`, `|`, `?`, generics, and `import type`. The compiler setup is `SDSD/6.Frontend/tsconfig.json`.

!!! tip "If you know C#"
    A props interface is a request DTO for the component. `ReactNode` is a slot that can hold markup. The `AuthState` union is an algebraic type. C# would be three records and a `switch` on the type. Here the discriminant is the `status` field. `api.get<T>` is `GetFromJsonAsync<T>` at the call site, without a shared generated contract.

## Props and `ReactNode`

A component declares an interface, then destructures it. Optional fields use `?`.

`Alerts`:

```tsx
interface AlertsProps {
  success?: string | null
  warning?: string | null
  error?: string | null
}

export default function Alerts({ success, warning, error }: AlertsProps) {
```

`ChallengeStatusBadge` requires one number:

```tsx
interface ChallengeStatusBadgeProps {
  status: number
}

export default function ChallengeStatusBadge({ status }: ChallengeStatusBadgeProps) {
```

A function prop includes its return type. `ConfirmModal` uses both shapes. `onConfirm` returns a promise. `onClose` returns nothing. `submitClassName` is optional:

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

`ReactNode` is anything the parent may nest or pass as markup: elements, strings, `null`. `ShellChrome` imports it as a type, so the import is erased from the JavaScript:

```tsx
import type { ReactNode } from 'react'
```

```tsx
notifications?: ReactNode
children: ReactNode
```

## Events and inference

Submit handlers in this app annotate the event. `SetGoalWeightPage` imports the type and names it on the function:

```tsx
import type { FormEvent } from 'react'
```

```tsx
async function onSubmit(event: FormEvent) {
```

`PinLoginPage` does the same for its submit. Change handlers are usually inline, and inference fills in the event type. This one has no annotation:

```tsx
onChange={(event) => setUserId(event.target.value)}
```

No file in `src/` writes `ChangeEvent<HTMLInputElement>`. The inline arrow is enough for `event.target.value`.

## Unions and narrowing

`AuthState` in `AuthContext.tsx` is the state machine from the [context](context.md) lesson. `identity` exists only on the `'authenticated'` variant, so the check comes first:

```tsx
export type AuthState =
  | { status: 'loading' }
  | { status: 'anonymous' }
  | { status: 'authenticated'; identity: AuthIdentity }
```

```tsx
const identity = state.status === 'authenticated' ? state.identity : null
```

The provider's memo is generic on the context value. That is the same `useMemo` the context lesson showed:

```tsx
const value = useMemo<AuthContextValue>(
```

A smaller union is a mode flag. `AddWeighInPage` starts on the add step:

```tsx
const [step, setStep] = useState<'add' | 'reasons'>('add')
```

## Generics at the JSON boundary, and `as`

`api.get<T>` is the caller's claim about the JSON. `RulesPage` uses `api.get<RulesResponse>`. The auth provider uses `api.get<AuthIdentity>('/api/auth/me')`. The compiler will not catch a mismatch with the C# DTO. The [API](calling-the-api.md) lesson says the same thing.

Two casts are already in the files. Do not add new ones unless a value will not narrow.

The badge checks the number, then tells TypeScript the result is `ChallengeStatus`:

```tsx
const key = (status === 1 || status === 2 || status === 3 ? status : 2) as ChallengeStatus
```

The check already limited the value to `1`, `2`, or `3`. The cast is what the type system needs after that runtime check.

`apiFetch` casts the parsed JSON. That is the unchecked boundary:

```tsx
return (await response.json()) as T
```

`as T` means "trust the caller." A `204` uses the same escape: `return undefined as T`.

## When you add a component

1. Give it a props interface, and destructure the argument.
2. Type slots as `ReactNode`.
3. Type a mode flag as a union of string literals, as `AddWeighInPage` does.
4. Call `api.get<TheInterface>` or `api.post<TheInterface>` for the JSON.
5. Skip `as` unless the JSON, or a DOM value that will not narrow, forces it.

## Continue

Previous: [Server state with TanStack Query](server-state-and-tanstack-query.md).

Next: [Read a page](read-a-page.md).
