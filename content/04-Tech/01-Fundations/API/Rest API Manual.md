## 1) What is a REST API?

**REST** (Representational State Transfer) is an architectural style for designing networked applications. A **REST API** exposes **resources** (things) via **URLs** and manipulates them using standard **HTTP methods**.

**Core idea:**  
- **Resources** are identified by **URIs** (URLs).  
- Clients and servers exchange **representations** of resources (usually JSON).  
- Communication is **stateless** (each request contains everything needed).

### REST constraints (high-level)
- **Client–server separation**
- **[[Statelessness|Statelessness]]**
- **Cacheability**
- **Uniform interface** (consistent patterns)
- **Layered system**
- *(Optional)* **Code on demand** (rare)

---

## 2) REST vs HTTP

REST uses HTTP as the transport, but REST is about **how you structure resources and interactions**.

HTTP provides:
- Methods: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, …
- Status codes: `200`, `201`, `204`, `400`, `401`, `403`, `404`, `409`, `422`, `500`, …
- Headers, caching, auth mechanisms, content negotiation, etc.

---

## 3) Resources and URL Design

### Rules of thumb
- Use **nouns**, not verbs:  
  ✅ `/users`, `/orders`, `/documents`  
  ❌ `/getUsers`, `/createOrder`
- Use **plural** collection names:  
  ✅ `/users` and `/users/{userId}`
- Use **hierarchy** for containment/relationships (sparingly):  
  ✅ `/users/{userId}/orders`  
  ✅ `/orders/{orderId}/items`
- Keep URLs stable; evolve the API with **versioning** and **backward-compatible changes**.

### Common patterns
- **Collections:** `GET /users`  
- **Single resource:** `GET /users/{id}`  
- **Sub-resources:** `GET /users/{id}/orders`  
- **Actions** (when truly not CRUD):  
  Use an action endpoint or a subresource:
  - ✅ `POST /documents/{id}/translations`
  - ✅ `POST /payments/{id}/capture`
  - Avoid `GET /doSomething`

---

## 4) HTTP Methods (and what they mean)

| Method | Typical use | Safe?* | Idempotent?** |
|---|---|---:|---:|
| `GET` | Read resource(s) | ✅ | ✅ |
| `POST` | Create / action | ❌ | ❌ |
| `PUT` | Replace whole resource | ❌ | ✅ |
| `PATCH` | Partial update | ❌ | *Usually* |
| `DELETE` | Delete resource | ❌ | ✅ |

\* **Safe** means it shouldn't change server state.  
\** **Idempotent** means repeating the request has the same effect as doing it once.

### Quick guidance
- `GET /items` — list
- `POST /items` — create new item
- `GET /items/{id}` — fetch one
- `PUT /items/{id}` — replace
- `PATCH /items/{id}` — update some fields
- `DELETE /items/{id}` — delete

---

## 5) Request & Response Basics

### JSON conventions
- Use `application/json`.
- Use consistent naming (commonly `snake_case` or `camelCase`—pick one).
- Prefer explicit types and stable fields.
- Include IDs as strings if they may exceed JS integer range.

### Example: create a user
```http
POST /users HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "email": "alan@example.com",
  "name": "Alan"
}
```

Response:
```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /users/123

{
  "id": "123",
  "email": "alan@example.com",
  "name": "Alan",
  "created_at": "2026-01-24T12:00:00Z"
}
```

---

## 6) Status Codes - Must Know

### Success
- **200 OK** — successful read/update
- **201 Created** — successful create; often include `Location` header
- **204 No Content** — successful but no body (common for delete)
- **206 Partial Content** — partial response (rare; range requests)

### Client errors (your fault)
- **400 Bad Request** — invalid JSON, missing required fields, bad params
- **401 Unauthorized** — missing/invalid auth
- **403 Forbidden** — authenticated but not allowed
- **404 Not Found** — resource doesn’t exist
- **409 Conflict** — state conflict (e.g., duplicate, version mismatch)
- **422 Unprocessable Entity** — validation failed (popular in some frameworks)

