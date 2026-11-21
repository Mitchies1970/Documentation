# Glossary

Shared terms used across **Acme App** documentation.

| Term | Definition |
| --- | --- |
| **Acme App** | Placeholder product name for this documentation template. Rename when adopting the site. |
| **Project** | A named workspace that groups users, settings, and API resources. |
| **Workspace** | Local directory created by `acme init`, usually containing `acme.yaml`. |
| **Token** | Bearer credential used to authenticate API and CLI calls. |
| **Environment** | Runtime mode: `development`, `staging`, or `production`. |
| **Control plane** | The API and admin surfaces that configure and operate the product. |
| **Worker** | Background process that consumes jobs from the queue. |
| **Health endpoint** | `GET /api/v1/health` — liveness/readiness signal for orchestrators. |
| **Base URL** | Root URL of an Acme App instance, e.g. `https://acme.example.com`. |

!!! tip "Keep this page short"
    Add only terms that appear in multiple guides. Prefer linking to deeper pages for long explanations.
