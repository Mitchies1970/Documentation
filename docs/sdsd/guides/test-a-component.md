# Test a component

Vitest runs the tests. Testing Library renders a component into a fake DOM (`jsdom`, set in `SDSD/6.Frontend/vite.config.ts`) and lets the test look at the screen the way a user would: by role, by text, or by `data-testid`.

The test does not start the ASP.NET host or the database. `fetch` is replaced with `vi.spyOn(globalThis, 'fetch')`. Each URL the page calls gets a hand-built `Response`. The JSON you return from the mock is the whole backend.

!!! tip "If you know C#"
    Vitest `describe` / `it` is an xUnit class and `[Fact]`. `vi.spyOn` is Moq, aimed at `globalThis.fetch` because the client calls `fetch` directly. There is no in-memory `SdsdDbContext` here. Asserting `screen.getByRole(...)` is asserting the rendered HTML, not a return value of a handler. The component function actually runs.

`render(<PinLoginPage />)` mounts the component. This file does not wrap `AuthProvider` or a router, because `PinLoginPage` does not call `useAuth` or `useNavigate`. `AuthContext.test.tsx` passes `AuthProvider` as the `wrapper` for `renderHook` when it tests the hook itself. No page test in `src/` renders a screen inside `AuthProvider`. A page that called `useAuth` would throw without a provider. This PIN file has not had to do that.

`src/test/setup.ts` is one line:

```ts
import '@testing-library/jest-dom/vitest'
```

That import adds `toBeInTheDocument` and `toHaveTextContent`.

## The happy path

`SDSD/6.Frontend/src/pages/PinLoginPage.test.tsx` builds a `Response` with this helper, and `afterEach` calls `vi.restoreAllMocks()` so one test's `fetch` does not leak.

```tsx
function jsonResponse(status: number, body: unknown): Response {
  return new Response(JSON.stringify(body), {
    status,
    headers: { 'Content-Type': 'application/json' }
  })
}

afterEach(() => {
  vi.restoreAllMocks()
})
```

The first test, in call order:

1. Mock `fetch`. `/api/auth/pin-users` returns one user, `{ userId: 1, displayName: 'Ido', pinSet: true }`. Any other call must be `POST` with body `{"userId":1,"pin":"258147"}` and returns `{ authStatus: 'authenticated', pinChangeRequired: false, nextUrl: null }`.
2. Stub `location.assign`.
3. `render(<PinLoginPage />)`.
4. `waitFor` until the option named `Ido` is in the document. That wait is the [effect](effects-and-loading.md): the fetch has to land and React has to render again.
5. `fireEvent.change` on `pin-user-select` and `pin-input`. `{ target: { value } }` is what the controlled `onChange` reads (`event.target.value`).
6. `fireEvent.submit` on `pin-login-form`.
7. `waitFor` until `assign` was called with `'/app/'`.

```tsx
it('loads the user list and posts the selected PIN', async () => {
  const fetchMock = vi.spyOn(globalThis, 'fetch').mockImplementation(async (input, init) => {
    const url = String(input)
    if (url.includes('/api/auth/pin-users')) {
      return jsonResponse(200, [{ userId: 1, displayName: 'Ido', pinSet: true }])
    }
    expect(init?.method).toBe('POST')
    expect(init?.body).toBe(JSON.stringify({ userId: 1, pin: '258147' }))
    return jsonResponse(200, { authStatus: 'authenticated', pinChangeRequired: false, nextUrl: null })
  })
  const assign = vi.fn()
  vi.stubGlobal('location', { ...window.location, assign, origin: 'https://localhost:7001' })

  render(<PinLoginPage />)

  await waitFor(() => expect(screen.getByRole('option', { name: 'Ido' })).toBeInTheDocument())
  fireEvent.change(screen.getByTestId('pin-user-select'), { target: { value: '1' } })
  fireEvent.change(screen.getByTestId('pin-input'), { target: { value: '258147' } })
  fireEvent.submit(screen.getByTestId('pin-login-form'))

  await waitFor(() => expect(assign).toHaveBeenCalledWith('/app/'))
  expect(fetchMock).toHaveBeenCalled()
})
```

`getByRole('option', { name: 'Ido' })` uses the accessible name of the `<option>`. `getByTestId` uses the ids the page author put in the markup: `pin-user-select`, `pin-input`, and `pin-login-form`.

## The rejected PIN

The second call returns `401` and `{ detail: 'Invalid login attempt.' }`. The page shows that detail because `ApiError` reads `detail`. The [API](calling-the-api.md) lesson is that message. `findByTestId` is the async query: it waits until `pin-login-error` exists.

```tsx
it('shows the problem detail when the PIN is rejected', async () => {
  vi.spyOn(globalThis, 'fetch').mockImplementation(async (input) => {
    if (String(input).includes('/api/auth/pin-users')) {
      return jsonResponse(200, [{ userId: 1, displayName: 'Ido', pinSet: true }])
    }
    return jsonResponse(401, { detail: 'Invalid login attempt.' })
  })

  render(<PinLoginPage />)
  await waitFor(() => expect(screen.getByRole('option', { name: 'Ido' })).toBeInTheDocument())
  fireEvent.change(screen.getByTestId('pin-user-select'), { target: { value: '1' } })
  fireEvent.change(screen.getByTestId('pin-input'), { target: { value: '000000' } })
  fireEvent.submit(screen.getByTestId('pin-login-form'))

  expect(await screen.findByTestId('pin-login-error')).toHaveTextContent('Invalid login attempt.')
})
```

## `data-testid`

`data-testid` is an attribute this project puts on elements so tests can find them. It is not a React feature. A unit test and a Playwright test can share the same id. The [read a page](read-a-page.md) checklist ends on searching for those ids.

## Run it once

From `SDSD/6.Frontend`:

```bash
npm run test -- --run
```

The other npm scripts are in [Run the React frontend](run-the-react-frontend.md#npm-scripts).

## Continue

Previous: [Read a page](read-a-page.md).
