---
name: api-design
description: HTTP / REST / GraphQL API design — resource modeling, status codes, error format, pagination, versioning. Framework-agnostic.
estimated-tokens: 1700
used-by: [architect, engineer]
---

# API Design

## Resource modeling (REST)

- Nouns, not verbs: `/orders`, not `/getOrders`.
- Pluralize collection names.
- Hierarchy reflects ownership: `/orders/{orderId}/items/{itemId}`.
- Avoid deep nesting (≤ 2 levels) — flatten with query parameters when relations get awkward.
- Verbs only for genuine actions that don't fit CRUD: `POST /invoices/{id}/void`.

## Methods and status codes

| Method | Use | Idempotent | Common success |
|---|---|---|---|
| GET | read | yes | 200 |
| POST | create or non-idempotent action | no | 201 (with `Location`) or 200 |
| PUT | replace | yes | 200 / 204 |
| PATCH | partial update | no | 200 / 204 |
| DELETE | remove | yes | 204 |

Status code map:

| Code | Meaning |
|---|---|
| 200 | OK — operation succeeded with body |
| 201 | Created — resource created; include `Location` |
| 204 | No content — success, no body |
| 400 | Bad request — malformed / failed validation |
| 401 | Unauthenticated — credentials missing or invalid |
| 403 | Forbidden — authenticated but not allowed |
| 404 | Not found |
| 409 | Conflict — version mismatch, duplicate, illegal state transition |
| 422 | Unprocessable — semantically invalid (when you want to distinguish from syntax-level 400) |
| 429 | Rate limited |
| 500 | Server error — unhandled |
| 503 | Unavailable — temporary; include `Retry-After` |

## Error format

Pick one shape and use it everywhere:

```json
{
  "error": {
    "code": "ORDER_NOT_FOUND",
    "message": "Order 42 was not found.",
    "details": [
      { "field": "orderId", "issue": "no_such_resource" }
    ],
    "traceId": "01HF..."
  }
}
```

- Stable machine-readable `code` (UPPER_SNAKE_CASE) — clients branch on this, not on `message`.
- Human-readable `message` — never expose stack traces.
- Optional `details` array for field-level validation errors.
- `traceId` for support / debugging.

## Pagination

Cursor-based for anything that can grow:

```
GET /orders?limit=50&cursor=eyJpZCI6IjQyIn0
```

Response:
```json
{
  "data": [...],
  "page": {
    "nextCursor": "eyJpZCI6IjkyIn0",
    "hasMore": true
  }
}
```

Offset-based (`page=N&size=M`) is fine for static / small lists; avoid it for unbounded streams (drift on insert, performance death at high N).

## Versioning

- URL path: `/v1/orders`. Pragmatic, easy to route.
- Header (`Accept: application/vnd.example.v1+json`): purer but operationally heavier.
- Deprecate with `Deprecation` and `Sunset` response headers; keep N-1 alive for at least one release cycle.

## Date / time / numbers

- All timestamps: ISO-8601 UTC with explicit offset (`2026-04-30T13:51:00Z`). Never local time.
- Money: integer minor units (cents) + currency code. Never floats.
- Identifiers: prefer ULID / UUID over auto-increment ints in public APIs.

## Idempotency

- For non-idempotent operations (`POST` create, `POST` payment), accept an `Idempotency-Key` header. Same key + same body within a window → return the original result, not a duplicate.

## Authentication

- Bearer tokens (`Authorization: Bearer ...`) for service-to-service and SPA.
- Session cookies for browser apps with same-origin frontends; `HttpOnly`, `Secure`, `SameSite`.
- Never accept credentials in query strings (they leak to logs and Referer headers).

## Rate limiting

- Headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`.
- 429 response with `Retry-After`.
- Per-token / per-IP / per-route — whichever matches the threat.

## GraphQL notes (if applicable)

- One schema, one endpoint (`/graphql`).
- Errors in the `errors` array, not in HTTP status.
- Persisted queries / query allowlist for production to limit surface.
- `@deprecated` on fields with a reason; keep them alive for one release minimum.

## Anti-patterns

- 200 OK with `{ "error": "..." }` body — clients can't tell.
- Verbs in resource paths (`/getOrders`).
- Mixing offset and cursor pagination in the same API.
- Auto-incrementing IDs in URLs (enumeration risk).
- Versioning by branching code instead of API shape ("v2 client" hits same endpoint with a query param).
