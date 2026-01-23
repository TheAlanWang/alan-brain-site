### What is an HTTP API?

An **HTTP API** exposes backend functionality through **HTTP endpoints**. Clients send HTTP requests (often with JSON) and receive HTTP responses (often JSON).

### Core pieces
- **Endpoint**: e.g., `/users`, `/orders/123`
- **Methods**: `GET` (read), `POST` (create), `PUT/PATCH` (update), `DELETE` (remove)
- **Headers**: e.g., `Content-Type`, `Authorization`
- **Body**: usually JSON for write operations
- **Status codes**: `200/201/400/401/403/404/500`

### HTTP vs HTTPS
**HTTPS** = HTTP + **TLS encryption** (what you should use in real systems).
### How “keys” show up in HTTP APIs
- **API keys**
- **Bearer tokens / JWT**
- **OAuth2**
- **HMAC request signatures**

**REST API** = a **design style/standard** for _some_ HTTP APIs (how you structure endpoints, methods, resources, status codes, statelessness).