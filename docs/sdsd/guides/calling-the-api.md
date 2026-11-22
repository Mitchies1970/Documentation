# Call the JSON API

Pages do not call `fetch` on their own. They call `api.get` and `api.post` from `SDSD/6.Frontend/src/api/client.ts`. Those functions return parsed JSON, or throw `ApiError`.

`api.get<T>(url)` is a GET. `api.post<T>(url, body)` JSON-encodes `body` and POSTs it. A `204` becomes `undefined`. Any other non-OK status throws.

!!! tip "If you know C#"
    `api.get<RulesResponse>('/api/team/rules')` is `HttpClient.GetFromJsonAsync<RulesResponse>(...)`, with the auth cookie added for you. The TypeScript interface next to the component stands in for the C# DTO. The names often match the MediatR response, written again on the client. They are not generated from the C# in this solution. If a property is wrong, the compiler will not catch a mismatch with the server. `ApiError` is the client-side stand-in for a non-success `IActionResult`. Components check `instanceof ApiError` and show `error.message`.

## What `apiFetch` adds

`api.get` and `api.post` are thin wrappers. `apiFetch` is the function that calls `fetch`.

```tsx
export const api = {
  get: <T>(input: string) => apiFetch<T>(input),
  post: <T>(input: string, body?: unknown) =>
    apiFetch<T>(input, {
      method: 'POST',
      headers: body === undefined ? undefined : { 'Content-Type': 'application/json' },
      body: body === undefined ? undefined : JSON.stringify(body)
    })
}
```

`Content-Type: application/json` is set only when `body` is not `undefined`. A post with no body sends neither the header nor a JSON payload.

Before `fetch`, `apiFetch` sets `Accept` to `application/json` and `credentials: 'same-origin'`. That sends the auth cookie. There is no bearer token in the React app.

Unsafe methods (`POST`, `PUT`, `PATCH`, `DELETE`) copy the `XSRF-TOKEN` cookie into the `RequestVerificationToken` header. The host rejects cookie-authenticated writes that do not echo that token. Cookie flags, and the table of controller prefixes, are in [Session and API calls](../reference/react-frontend.md#session-and-api-calls) and [JSON API the UI calls](../reference/react-frontend.md#json-api-the-ui-calls).

A `204` returns `undefined`. Any other non-OK status reads the problem body and throws `ApiError`. The message prefers field errors: each message is collected, duplicates dropped, and the rest joined with spaces. If there are no field errors, the message is `detail`, then `errorMessage`, then `title`. A `401` also dispatches `auth:unauthorized`, which the [context](context.md) lesson already handled, and then still throws.

```tsx
export class ApiError extends Error {
  readonly fieldErrors: Record<string, string[]>

  constructor(
    public readonly status: number,
    detail?: string,
    fieldErrors?: Record<string, string[]>
  ) {
    super(detail ?? `API request failed with status ${status}`)
    this.name = 'ApiError'
    this.fieldErrors = fieldErrors ?? {}
  }
}
```

`error.message` is that `detail`, or the fallback string with the status code.

## The generic is a claim about the JSON

`RulesPage` types the GET:

```tsx
api.get<RulesResponse>('/api/team/rules')
```

`RulesResponse` is the interface at the top of that file. The page reads `success`, `errorMessage`, `teamName`, `competitionStartDate`, `competitionEndDate`, and `template`. The generic is the caller's claim about the response shape. TypeScript trusts it. The server is not checked.

The goal-weight form posts a plain object. The [state](state-and-events.md) lesson walked the rest of the handler:

```tsx
await api.post('/api/weigh-ins/goal-weight', { goalWeightKg: Number(goalWeightKg) })
```

The failure path there is:

```tsx
setError(caught instanceof ApiError ? caught.message : 'Failed to set goal weight.')
```

PIN login is the same check, with a cleared PIN. The form around it is the goal-weight pattern with more fields. The catch is the part that matters here:

```tsx
} catch (caught) {
  setPin('')
  setError(caught instanceof ApiError ? caught.message : 'Invalid login attempt.')
  setPending(false)
}
```

A `401` whose body is `{ detail: 'Invalid login attempt.' }` becomes `caught.message` because `ApiError` uses `detail` when there are no field errors. The testing lesson asserts that text.

The notification menu calls the same `api.get`. The TanStack Query lesson is that one screen. It does not use a different client.

## Where the routes live

JSON actions live in `SDSD/SDSD.WebApp/Controllers/Api/`. They share `ApiBaseController`: current user, the admin check, the antiforgery cookie, and MediatR. Business rules stay in the MediatR handlers. The React page does not reimplement them.

A new screen needs a JSON action on the host, then a call from the page. Adding that controller is outside this lesson. The prefix table is the reference link above.

## Continue

Previous: [Context](context.md).

Next: Server state with TanStack Query.
