# Plan: React for beginners (SDSD)

Status: plans only. Do not publish these files. The pages they describe are not written yet.

## Why this set exists

The reader knows JavaScript, writes backend C#, and has used TypeScript. They are new to React and want the skill to transfer to later jobs. The examples must come from this app, in `SDSD/6.Frontend/`, and the explanations must stay simple.

These pages teach React. They do not replace the two pages that already describe the product:

| Already published | Job it does | Do not repeat it |
| --- | --- | --- |
| `docs/sdsd/guides/run-the-react-frontend.md` | Start Vite, the AppHost, and the host | npm scripts, ports, `Frontend:*` keys |
| `docs/sdsd/reference/react-frontend.md` | How the SPA is hosted, the route table, session, API prefixes | Proxy, SPA fallback, antiforgery design, the prefix catalog |

Link to those pages. A learning page may use one short snippet from a file they describe, then send the reader there for the operational detail.

## Where the pages go

Product slug: `sdsd`. Site: MkDocs Material in this repo. Follow `.cursor/skills/document-product/SKILL.md`.

| Kind of page | Folder | Why |
| --- | --- | --- |
| Lessons, in reading order | `docs/sdsd/guides/` | A lesson is a task: "understand this idea by reading this component" |
| Lookup after the lessons | `docs/sdsd/reference/` | Glossary and the hook list are references, not tutorials |
| HTTP catalog | none | `reference/react-frontend.md` already lists `/api` prefixes. A new `api/` section would copy that table. |

One topic per file. Filenames are kebab-case.

## Audience assumptions to keep in every lesson

- They can read JavaScript, including `async`/`await`, array methods, and modules.
- They can read a TypeScript interface. Do not teach TypeScript from scratch. The TypeScript lesson is only the React-specific parts.
- Compare with C# when it helps: Razor partials, controller actions, `HttpClient`, `HttpContext.User`, xUnit + Moq, a DTO.
- Say when this app differs from a typical React job, in one or two sentences. Do not survey libraries the app does not use.

## How this app actually builds UI

Verified against `SDSD/6.Frontend/src/` (November 2026). Lessons must match this, not a generic React tutorial.

- Function components only. No class components.
- Props are a TypeScript interface on the function argument. Most components use `export default function`.
- Local UI state is `useState`. Forms are controlled inputs (`value` + `onChange`). There is no form library.
- Server data is usually `useEffect` + `useState` + `api.get` / `api.post` from `src/api/client.ts`. TanStack Query is used in one place: `NotificationMenu`.
- Routing is React Router 7, imported from `react-router` (not `react-router-dom`). The basename is `/app`, set in `src/main.tsx`. The route table is `src/App.tsx`. Paths still look like the old MVC paths (`WeighIn/Add`, `Team/Details/:id`).
- Shared session state is `AuthProvider` / `useAuth` in `src/auth/AuthContext.tsx`. There is no Redux, Zustand, or other store.
- Styling is Bootstrap class names (`className`), plus the host stylesheet. There are no CSS modules.
- `data-testid` is the hook for tests and for Playwright.
- Prettier in this folder: no semicolons, single quotes. Snippets copied into the docs should follow that.

Provider order in `src/main.tsx`, outside in:

1. `StrictMode`
2. `QueryClientProvider`
3. `BrowserRouter` (`basename="/app"`)
4. `AuthProvider`
5. `App`

`index.html` has `<div id="root">`. `createRoot(...).render(...)` mounts the tree there.

## Reading order

Write and nav the guides in this order. Each one assumes the previous.

