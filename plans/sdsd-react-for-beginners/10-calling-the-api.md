# Call the JSON API

- **Nav title:** Call the JSON API
- **Page:** `docs/sdsd/guides/calling-the-api.md`
- **Reader can:** Call `api.get` and `api.post` from a component, type the JSON, and show an `ApiError` message.

## Explain simply

Pages do not call `fetch` on their own. They call `api.get` and `api.post` from `src/api/client.ts`. Those functions return parsed JSON, or throw `ApiError`.

`api.get<T>(url)` is a GET. `api.post<T>(url, body)` JSON-encodes `body` and POSTs it. A `204` becomes `undefined`. Any other non-OK status throws. The message prefers the problem body: field errors joined together, then `detail`, then `title`.

Unsafe methods (`POST`, `PUT`, `PATCH`, `DELETE`) copy the `XSRF-TOKEN` cookie into the `RequestVerificationToken` header. Say what that is for in two sentences (the host rejects cookie-authenticated writes without the token). Link the reference for the cookie flags and for the controller prefix table. Do not rebuild that page.

`credentials: 'same-origin'` sends the auth cookie. There is no bearer token in the React app.

A `401` also dispatches `auth:unauthorized`, which the context lesson already handled. One sentence of reminder.

## C# bridge

`api.get<RulesResponse>('/api/team/rules')` is `HttpClient.GetFromJsonAsync<RulesResponse>(...)`, with the cookie added for you.

The TypeScript interface next to the component (`RulesResponse`, the goal-weight body `{ goalWeightKg: number }`) stands in for the C# DTO. The names are often the same shape as the MediatR response, written again on the client. They are not generated from the C# in this solution. If a property is wrong, the compiler will not catch a mismatch with the server. Be explicit about that.

`ApiError` is the client-side stand-in for a non-success `IActionResult`. Components check `instanceof ApiError` and show `error.message`.

## Examples

- `src/api/client.ts` — `ApiError`, the `api` object, and a short description of `apiFetch` (Accept header, unsafe-method token, 204, problem detail, 401 event). Do not paste `readProblemDetail` in full. Describe the field-error join in a sentence.
- `SetGoalWeightPage` — the `api.post` line and the `catch`. The reader already knows the form.
- `RulesPage` — `api.get<RulesResponse>` and the interface fields that the page actually reads (`success`, `errorMessage`, `teamName`, dates, `template`). Enough to show "the generic is the response shape."
- One failure UI: the goal-weight `setError(...)` line, or the PIN login `catch` that clears the PIN and sets `error`. The PIN page is the richer example (`caught instanceof ApiError`). Use that catch block, and say the form around it was the state lesson's sibling.

Show a successful post body as a plain object, and note `Content-Type: application/json` is set only when `body` is not `undefined`.

## Page outline

1. One wrapper, two methods.
2. What `apiFetch` adds before `fetch` (cookie, Accept, XSRF on writes).
3. Typing the response with `<T>`, and the caveat that it is not checked against C#.
4. `ApiError` and how a page displays it.
5. Where the routes live: `SDSD.WebApp/Controllers/Api/`, prefix table in the reference. Name `ApiBaseController` as the shared base (current user, admin check, antiforgery cookie, MediatR) in one sentence. Business rules stay in the MediatR handlers. The React page does not reimplement them.

## Leave out

- A catalog of every endpoint.
- TanStack Query's `queryFn`, except a forward pointer: the notification menu calls the same `api.get`.
- How to add a new controller. Out of scope for a React lesson. One sentence: a new screen needs a JSON action on the host, then a call from the page.

## Links

- Previous: Context.
- Next: Server state with TanStack Query.
- Reference: React frontend, "JSON API the UI calls" and "Session and API calls".