### Server errors (our fault)
- **500 Internal Server Error**
- **502/503/504** — upstream / unavailable / timeout

**Tip:** pick one of `400` vs `422` as your “validation errors” and be consistent.

---

## 7) Headers

### Common request headers
- `Accept: application/json` — response format desired
- `Content-Type: application/json` — request body format
- `Authorization: Bearer <token>` — auth token
- `If-None-Match: <etag>` / `If-Modified-Since` — caching validation

### Common response headers
- `Content-Type: application/json`
- `Location: /resource/{id}` — after `201`
- `Cache-Control`, `ETag` — caching
- `Retry-After` — rate limiting / maintenance
- `X-Request-Id` — tracing

---

## 8) Authentication & Authorization

### Common approaches
1) **Bearer tokens (JWT or opaque tokens)**
   - `Authorization: Bearer <token>`
2) **API keys**
   - `Authorization: Api-Key <key>` or `X-API-Key: <key>`
3) **OAuth 2.0 / OpenID Connect**
   - standard for user-delegated access

### JWT basics (what interviewers expect)
- JWT has **header.payload.signature**.
- Signature proves token integrity.
- Don’t put secrets in JWT payload; it’s base64-encoded, not encrypted.
- Validate:
  - signature
  - `exp`, `iss`, `aud`
  - scopes/roles/permissions

### Authorization patterns
- Role-based access control (RBAC)
- Scope-based permissions (`read:docs`, `write:docs`)
- Resource-level checks (`user_id` owns `doc_id`)

---

## 9) Pagination, Filtering, Sorting

### Pagination
- **Offset/limit**: `GET /items?limit=20&offset=40`
- **Cursor-based** (recommended for large datasets):  
  `GET /items?limit=20&cursor=eyJpZCI6IjEyMyJ9`

Cursor response example:
```json
{
  "data": [ ... ],
  "next_cursor": "eyJpZCI6IjEyMyJ9",
  "has_more": true
}
```

### Filtering
- Keep query params simple:
  - `?status=active`
  - `?created_after=2026-01-01`
  - `?q=search_term`

### Sorting
- `?sort=created_at` or `?sort=-created_at` (minus = descending)
- Or `?sort_by=created_at&order=desc`

---

## 10) Error Response Design (Very Important)

Make errors **predictable** and **machine-readable**.

Example:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input.",
    "details": [
      { "field": "email", "issue": "must be a valid email" }
    ],
    "request_id": "req_abc123"
  }
}
```

**Guidelines**
- Always return `request_id` for debugging.
- Use stable `code` strings.
- Don’t leak internal stack traces in production.

---

## 11) Versioning

### Options
- URL versioning: `/v1/users`
- Header versioning: `Accept: application/vnd.company.v1+json`

**Most common in practice:** `/v1/...`

**Rules**
- Don’t break clients silently.
- Add fields rather than rename/remove.
- Deprecate with dates and migration guides.

---

## 12) Idempotency (Critical for Payments/Uploads)

For `POST` create endpoints that might be retried, support an **Idempotency-Key**:
```http
POST /orders
Idempotency-Key: 7c9e6679-7425-40de-944b-e07fc1f90ae7
```

Server stores key → if same key repeats, return the original result.

---

## 13) Caching

### Basic caching headers
- `Cache-Control: public, max-age=60`
- `ETag: "abc123"`
- `If-None-Match: "abc123"` → server can return `304 Not Modified`

**When to cache**
- `GET` responses that are expensive and stable.
- Don’t cache personalized data publicly unless careful.

---

## 14) Rate Limiting

Return meaningful headers:
- `429 Too Many Requests`
- `Retry-After: 30`
- optionally:
  - `X-RateLimit-Limit`
  - `X-RateLimit-Remaining`
  - `X-RateLimit-Reset`

---

## 15) CORS (Web Frontend Needs This)

CORS is a browser security mechanism. If your frontend calls your API from another origin, the API must return headers like:
- `Access-Control-Allow-Origin: https://your-frontend.com`
- `Access-Control-Allow-Methods: GET,POST,PATCH,DELETE`
- `Access-Control-Allow-Headers: Authorization,Content-Type`

