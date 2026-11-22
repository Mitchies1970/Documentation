# Components and JSX

A component is a function whose name starts with a capital letter. It returns a description of UI. That description is JSX: JavaScript that looks like HTML.

React calls the function. At the call site you do not write `ChallengeStatusBadge(props)`. You write a tag. The capital letter is how JSX tells a component from a DOM tag such as `<span>`.

!!! tip "If you know C#"
    Think of a Razor partial (`_Badge.cshtml`) plus its model. The partial is text the server concatenates into one response. The component is a function the browser can run again later. The TypeScript `interface` on the props is the view model.

## The badge

`SDSD/6.Frontend/src/features/shared/ChallengeStatusBadge.tsx` is the whole component. It has no state. The same inputs produce the same output, which makes it the right first file.

```tsx
type ChallengeStatus = 1 | 2 | 3

const STATUS_LABEL: Record<ChallengeStatus, string> = {
  1: 'Active',
  2: 'Completed',
  3: 'Ended early'
}

const STATUS_CLASS: Record<ChallengeStatus, string> = {
  1: 'bg-primary',
  2: 'bg-secondary',
  3: 'bg-warning text-dark'
}

interface ChallengeStatusBadgeProps {
  status: number
}

export default function ChallengeStatusBadge({ status }: ChallengeStatusBadgeProps) {
  const key = (status === 1 || status === 2 || status === 3 ? status : 2) as ChallengeStatus
  return (
    <span className={`badge ${STATUS_CLASS[key]} ms-1`} data-testid="challenge-status">
      {STATUS_LABEL[key]}
    </span>
  )
}
```

Read it from the top:

1. `ChallengeStatus` and the two `Record` maps are plain TypeScript. They are not React. `1` is Active, `2` is Completed, `3` is Ended early. Each number also picks a Bootstrap background class.
2. `ChallengeStatusBadgeProps` says the function takes `status: number`.
3. `{ status }` in the parameter list pulls that field out of the argument. Same idea as taking the model apart at the top of a method.
4. An unknown number becomes `2` ("Completed"):

    ```tsx
    const key = (status === 1 || status === 2 || status === 3 ? status : 2) as ChallengeStatus
    ```

5. The returned `<span>` is the UI. The template string builds the Bootstrap classes (`badge`, then `bg-primary` or one of the other entries, then `ms-1`).
6. `data-testid="challenge-status"` is an attribute tests look for. The [testing](test-a-component.md) lesson uses that idea later. Playwright looks for the same kind of id.

## JSX rules this file is already using

- **One parent.** The `return` has a single `<span>`. A component can also return a fragment, `<>...</>`, when it needs several elements and no wrapper. `Alerts` does that. The [props](props.md) lesson walks that file.
- **`className`, not `class`.** `class` is a reserved word in JavaScript, so JSX uses `className` for the HTML `class` attribute.
- **`{}` for JavaScript.** `{STATUS_LABEL[key]}` runs that expression and puts the string in the span. The surrounding text of a tag can sit in the markup directly; this span's text happens to come from the map.
- **Default export.** `export default function ChallengeStatusBadge` means the importer chooses the local name. In practice the importer uses the same name:

```tsx
import ChallengeStatusBadge from '../shared/ChallengeStatusBadge'
```

That line is in `DailyChallengeDetailsPage.tsx`. The tag on the details header is:

```tsx
<ChallengeStatusBadge status={heatMap.status} />
```

`DailyChallengeHistoryPage` does the same with a row: `<ChallengeStatusBadge status={item.status} />`.

## What "render" means

React called `ChallengeStatusBadge`. That call is a render. This function stores nothing, so React does not call it again until the parent renders again and passes props in once more.

## Continue

Previous: [Start here](learn-react.md).

Next: [Props](props.md).
