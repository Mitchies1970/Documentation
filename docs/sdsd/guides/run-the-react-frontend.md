# Run the React frontend

The React app is served at `/app/` on the ASP.NET Core host. In development the host proxies that prefix to Vite, so the browser stays on the host origin (`https://localhost:7001` with the https launch profile). Cookies, antiforgery, and passkey origin checks then match production.

Open the app at:

```text
https://localhost:7001/app/
```

Opening the Vite port directly is redirected to that host URL. Google sign-in and passkeys are registered for the host origin.

## Prerequisites

- .NET 11 SDK (the host and the Aspire AppHost)
- Node 22, matching `SDSD/6.Frontend/.nvmrc` (Node 20 or newer also satisfies `package.json`)
- Frontend dependencies installed once from the lockfile:

```bash
cd SDSD/6.Frontend
npm ci
```

The Aspire AppHost runs `npm ci` for the Vite resource itself. A manual `npm run dev` still needs the install above.

## Start with the AppHost

From the repository root, the AppHost starts the web host and the Vite dev server together:

```bash
dotnet run --project SDSD/4.Infra/SDSD.AppHost
```

In Visual Studio, start the `SDSD.AppHost` project. Aspire assigns Vite a port and passes that address to the web host as `Frontend__SpaDevServerUri`. The dashboard link labeled **App** opens `https://localhost:7001/app/`.

The Vite resource listens on `127.0.0.1` with Aspire proxying turned off, so the web host talks to Vite directly. Its dev script runs `node` on Vite's JavaScript entry (`node ./node_modules/vite/bin/vite.js`) so the process starts under Visual Studio's debug environment.

!!! warning
    With `Frontend:UseProxyToSpaDevServer` left at its default (`true`), `/app/` fails with a proxy error when Vite is not running. Start the AppHost, or start Vite yourself as below.

## Start the host and Vite separately

Use this when you want the web host without the AppHost.

1. Start Vite:

```bash
cd SDSD/6.Frontend
npm run dev
```

Vite binds `127.0.0.1:5173` (`strictPort: true`). That address is the default of `Frontend:SpaDevServerUri`.

2. Start the web host with its https launch profile (`https://localhost:7001`):

```bash
dotnet run --project SDSD/SDSD.WebApp --launch-profile https
```

3. Browse to `https://localhost:7001/app/`.

`VITE_WEBAPP_ORIGIN` defaults to `https://localhost:7001` in development. Set it when the host origin differs, including when Vite is started by a tool other than the AppHost:

```bash
# PowerShell
$env:VITE_WEBAPP_ORIGIN = "https://localhost:7001"
npm run dev
```

## npm scripts

Run these in `SDSD/6.Frontend/`:

| Script | What it does |
| --- | --- |
| `npm run dev` | Vite dev server on `127.0.0.1`, port `PORT` or `5173` |
| `npm run build` | `tsc -b`, then Vite build into `SDSD/SDSD.WebApp/wwwroot/app/` |
| `npm run lint` | ESLint |
| `npm run test` | Vitest in watch mode |
| `npm run test -- --run` | Vitest once (the CI command) |
| `npm run preview` | Vite preview of the production bundle |

`npm run build` empties `wwwroot/app` before writing. That folder is gitignored. Production and the Playwright harness serve those files. The harness sets `Frontend:UseProxyToSpaDevServer` to `false` so it never proxies to Vite.

A normal `dotnet build` of `SDSD/SDSD.slnx` does not run npm. The `SDSD.Frontend` project is a NoTargets shim so the folder is in the solution. Opt in to an MSBuild-driven install and build with:

```bash
dotnet build SDSD/6.Frontend/SDSD.Frontend.csproj -p:BuildReactFrontend=true
```

## Checks that CI runs

The app repository workflow, in `SDSD/6.Frontend/`:

1. `npm ci`
2. `npm run lint`
3. `npm run test -- --run`
4. `npm run build`
5. `npm audit --audit-level=high`

Node comes from `SDSD/6.Frontend/.nvmrc`.

## Frontend configuration

These keys live in the web host configuration (`Frontend` section). They have defaults, so the host starts when the section is absent. Copy the shape from `SDSD/SDSD.WebApp/appsettings.template.json`.

| Key | Default | Role |
| --- | --- | --- |
| `Frontend:SpaBasePath` | `/app` | URL prefix of the React app. Must match Vite `base` and the React Router basename. |
| `Frontend:UseProxyToSpaDevServer` | `true` | In Development, proxy `/app` (including the Vite HMR websocket) to Vite. Outside Development the built files are served instead. |
| `Frontend:SpaDevServerUri` | `http://127.0.0.1:5173` | Vite address for a manual `npm run dev`. The AppHost overwrites this with the Vite endpoint (`Frontend__SpaDevServerUri`). |

The host rewrites a loopback host of `localhost` to `127.0.0.1` before connecting, because Vite listens on IPv4 only.

See the [frontend reference](../reference/react-frontend.md) for the request path, routing, and the JSON API the UI calls.
