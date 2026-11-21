# API overview

The Acme App HTTP API is a JSON REST surface under `/api/v1`.

## Base URL

| Environment | Base URL |
| --- | --- |
| Local | `http://127.0.0.1:8080` |
| Staging | `https://staging.acme.example.com` |
| Production | `https://acme.example.com` |

## Authentication

Send a bearer token on every request except health:

```http
Authorization: Bearer <token>
```

!!! failure "401 Unauthorized"
    Missing or invalid tokens return `401` with a JSON error body.

## Common headers

| Header | Required | Description |
| --- | --- | --- |
| `Authorization` | Yes* | Bearer token (*not required for `/health`) |
| `Content-Type` | For bodies | `application/json` |
| `Accept` | No | Prefer `application/json` |

## Versioning

Breaking changes ship under a new path prefix (`/api/v2`, …). Additive fields may appear on `v1` without a bump.

## Error shape

```json
{
  "error": {
    "code": "validation_error",
    "message": "name is required",
    "details": {
      "field": "name"
    }
  }
}
```

## Endpoints in this template

| Resource | Path | Docs |
| --- | --- | --- |
| Health | `GET /api/v1/health` | below |
| Users | `/api/v1/users` | [Users](users.md) |
| Projects | `/api/v1/projects` | [Projects](projects.md) |

### Health

```http
GET /api/v1/health HTTP/1.1
Host: localhost:8080
```

```json
{
  "status": "ok",
  "version": "0.1.0"
}
```
