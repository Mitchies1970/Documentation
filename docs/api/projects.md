# Projects API

Projects group users and configuration inside Acme App.

## List projects

```http
GET /api/v1/projects HTTP/1.1
Authorization: Bearer <token>
```

**Response `200`**

```json
{
  "data": [
    {
      "id": "prj_01ABC",
      "name": "demo",
      "slug": "demo",
      "created_at": "2026-02-01T12:00:00Z"
    }
  ],
  "next_cursor": null
}
```

## Get a project

```http
GET /api/v1/projects/{project_id} HTTP/1.1
Authorization: Bearer <token>
```

## Create a project

```http
POST /api/v1/projects HTTP/1.1
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "Analytics",
  "slug": "analytics"
}
```

**Response `201`**

```json
{
  "id": "prj_01NEW",
  "name": "Analytics",
  "slug": "analytics",
  "created_at": "2026-11-21T11:00:00Z"
}
```

!!! warning "Slug uniqueness"
    `slug` must be unique per instance. Conflicting slugs return `409` with `code: conflict`.

## Update a project

```http
PATCH /api/v1/projects/{project_id} HTTP/1.1
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "Analytics Platform"
}
```

## Delete a project

```http
DELETE /api/v1/projects/{project_id} HTTP/1.1
Authorization: Bearer <token>
```

**Response `204`** — empty body on success.

!!! danger "Destructive"
    Deleting a project removes associated memberships and settings. Prefer soft-disable workflows in production if you need recovery.

## Annotated request example

```bash
curl -s -X POST http://127.0.0.1:8080/api/v1/projects \  # (1)!
  -H "Authorization: Bearer $ACME_AUTH__TOKEN" \           # (2)!
  -H "Content-Type: application/json" \
  -d '{"name":"Analytics","slug":"analytics"}'             # (3)!
```

1. Use HTTPS and your real base URL outside local development.
2. Admin or member tokens with project-create permission are required.
3. `slug` should be URL-safe (`[a-z0-9-]+`).
