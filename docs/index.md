# Acme App

**Acme App** is a placeholder product name for this documentation template. Rename it everywhere when you adopt the site for a real project.

Acme App helps teams ship features faster with a clear control plane for configuration, automation, and APIs.

[Get started](guides/getting-started.md){ .md-button .md-button--primary }
[API overview](api/index.md){ .md-button }

---

## What you can do

| Area | Use it for |
| --- | --- |
| **Guides** | Install, configure, and operate Acme App day to day |
| **Reference** | Architecture, CLI, config keys, and shared terms |
| **API** | REST endpoints for users, projects, and integrations |

## Quick start

=== "pip"

    ```bash
    pip install acme-app
    acme init
    acme serve --port 8080
    ```

=== "Docker"

    ```bash
    docker run --rm -p 8080:8080 acme/app:latest
    ```

=== "npm"

    ```bash
    npx @acme/app init
    npm run start
    ```

!!! tip "Rename this product"
    Search the repo for `Acme App` / `acme` and replace with your product name, package names, and URLs before publishing.

## Next steps

1. [Install Acme App](guides/installation.md)
2. [Configure the service](guides/configuration.md)
3. [Explore the architecture](reference/architecture.md)
4. [Call the API](api/index.md)
