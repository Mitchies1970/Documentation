# Users API

Manage user accounts for an Acme App instance.

## List users

```http
GET /api/v1/users HTTP/1.1
Authorization: Bearer <token>
```

**Query parameters**

| Name | Type | Default | Description |
| --- | --- | --- | --- |
| `limit` | int | `50` | Max items (1–200) |
| `cursor` | string | — | Pagination cursor |

**Response `200`**

```json
{
  "data": [
    {
      "id": "usr_01HXYZ",
      "email": "ada@example.com",
      "name": "Ada Lovelace",
      "role": "admin",
      "created_at": "2026-01-15T10:00:00Z"
    }
  ],
  "next_cursor": null
}
```

## Get a user

```http
GET /api/v1/users/{user_id} HTTP/1.1
Authorization: Bearer <token>
```

**Response `200`**

```json
{
  "id": "usr_01HXYZ",
  "email": "ada@example.com",
  "name": "Ada Lovelace",
  "role": "admin",
  "created_at": "2026-01-15T10:00:00Z"
}
```

!!! failure "404 Not found"
    Unknown `user_id` values return `404` with `code: not_found`.

## Create a user

```http
POST /api/v1/users HTTP/1.1
Authorization: Bearer <token>
Content-Type: application/json

{
  "email": "grace@example.com",
  "name": "Grace Hopper",
  "role": "member"
}
```

**Response `201`**

```json
{
  "id": "usr_01HNEW",
  "email": "grace@example.com",
  "name": "Grace Hopper",
  "role": "member",
  "created_at": "2026-11-21T10:30:00Z"
}
```

## Roles

| Role | Capabilities |
| --- | --- |
| `admin` | Full access, including user management |
| `member` | Read/write within assigned projects |
| `viewer` | Read-only access |

## Example: create then list

=== "curl"

    ```bash
    curl -s -X POST http://127.0.0.1:8080/api/v1/users \
      -H "Authorization: Bearer $ACME_AUTH__TOKEN" \
      -H "Content-Type: application/json" \
      -d '{"email":"grace@example.com","name":"Grace Hopper","role":"member"}'

    curl -s http://127.0.0.1:8080/api/v1/users \
      -H "Authorization: Bearer $ACME_AUTH__TOKEN"
    ```

=== "acme CLI"

    ```bash
    acme users create --email grace@example.com --name "Grace Hopper"
    acme users list
    ```
