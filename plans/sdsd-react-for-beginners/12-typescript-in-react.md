# TypeScript in components

- **Nav title:** TypeScript in components
- **Page:** `docs/sdsd/guides/typescript-in-react.md`
- **Reader can:** Type a component's props, a DOM event, a state union, and an `api.get` response the way these files do.

## Explain simply

This lesson is not a TypeScript course. It is the handful of types that show up because the file is React.

Assume the reader can already read `interface`, `type`, `|`, `?`, generics, and `import type`.

Patterns to show, each from a file they have already seen:

1. **Props interface, then destructure.** `AlertsProps`, `ChallengeStatusBadgeProps`, `ConfirmModalProps`. Optional `?`. A function prop's return type (`() => Promise<void>` vs `() => void`).
2. **`ReactNode`.** Anything the parent may nest or pass as markup: elements, strings, `null`. `ShellChrome`'s `children` and `notifications`. Import it with `import type { ReactNode } from 'react'`.
3. **Events.** `FormEvent` from `react` on submit (`SetGoalWeightPage`, `PinLoginPage`). Change handlers in this app are often inline `(event) => setUserId(event.target.value)` and let inference work. Show one explicit annotation only if a file has one. Do not invent `ChangeEvent<HTMLInputElement>` if the file does not write it. Say inference is why the inline arrow often has no annotation.
4. **Unions as state machines.** `AuthState` in `AuthContext.tsx`. Narrow with `state.status === 'authenticated'` before using `state.identity`. Also `AddWeighInPage`'s `useState<'add' | 'reasons'>('add')` as a smaller union. Quote that one line.
5. **Generics on the API.** `api.get<RulesResponse>(...)` and `api.get<AuthIdentity>('/api/auth/me')`. The generic is the caller's claim about the JSON. Repeat the caveat from the API lesson in one sentence.
6. **`import type`.** Used so a type-only import is erased. Show one real import (`import type { FormEvent } from 'react'` or `import type { ReactNode }`).

`as` appears in `ChallengeStatusBadge` (`as ChallengeStatus` after a runtime check) and in `apiFetch` (`as T`). Explain the badge cast as "the check narrowed the value, and the cast tells TypeScript." Explain `as T` as the unchecked JSON boundary. Do not encourage new casts.

## C# bridge

A props interface is a request DTO for the component. `ReactNode` is loosely like `RenderFragment` in Blazor, or an `IHtmlContent`, if a Blazor note helps; keep it to one clause because the reader may not know Blazor. Prefer: "a slot that can hold markup."

The `AuthState` union is an algebraic type. C# would be three records and a `switch` on the type. Here the discriminant is the `status` field.

`api.get<T>` is `GetFromJsonAsync<T>` at the call site, without a shared generated contract.

## Examples

Only files already in the path. No new component. Re-read each quote before pasting.

- `Alerts.tsx`, `ConfirmModal.tsx`, `ShellChrome.tsx` (props)
- `SetGoalWeightPage.tsx` (`FormEvent`)
- `AuthContext.tsx` (`AuthState`, `useMemo<AuthContextValue>`)
- `AddWeighInPage.tsx` (the step union line only)
- `client.ts` (`apiFetch<T>`, `as T`)
- `ChallengeStatusBadge.tsx` (the `as` after the `status === 1 || ...` check)

## Page outline

1. What this page will not reteach.
2. Props and `ReactNode`.
3. Events and inference.
4. Unions and narrowing, with the auth `if`.
5. Generics at the JSON boundary, and the two `as` sites.
6. A short "when you add a component" checklist: props interface, `ReactNode` for slots, union for a mode flag, `api.get<TheInterface>`, no `as` unless the JSON or a DOM value will not narrow.

## Leave out

- `strict` compiler flags and `tsconfig`. One line pointing at `6.Frontend/tsconfig.json` if a reader wants the compiler setup. Do not explain project references unless you re-read them and they matter.
- Generics beyond the `api` call.

## Links

- Previous: Server state with TanStack Query.
- Next: Read a page.
- Glossary: the type terms that are also React terms (props, `ReactNode`) link back here.
