# React glossary

- **Nav title:** React glossary
- **Page:** `docs/sdsd/reference/react-glossary.md`
- **Reader can:** Look up a word from the lessons and land on the file that shows it.

## What this page is

A reference, not a lesson. Each entry is a few lines: a plain definition, the SDSD file to open, and a link to the guide that teaches it. Alphabetical.

Do not introduce terms the lessons never use. Do not define Redux, JSX runtime, reconciliation algorithm, or concurrent features. If "render" needs a sentence about updating the DOM, keep it to "React calls your function again and updates the elements that changed."

## Entries

Write these, and only these, unless a lesson plan gained a term during the writing pass. Then add it here in the same pass.

| Term | Point at | Guide |
| --- | --- | --- |
| Basename | `main.tsx` `SPA_BASENAME` | Routing |
| Children | `ShellChrome` `children` | Props, Composition |
| Component | `ChallengeStatusBadge` | Components and JSX |
| Context | `AuthContext` | Context |
| Controlled input | `SetGoalWeightPage` input | State and events |
| Dependency array | `RulesPage` effect `[]` | Load data on mount |
| Effect | `RulesPage`, `PinLoginPage` | Load data on mount |
| Hook | `useState` as the first example; "a function whose name starts with `use`, called at the top of the component" | State; hooks reference |
| JSX | the badge `return` | Components and JSX |
| Key | `key={user.userId}` | Lists and conditions |
| Prop | `AlertsProps` | Props |
| Provider | `AuthProvider`, `QueryClientProvider` | Context, Query |
| Query | `NotificationMenu` `useQuery` | Server state |
| Render | `createRoot` in `main.tsx`, then "calling the component function" | Start here |
| Route | `App.tsx` `<Route>` | Routing |
| State | `useState` in `SetGoalWeightPage` | State and events |

`data-testid` can be a short entry pointing at the testing guide. It is an attribute this project uses, not a React feature. Say that.

## Page outline

1. One sentence: definitions for the learning path, examples from `6.Frontend`.
2. The entries.
3. Link to the hooks reference for the function list, and to the existing React frontend reference for hosting and HTTP.

## Leave out

- Hosting, API prefixes, npm. Those references exist.

## Links

- Guides: the learning path start page.
- Reference: Hooks in this app, React frontend.
