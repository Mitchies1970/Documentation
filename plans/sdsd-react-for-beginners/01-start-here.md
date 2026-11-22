# Start here

- **Nav title:** Start here
- **Page:** `docs/sdsd/guides/learn-react.md`
- **Reader can:** Say what a React UI is in this app, and know which lesson to open next.

## Explain simply

React is a library that runs in the browser. You write functions that describe the screen. When data changes, React calls those functions again and updates the DOM.

In SDSD the ASP.NET Core host still owns sign-in cookies, `/api`, and the HTML shell. React owns the screens under `/app/`. The host page has an empty `<div id="root">`. `src/main.tsx` finds that node and renders `<App />` inside a few wrappers.

Do not explain the Vite proxy, HMR, or SPA fallback here. Link to [Run the React frontend](../run-the-react-frontend.md) and [React frontend](../../reference/react-frontend.md).

## C# bridge

Use Material tabs (`=== "Razor"` / `=== "React"`):

- Razor: the server builds HTML for one request, then the response is done. A partial is included while that HTML is being built.
- React: the server sends the shell once. The browser keeps a tree of functions and calls them again when state changes. A component is the partial.

`createRoot(...).render(...)` is the one-time mount. It is not a request pipeline.

## Examples

- `SDSD/6.Frontend/index.html` — `#root` and the module script. Also note the host CSS links (`/css/site.css`, Bootstrap). One sentence: styling is still Bootstrap classes, not a separate design system.
- `SDSD/6.Frontend/src/main.tsx` — the wrapper order. A small Mermaid flowchart is enough:

```text
StrictMode → QueryClientProvider → BrowserRouter → AuthProvider → App
```

Say what each wrapper is for in one line, and point at the later lesson (Query lesson, routing lesson, context lesson). `SPA_BASENAME = '/app'` matches the host prefix. If `devServerLandingUrl` returns a URL, the page redirects to the host and never mounts. Mention that in one sentence so a direct Vite URL is not a mystery; details stay in the run guide.

## Page outline

1. Who this path is for (JS, C#, some TypeScript; new to React).
2. The Razor vs React tabs.
3. Where the code lives (`6.Frontend/src/`) and the mount snippet.
4. What this path will not teach (hosting, the API catalog) with links.
5. What transfers to a later job, and the three local habits that differ (server data in `useState`, no form library, MVC-shaped routes). Two short paragraphs, taken from the overview. Do not expand into a library tour.
6. Reading-order table: lesson title, one-line idea, file to open. Link each lesson.
7. Optional first click: open `ChallengeStatusBadge.tsx` before the next lesson. It is the smallest component.

## Leave out

- npm scripts and configuration keys.
- The full route table.
- How antiforgery works.

## Links

- Next: Components and JSX.
- Also: Run the React frontend, React frontend reference.
