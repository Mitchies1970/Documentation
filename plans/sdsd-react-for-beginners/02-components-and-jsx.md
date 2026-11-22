# Components and JSX

- **Nav title:** Components and JSX
- **Page:** `docs/sdsd/guides/components-and-jsx.md`
- **Reader can:** Read a small component and point at the function, the props, and the returned markup.

## Explain simply

A component is a function whose name starts with a capital letter. It returns a description of UI. That description is JSX: JavaScript that looks like HTML.

React calls the function. You do not call it as `ChallengeStatusBadge(props)` at the call site. You write `<ChallengeStatusBadge status={1} />`. The capital letter is how JSX tells a component from a DOM tag (`<span>`).

JSX rules to show with the real file, not as an abstract list:

- One parent element (or a fragment `<>...</>` — `Alerts` is the fragment example, so mention it in one sentence and leave the walk to the props lesson).
- `className`, not `class`. `class` is a reserved word in JavaScript.
- JavaScript expressions go in `{}`. Text and numbers can sit in the markup directly.
- A component file usually `export default function Name`. The importer writes `import Name from './Name'`.

This component has no state. Same inputs, same output. That is the right first example.

## C# bridge

A Razor partial (`_Badge.cshtml`) plus its model. The difference: the partial is text the server concatenates. The component is a function the browser runs again later. The TypeScript `interface` on the props is the view model.

## Examples

`SDSD/6.Frontend/src/features/shared/ChallengeStatusBadge.tsx` — the whole file is short enough to show.

Walk it in order:

1. `ChallengeStatus` and the two `Record` maps (`STATUS_LABEL`, `STATUS_CLASS`). Plain TypeScript. They are not React.
2. `ChallengeStatusBadgeProps` with `status: number`.
3. Destructuring `{ status }` in the parameter list. Same as taking the model apart at the top of a method.
4. The fallback: an unknown number becomes `2` ("Completed"). Show the line. Do not invent other statuses.
5. The returned `<span className={...}>`. Template string builds the Bootstrap classes (`badge`, `bg-primary`, and so on).
6. `data-testid="challenge-status"`. One sentence: tests and Playwright look for this. The testing lesson uses the idea later.

Real call sites, one line each: `DailyChallengeDetailsPage` renders `<ChallengeStatusBadge status={heatMap.status} />`, and `DailyChallengeHistoryPage` renders `<ChallengeStatusBadge status={item.status} />`. Quote one of them so the tag is not hypothetical.

## Page outline

1. Function, capital letter, JSX.
2. The badge file, annotated in the order above.
3. `className`, `{}`, and one parent.
4. Default export and the import line.
5. What "render" means: React called the function. No state yet, so it does not call it again until the parent renders again.

## Leave out

- `useState`, effects, routing.
- A second full component. `Alerts` belongs to props and lists.

## Links

- Previous: Start here.
- Next: Props.
