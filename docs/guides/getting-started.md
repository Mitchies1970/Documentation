# Getting started

This guide walks you through running **Acme App** locally for the first time.

## Prerequisites

- Python 3.11+ (or Docker)
- A terminal and a text editor
- Optional: an API token if you plan to call remote environments

!!! note "Placeholder product"
    **Acme App** is the sample product in this template. Rename it when you fork or adopt these docs.

## 1. Install

```bash
pip install acme-app
```

Or with Docker:

```bash
docker pull acme/app:latest
```

See [Installation](installation.md) for platform-specific options.

## 2. Initialize a project

```bash
acme init ./my-project
cd my-project
```

This creates a starter config and a local workspace:

```text
my-project/
├── acme.yaml      # primary configuration
├── .env.example   # secrets template
└── README.md
```

## 3. Start the service

```bash
acme serve --port 8080
```

Open [http://localhost:8080](http://localhost:8080). You should see the health page respond with `status: ok`.

!!! success "You're ready"
    Local Acme App is running. Next, [tune configuration](configuration.md) or [call the API](../api/index.md).

## 4. Make a sample API call

```bash
curl -s http://localhost:8080/api/v1/health | jq
```

Expected response:

```json
{
  "status": "ok",
  "version": "0.1.0"
}
```

## Where to go next

- [Configuration](configuration.md) — environment variables and `acme.yaml`
- [How to deploy](how-to-deploy.md) — ship to staging or production
- [Architecture](../reference/architecture.md) — how the pieces fit together
