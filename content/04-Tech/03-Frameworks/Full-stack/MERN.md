# MERN - Must-Know Guide

> MERN = **MongoDB + Express + React + Node.js**  
> Goal: a practical checklist of what you must know to pass interviews and build real projects.

---

## 0) MERN “What is what” (Don’t mix terms)

- **MongoDB**: Database (NoSQL document store)
- **Node.js**: Runtime (server-side JavaScript environment)
- **Express**: Backend framework (build REST APIs on Node)
- **React**: Frontend library (UI components)
- **Next.js** (often with MERN): React framework (SSR/SSG + backend capabilities)

---

## 1) JavaScript & TypeScript (Required foundation)

### Must know (JS)
- `let/const`, scope, closure
- `this` binding (arrow vs normal function)
- Prototype chain basics
- `async/await`, Promises (`all`, `allSettled`, `race`)
- Error handling: try/catch, async error patterns

### Strongly recommended (TS)
- `type` vs `interface`
- generics, union/intersection, optional fields
- DTO typing for APIs

---

## 2) Node.js backend essentials

### 2.1 Event Loop (Top interview topic)
- Non-blocking I/O
- microtasks (Promises) vs macrotasks (timers)
- `setTimeout` vs `setImmediate` vs `process.nextTick`
- When Node struggles: CPU-bound work → worker threads / queue

### 2.2 Modules & tooling
- CommonJS vs ESM
- npm scripts, semver, lockfiles
- dotenv / env vars for config

### 2.3 Streams (real-world)
- Handling large files without loading into memory
- Backpressure concept

---

## 3) Express (Backend framework you must be able to build with)

### 3.1 Core concepts
- Routing: `GET/POST/PATCH/DELETE`
- Middleware pipeline: `app.use(...)`
- Global error handler: `app.use((err, req, res, next) => ...)`
- Request parsing: `express.json()`

### 3.2 Minimal API skeleton
```js
import express from "express";
const app = express();
app.use(express.json());

app.get("/health", (req, res) => res.json({ status: "ok" }));

app.listen(3000);
```

### 3.3 What to know for interviews
- Middleware order and how `next()` works
- How to implement auth middleware (JWT)
- How to validate request bodies (Zod/Joi)
- Consistent error response format (code/message/request_id)

### 3.4 Recommended structure
```
src/
  app.ts
  routes/
  controllers/
  services/
  repositories/
  middlewares/
  config/
  tests/
```

---

## 4) REST API design (MERN must-have)

### You must know
- Resource-oriented URLs: `/users/{id}`
- Status codes: 200/201/204/400/401/403/404/409/422/500
- Pagination: limit/offset vs cursor
- Filtering/sorting with query params
- Idempotency for retryable POST (Idempotency-Key)
- Versioning (`/v1/...`)

### PUT vs PATCH
- PUT: full replace, idempotent
- PATCH: partial update

---

## 5) Authentication & Security (Critical)

### Authentication
- JWT access token + refresh token (conceptually)
- Password hashing: bcrypt/argon2 (never store raw passwords)
- Secure cookie vs Authorization header

### Authorization
- RBAC (roles) or scopes
- Resource-level checks (owner can access doc)

### Web security you should mention
- Input validation (SQL/NoSQL injection)
- CORS basics
- Rate limiting / brute-force protection
- HTTPS everywhere
- Don’t leak secrets in logs

---

## 6) MongoDB essentials (Database must-know)

### 6.1 Data model
- Document structure (JSON-like)
- Embedding vs referencing
- ObjectId basics

### 6.2 Query basics
- `find`, `findOne`, `insertOne`, `updateOne`, `deleteOne`
- filters, projections
- sorting, limiting

### 6.3 Indexes (interview-level)
- What is an index and why it speeds reads
- Tradeoff: write cost + storage
- When to index: frequent filters/sorts

### 6.4 Transactions (know the idea)
- MongoDB supports multi-document transactions (replica set)
- Use when you need atomic multi-step operations

---

## 7) Data access layer (ODM/ORM)

### Popular choices
- **Mongoose** (classic ODM)
- **Prisma** (great TypeScript experience)

What to know:
- schema definition vs flexible Mongo documents
- validation at app layer vs DB layer
- migrations (Prisma) vs model evolution strategy (Mongoose)

Interview talking point:
> Mongo is flexible, but we still enforce schema with validation at the application layer.

---

## 8) React essentials (Frontend must-know)

### Must know
- component, props, state
- controlled inputs, forms
- lifting state up
- hooks: `useState`, `useEffect`, `useMemo`, `useCallback`
- rendering lists and keys
- conditional rendering

### Data fetching
- fetch on client side
- loading/error states
- caching strategy (basic)

### Performance basics
- avoid unnecessary re-renders
- memoization where appropriate

---

## 9) React + API integration 

### Must know patterns
- Use environment variables for API base URL
- Handle auth token (store carefully; prefer httpOnly cookie if possible)
- Error handling and user-friendly messages
- Optimistic UI updates (optional, advanced)

---

## 10) Next.js

Even if MERN is React, Next.js:
- SSR/SSG, routing
- API Routes / Server Actions (full-stack capability)
- Where code runs: client vs server
- Deployment on Vercel

Key concept:
> Next.js is a React framework that can run both frontend rendering and backend logic.

---

## 11) Testing (must know at least basics)

### Backend
- Jest + Supertest (HTTP tests)
- Mock external dependencies (DB, HTTP calls)
- Test auth + error cases

### Frontend
- React Testing Library (basic)
- E2E: Playwright/Cypress (optional)

---

## 12) Deployment & Production Basics (big bonus)

### Docker
- build image, run container
- env vars
- expose ports

### Hosting
- Frontend: Vercel
- Backend: Render/Fly.io/AWS/GCP (varies)
- DB: MongoDB Atlas

### Observability
- structured logs
- request_id
- basic metrics (latency/error rate)

---

## 13) System design mini-topics (MERN-level)

- caching (Redis) + TTL
- queues (BullMQ) for background jobs
- file upload architecture (presigned URL)
- rate limiting (token bucket concept)
- eventual consistency and retries


---

## 14) Questions (Practice)

### Node/Express
- Explain the event loop.
- How does middleware work in Express?
- How do you handle errors globally?
- How do you secure APIs with JWT?

### MongoDB
- Embedding vs referencing?
- What is an index? Tradeoffs?
- When do you need transactions?

### React
- Explain `useEffect` and common pitfalls.
- How do you manage state across components?
- How do you avoid unnecessary re-renders?

### System design
- Design pagination.
- Add caching to an API.
- Design an upload pipeline.

---

## 15) cheat sheet

- Node.js runtime + Express framework for REST APIs
- MongoDB document DB, schema enforced via validation/ODM
- React UI with hooks, data fetching, and state management
- Auth with JWT, password hashing, and rate limiting
- Testing with Jest/Supertest; production basics with Docker and logs

