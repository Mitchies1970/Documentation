# Test a component

- **Nav title:** Test a component
- **Page:** `docs/sdsd/guides/test-a-component.md`
- **Reader can:** Read `PinLoginPage.test.tsx` and describe what it renders, what it mocks, and what it asserts.

## Explain simply

Vitest runs the tests. Testing Library renders a component into a fake DOM (`jsdom`) and lets the test look at the screen the way a user would: by role, by text, or by `data-testid`.

The test does not start the ASP.NET host. `fetch` is replaced with `vi.spyOn(globalThis, 'fetch')`. Each URL the page calls gets a hand-built `Response`.

`render(<PinLoginPage />)` mounts the component. The auth provider and the router are not wrapped here, because this page does not call `useAuth` or `useNavigate`. Say that. A component that calls `useAuth` would need `AuthProvider` in the test, or the hook will throw. Do not invent a wrapper the file does not have. If you mention the need, mark it as what a different test must do, and only if you have seen such a test. A quick search at write time: if no test wraps `AuthProvider`, say "this suite has not had to yet" rather than showing a fictional wrapper.

`await waitFor(...)` and `findByTestId` wait for the effect's fetch to land and a re-render to happen. That is the effects lesson, seen from the test.

Queries used in this file:

- `getByRole('option', { name: 'Ido' })` — the accessible name of the `<option>`.
- `getByTestId('pin-user-select' | 'pin-input' | 'pin-login-form')` — the ids the page author put in the markup.
- `findByTestId('pin-login-error')` — async; waits until the error exists.

`fireEvent.change` and `fireEvent.submit` dispatch the events the handlers listen for. `change` with `{ target: { value } }` is what the controlled `onChange` reads (`event.target.value`).

`afterEach` calls `vi.restoreAllMocks()` so one test's `fetch` does not leak.

## C# bridge

Vitest `describe` / `it` is an xUnit class and `[Fact]`. `vi.spyOn` is Moq, aimed at `globalThis.fetch` because the client calls `fetch` directly. There is no in-memory `SdsdDbContext` here. The JSON you return from the mock is the whole backend.

Asserting `screen.getByRole(...)` is asserting the rendered HTML, not a return value of a handler. Closer to an MVC test that inspects the view, except the component function actually runs.

`data-testid` is also the Playwright contract. A unit test and an end-to-end test can share the same id. Do not explain the Playwright harness.

## Examples

`SDSD/6.Frontend/src/pages/PinLoginPage.test.tsx` — both tests, they are short.

1. **Happy path.** Mock: `/api/auth/pin-users` returns one user; any other call must be `POST` with body `{"userId":1,"pin":"258147"}` and returns `{ pinChangeRequired: false, nextUrl: null }`. Stub `location.assign`. Render, wait for the option, change the select and the PIN, submit, wait until `assign` was called with `'/app/'`.
2. **Rejected PIN.** The second call returns `401` and `{ detail: 'Invalid login attempt.' }`. The page shows that detail because `ApiError` reads `detail`. Assert the alert text.

How to run, one line, matching the existing run guide: from `SDSD/6.Frontend`, `npm run test -- --run`. Link the run guide for the rest of the npm scripts. Do not copy the CI list.

`src/test/setup.ts` is one line: `import '@testing-library/jest-dom/vitest'`. That import is what adds `toBeInTheDocument` and `toHaveTextContent`. Do not describe other matchers.

## Page outline

1. What is running (Vitest, jsdom, Testing Library) and what is not (the host, the database).
2. The happy-path test, in call order.
3. The failure test, tied back to `ApiError` and `detail`.
4. Why `waitFor` exists (the effect).
5. `data-testid` as the shared id.
6. The command to run the file once.

## Leave out

- Coverage goals, Playwright, and snapshot tests.
- Rewriting the page to be more testable.

## Links

- Previous: Read a page.
- Run guide: npm test script.
- Glossary: no new terms except `render` and `data-testid` if they are not already defined.