| # | Plan | Future page | Idea | Main files |
| --- | --- | --- | --- | --- |
| 1 | `01-start-here.md` | `guides/learn-react.md` | What React is here, and the path | `main.tsx`, `index.html` |
| 2 | `02-components-and-jsx.md` | `guides/components-and-jsx.md` | A component is a function that returns UI | `ChallengeStatusBadge.tsx` |
| 3 | `03-props.md` | `guides/props.md` | Arguments in, including callbacks | `Alerts.tsx`, `ConfirmModal.tsx` |
| 4 | `04-composition.md` | `guides/composition.md` | Parents render children | `Layout.tsx`, `ShellChrome.tsx`, `DashboardPage.tsx` |
| 5 | `05-state-and-events.md` | `guides/state-and-events.md` | `useState` and controlled inputs | `SetGoalWeightPage.tsx` |
| 6 | `06-lists-and-conditions.md` | `guides/lists-and-conditions.md` | `&&`, ternary, `map`, `key`, early return | `Alerts.tsx`, `PinLoginPage.tsx`, `RulesPage.tsx` |
| 7 | `07-effects-and-loading.md` | `guides/effects-and-loading.md` | Load after the first paint | `RulesPage.tsx`, `PinLoginPage.tsx` |
| 8 | `08-routing.md` | `guides/routing.md` | URL picks the page | `App.tsx`, `TeamDetailsPage.tsx` |
| 9 | `09-context.md` | `guides/context.md` | One value many components can read | `AuthContext.tsx`, `Layout.tsx` |
| 10 | `10-calling-the-api.md` | `guides/calling-the-api.md` | `api.get` / `api.post` and `ApiError` | `client.ts`, `SetGoalWeightPage.tsx` |
| 11 | `11-tanstack-query.md` | `guides/server-state-and-tanstack-query.md` | The one polled query | `NotificationMenu.tsx`, `main.tsx` |
| 12 | `12-typescript-in-react.md` | `guides/typescript-in-react.md` | Props, events, unions, `api.get<T>` | `AuthContext.tsx`, `ConfirmModal.tsx` |
| 13 | `13-read-a-page.md` | `guides/read-a-page.md` | Walk one page top to bottom | `PinLoginPage.tsx`, then `DashboardPage.tsx` |
| 14 | `14-test-a-component.md` | `guides/test-a-component.md` | Vitest + Testing Library | `PinLoginPage.test.tsx` |

Reference, not part of the sequence:

| Plan | Future page |
| --- | --- |
| `15-glossary.md` | `reference/react-glossary.md` |
| `16-hooks-in-this-app.md` | `reference/react-hooks-in-this-app.md` |

## Nav to add when the pages are written

Keep the existing guide and reference entries. Nest the lessons so "Run the React frontend" stays the operational guide.

```yaml
- SDSD:
    - Overview: sdsd/index.md
    - Guides:
        - Run the React frontend: sdsd/guides/run-the-react-frontend.md
        - Learn React:
            - Start here: sdsd/guides/learn-react.md
            - Components and JSX: sdsd/guides/components-and-jsx.md
            - Props: sdsd/guides/props.md
            - Composition: sdsd/guides/composition.md
            - State and events: sdsd/guides/state-and-events.md
            - Lists and conditions: sdsd/guides/lists-and-conditions.md
            - Load data on mount: sdsd/guides/effects-and-loading.md
            - Routing: sdsd/guides/routing.md
            - Context: sdsd/guides/context.md
            - Call the JSON API: sdsd/guides/calling-the-api.md
            - Server state with TanStack Query: sdsd/guides/server-state-and-tanstack-query.md
            - TypeScript in components: sdsd/guides/typescript-in-react.md
            - Read a page: sdsd/guides/read-a-page.md
            - Test a component: sdsd/guides/test-a-component.md
    - Reference:
        - React frontend: sdsd/reference/react-frontend.md
        - React glossary: sdsd/reference/react-glossary.md
        - Hooks in this app: sdsd/reference/react-hooks-in-this-app.md
```

Also update `docs/sdsd/index.md`: add a "Learn React" button to `guides/learn-react.md`, and one sentence that those guides teach React with this app's components. The home hub (`docs/index.md`) already links SDSD. Adjust its one-line description only if it still reads as operations-only after the lessons exist. Do not add an API nav group.

## Writing rules for the later pass

- Open with the idea in plain language. Then one short "If you know C#" note (`!!! tip`). Then a short real snippet. Then the path to open in the app repo.
- Snippets are copied from the app and stay short. The docs site cannot use IDE line-citations. Prefer the important 15–30 lines over a whole file.
- Snippets must match the source at write time. Re-read the file; do not trust this plan if the code has moved.
- Cross-link the previous and next lesson at the bottom.
- Use a Mermaid diagram only where the plan names one (provider tree, dashboard composition). Use `===` tabs only on the start page, for the Razor vs React contrast.
- Name libraries this app does not use only to prevent a wrong assumption (no Redux, no form library, no CSS modules, no `react-router-dom` import). Do not teach them.
- No secrets, no invented endpoints, no invented hooks.
- After the pages, `mkdocs.yml`, and the SDSD landing are updated: `mkdocs build --strict`, then a PR against `main` of the Documentation repo. That is a later task. These plan files stay out of `docs/` so the site does not publish them.

## What a later job will still need

Say this once on the start page, and again only where a lesson is the local special case:

- Function components, JSX, props, state, effects, lists, and context are the transferable core.
- This app keeps server data in component state and refetches by calling `load` again. Many jobs use TanStack Query (or a similar cache) for almost every request. Lesson 11 is the bridge.
- This app uses controlled `useState` fields. Many jobs add a form library on top of the same idea.
- Routes here copy old MVC paths and some actions call `window.location.assign` for a full page load. The router ideas still transfer; the path strings are local.
