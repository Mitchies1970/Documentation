# CLI reference

The `acme` CLI manages local projects and talks to a running Acme App instance.

## Global options

| Option | Description |
| --- | --- |
| `--config PATH` | Path to `acme.yaml` (default: `./acme.yaml`) |
| `--base-url URL` | API base URL (default: `http://127.0.0.1:8080`) |
| `--token TOKEN` | Bearer token; overrides `ACME_AUTH__TOKEN` |
| `-v`, `--verbose` | Increase log verbosity |
| `-h`, `--help` | Show help |

## Commands

### `acme init`

Create a new project workspace.

```bash
acme init [DIRECTORY]
```

| Argument / option | Description |
| --- | --- |
| `DIRECTORY` | Target folder (default: `.`) |
| `--force` | Overwrite existing starter files |

### `acme serve`

Run the HTTP API locally.

```bash
acme serve [--host HOST] [--port PORT]
```

### `acme doctor`

Run environment and config checks.

```bash
acme doctor [--verbose]
```

### `acme config validate`

Validate the active configuration against the schema.

```bash
acme config validate [--config PATH]
```

### `acme projects list`

List projects from the API.

```bash
acme projects list [--base-url URL]
```

### `acme users create`

Create a user (admin token required).

```bash
acme users create --email USER@example.com --name "Display Name"
```

## Exit codes

| Code | Meaning |
| --- | --- |
| `0` | Success |
| `1` | General error |
| `2` | Invalid configuration |
| `3` | Authentication or authorization failure |
| `4` | Network / API unreachable |

## Annotated example

```bash
# (1) Point at staging
# (2) Use a short-lived token from your secret store
acme projects list \
  --base-url https://staging.acme.example.com \  # (1)!
  --token "$STAGING_TOKEN"                       # (2)!
```

1. Prefer an explicit base URL in scripts so local defaults never leak into staging.
2. Pass tokens via the environment rather than hard-coding them in shell history when possible.
