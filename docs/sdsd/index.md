# SDSD

SlimDownShowDown (SDSD) is an ASP.NET Core weight-loss competition app. The interactive UI is a React single-page app served by that same web host under `/app/`.

These pages describe that React frontend as it is wired today: how to run it, and how the browser, Vite, and the ASP.NET Core host fit together.

[Run the React frontend](guides/run-the-react-frontend.md){ .md-button .md-button--primary }
[Frontend reference](reference/react-frontend.md){ .md-button }

## Where the code lives

| Piece | Path in the app repo |
| --- | --- |
| React app | `SDSD/6.Frontend/` |
| ASP.NET Core host | `SDSD/SDSD.WebApp/` |
| Aspire AppHost (starts the host and Vite together) | `SDSD/4.Infra/SDSD.AppHost/` |
| JSON API used by the React app | `SDSD/SDSD.WebApp/Controllers/Api/` |

The host targets **.NET 11**. The React app pins **Node 22** in `SDSD/6.Frontend/.nvmrc`. `package.json` allows Node 20 or newer.

Google and Facebook sign-in, plus a short list of account callbacks, stay on the ASP.NET Core host. The React app renders the signed-in product screens. Details are in the [frontend reference](reference/react-frontend.md).