---

## 16) Content Negotiation (Optional but good to know)

Clients can ask for formats:
- `Accept: application/json`
- `Accept: application/xml`

Most APIs only support JSON. That’s fine.

---

## 17) File Uploads (Common in Real Systems)

### Option A: multipart upload (simple)
`Content-Type: multipart/form-data`

### Option B: pre-signed URLs (cloud-friendly)
1) Client requests an upload URL
2) Server returns `PUT` URL to S3/GCS
3) Client uploads directly to storage
4) Client notifies server to process

This reduces server load and improves throughput.

---

## 18) Observability: Logging, Tracing, Metrics

Minimum:
- request_id / correlation_id
- structured logs (JSON logs)
- latency metrics, error rates, throughput
- traces across services (OpenTelemetry)

---

## 19) Documentation: OpenAPI/Swagger

**OpenAPI** describes endpoints, schemas, auth, examples.
- Auto-generate docs
- Generate client SDKs
- Keep docs in sync with code

Checklist:
- endpoints + descriptions
- request/response schemas
- examples
- auth methods
- error models

---

## 20) Testing REST APIs

### Types
- Unit tests (handlers/services)
- Integration tests (DB + HTTP)
- Contract tests (client/server expectations)
- Load tests (performance)

### What to test
- status codes
- validation rules
- auth edge cases
- pagination correctness
- idempotency (if used)
- error payload format

---

## 21) Security Essentials

- Use HTTPS everywhere.
- Validate inputs (types, sizes, allowed values).
- Avoid sensitive data in logs.
- Use least-privilege permissions.
- Protect against:
  - injection (SQL/NoSQL)
  - SSRF (if fetching URLs)
  - file upload issues (size/type scanning)
  - broken access control
- Rotate secrets; store in secret managers.
- Apply rate limiting and abuse detection.

---

## 22) Best Practices Summary (Interview Cheat Sheet)

### Resource design
- Nouns + plural collections
- Consistent patterns across endpoints

### Correct method usage
- `GET` safe, `PUT` idempotent, `POST` create/action, `PATCH` partial update

### Responses
- Return correct status codes
- Use consistent JSON shape
- Provide predictable error format

### Production readiness
- Auth + authorization checks
- Pagination and filtering
- Rate limiting and timeouts
- Logging and request IDs
- Documentation (OpenAPI)

---

## 23) Quick Glossary

- **Resource:** entity exposed by API (`user`, `order`, `doc`)
- **Representation:** JSON body representing a resource
- **Idempotent:** repeated requests have same effect
- **Stateless:** server doesn’t store session between requests
- **ETag:** identifier for caching validation
- **CORS:** browser cross-origin control
- **OpenAPI:** API specification standard

---

## 24) Practical Endpoint Template (Copy/Paste)

### Example API surface
- `GET /items` (list, pagination, filters)
- `POST /items` (create)
- `GET /items/{id}` (read)
- `PATCH /items/{id}` (update)
- `DELETE /items/{id}` (delete)

### List response shape
```json
{
  "data": [],
  "meta": {
    "limit": 20,
    "next_cursor": null
  }
}
```

---

## 25) Final Checklist (Use this before shipping)

- [ ] Endpoints follow consistent naming
- [ ] Correct method + status codes
- [ ] Validation and consistent error format
- [ ] Auth + authorization
- [ ] Pagination and filtering for list endpoints
- [ ] Idempotency for retryable creates
- [ ] Logging with request_id
- [ ] Rate limiting and timeouts
- [ ] OpenAPI docs updated
- [ ] Tests cover critical flows

---

## Optional
- REST = 资源 + URL + HTTP 方法 + 无状态  
- URL 用名词、复数：`/users/{id}`  
- 方法：GET查、POST建/动作、PUT全量替换、PATCH部分更新、DELETE删除  
- 状态码：200/201/204；400/401/403/404/409/422；500  
- 必备：统一错误格式、分页、鉴权、日志 request_id、限流、OpenAPI 文档  
