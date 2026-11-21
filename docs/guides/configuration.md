# Configuration

Acme App reads settings from `acme.yaml`, environment variables, and (optionally) a `.env` file. Precedence is:

1. Environment variables (highest)
2. `.env` / secrets provider
3. `acme.yaml` (lowest)

## Minimal `acme.yaml`

```yaml title="acme.yaml"
app:
  name: acme-app
  env: development
server:
  host: 0.0.0.0
  port: 8080
logging:
  level: info
```

## Common settings

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `app.name` | string | `acme-app` | Service display name |
| `app.env` | string | `development` | `development`, `staging`, or `production` |
| `server.host` | string | `127.0.0.1` | Bind address |
| `server.port` | int | `8080` | HTTP port |
| `logging.level` | string | `info` | `debug`, `info`, `warn`, `error` |
| `auth.token` | string | — | API bearer token (prefer env var) |

For the full key list, see [Config reference](../reference/config.md).

## Environment variables

Acme App maps nested keys to uppercase env vars with `__` as a separator:

```bash
export ACME_APP__ENV=production
export ACME_SERVER__PORT=8443
export ACME_AUTH__TOKEN="replace-me"
```

!!! danger "Do not commit secrets"
    Keep tokens and passwords out of git. Use `.env` locally and your platform secret store in production.

## Content tabs example: loading config

=== "YAML file"

    Point the CLI at a file:

    ```bash
    acme serve --config ./acme.yaml
    ```

=== "Environment only"

    Skip the file and set variables:

    ```bash
    export ACME_SERVER__HOST=0.0.0.0
    export ACME_SERVER__PORT=8080
    acme serve
    ```

=== "Mixed"

    Use a base file and override one value:

    ```bash
    ACME_LOGGING__LEVEL=debug acme serve --config ./acme.yaml
    ```

## Validate configuration

```bash
acme config validate
```

Invalid keys or types exit non-zero and print a pointer to the offending path.
