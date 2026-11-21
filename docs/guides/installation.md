# Installation

Install **Acme App** on your workstation or in a container. Choose one path below.

## Requirements

| Component | Minimum |
| --- | --- |
| Python | 3.11+ |
| Disk | ~100 MB for the CLI and runtime |
| Memory | 512 MB recommended for local serve |
| OS | Linux, macOS, or Windows |

## Install with pip

```bash
python -m pip install --upgrade pip
pip install acme-app
acme --version
```

!!! warning "Virtual environments"
    Prefer a virtual environment so Acme App does not conflict with system packages:

    ```bash
    python -m venv .venv
    source .venv/bin/activate   # Windows: .venv\Scripts\activate
    pip install acme-app
    ```

## Install with Docker

```bash
docker pull acme/app:latest
docker run --rm -p 8080:8080 \
  -v "$PWD/acme.yaml:/app/acme.yaml:ro" \
  acme/app:latest
```

## Verify the install

```bash
acme doctor
```

A healthy install prints checks similar to:

```text
✓ python runtime
✓ config schema
✓ network bindings available
```

If a check fails, see [Configuration](configuration.md) or run `acme doctor --verbose`.

## Upgrade

=== "pip"

    ```bash
    pip install --upgrade acme-app
    ```

=== "Docker"

    ```bash
    docker pull acme/app:latest
    ```

## Uninstall

```bash
pip uninstall acme-app
```

Docker images can be removed with `docker rmi acme/app:latest`.
