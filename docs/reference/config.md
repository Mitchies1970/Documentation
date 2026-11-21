# Config reference

Complete list of keys recognized in `acme.yaml` and their environment-variable equivalents.

## `app`

| Key | Env var | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `app.name` | `ACME_APP__NAME` | string | `acme-app` | Service name shown in logs and UI |
| `app.env` | `ACME_APP__ENV` | string | `development` | Runtime environment |
| `app.version` | `ACME_APP__VERSION` | string | build | Override reported version |

## `server`

| Key | Env var | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `server.host` | `ACME_SERVER__HOST` | string | `127.0.0.1` | Bind address |
| `server.port` | `ACME_SERVER__PORT` | int | `8080` | HTTP listen port |
| `server.workers` | `ACME_SERVER__WORKERS` | int | `1` | Process workers (production) |
| `server.cors_origins` | `ACME_SERVER__CORS_ORIGINS` | list | `[]` | Allowed browser origins |

## `auth`

| Key | Env var | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `auth.token` | `ACME_AUTH__TOKEN` | string | — | Default API bearer token |
| `auth.issuer` | `ACME_AUTH__ISSUER` | string | — | JWT issuer URL when using OIDC |
| `auth.audience` | `ACME_AUTH__AUDIENCE` | string | — | Expected JWT audience |

## `logging`

| Key | Env var | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `logging.level` | `ACME_LOGGING__LEVEL` | string | `info` | Log level |
| `logging.format` | `ACME_LOGGING__FORMAT` | string | `text` | `text` or `json` |

## `store`

| Key | Env var | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `store.url` | `ACME_STORE__URL` | string | `sqlite:///./acme.db` | Database URL |
| `store.pool_size` | `ACME_STORE__POOL_SIZE` | int | `5` | Connection pool size |

## Example production snippet

```yaml title="acme.yaml"
app:
  name: acme-app
  env: production
server:
  host: 0.0.0.0
  port: 8080
  workers: 4
  cors_origins:
    - https://app.example.com
logging:
  level: info
  format: json
store:
  url: postgres://acme:${DB_PASSWORD}@db:5432/acme
```

!!! warning "Secrets"
    Prefer injecting `ACME_AUTH__TOKEN` and database credentials from a secret manager rather than writing them into this file.
