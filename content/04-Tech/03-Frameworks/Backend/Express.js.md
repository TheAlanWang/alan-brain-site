> Express is the most commonly used backend web framework for Node.js.

---

## 0) Express 是什么？(What is Express?)

Express.js is a minimalist web framework for Node.js used to build APIs and web servers by handling HTTP requests and organizing REST routes and middleware.

Key words:
- **Routing（路由）**: URL → handler function
- **Middleware（中间件）**: request pipeline（请求管道）
- **Request/Response（请求/响应）**: `req`, `res`
- **Error handling（错误处理）**: global error middleware

---

## 1) Express 在 MERN 里是什么角色？
**MERN** = MongoDB + Express + React + Node.js
- Node.js = runtime（运行时）
- Express = backend framework
- MongoDB = database
- React/Next.js = frontend

> In MERN, Express is the backend framework that defines REST endpoints and middleware on top of Node.js.

---

## 2) Minimum working Demo

```js
import express from "express";
const app = express();

// Built-in middleware: parse JSON body
app.use(express.json());

app.get("/health", (req, res) => {
  res.status(200).json({ status: "ok" });
});

app.listen(3000, () => console.log("Server running on 3000"));
```

understand:
- `app.use(express.json())` = 解析 JSON request body
- `app.get(...)` = route handler
- `res.status(...).json(...)` = set status + return JSON

---

## 3) Routing

### 3.1 Basic Routing
```js
app.get("/users", (req, res) => {});
app.post("/users", (req, res) => {});
app.patch("/users/:id", (req, res) => {});
app.delete("/users/:id", (req, res) => {});
```

### 3.2 Path params（路径参数）
- `/users/:id` → `req.params.id`

```js
app.get("/users/:id", (req, res) => {
  const id = req.params.id;
  res.json({ id });
});
```

### 3.3 Query params（查询参数）
- `/users?limit=10` → `req.query.limit`

```js
app.get("/users", (req, res) => {
  const limit = Number(req.query.limit ?? 20);
  res.json({ limit });
});
```

### 3.4 Body（请求体）
- `req.body` 需要 `express.json()` 才会有

```js
app.post("/users", (req, res) => {
  const payload = req.body;
  res.status(201).json(payload);
});
```

---

## 4) Router 分层（APIRouter-like design）

你必须会把路由拆出去（面试 + 实战都喜欢）：

`routes/users.js`
```js
import { Router } from "express";
const router = Router();

router.get("/", (req, res) => res.json([]));
router.get("/:id", (req, res) => res.json({ id: req.params.id }));

export default router;
```

`app.js`
```js
import express from "express";
import userRouter from "./routes/users.js";

const app = express();
app.use(express.json());

// mount router
app.use("/users", userRouter);

export default app;
```

Explain:  
- `Router()` 用于模块化路由  
- `app.use("/users", userRouter)` = route prefix（前缀）

---

## 5) Middleware（中间件）—— Express 的灵魂

**Middleware = a function that runs before/after your route handler.**  
中间件就是请求在进入业务逻辑之前/之后要走的一段“管道”。

### 5.1 Middleware signature（函数签名）
```js
(req, res, next) => {}
```
- `next()` = 继续走下一个 middleware/route
- 不调用 `next()` 并且不返回 response → request 会挂住（常见 bug）

### 5.2 Common Middleware Use Cases
- logging（日志）
- auth（鉴权）
- validation（参数校验）
- rate limiting（限流）
- error handling（统一错误）

### 5.3 Example: logging middleware
```js
app.use((req, res, next) => {
  console.log(req.method, req.path);
  next();
});
```

### 5.4 Example: auth middleware (JWT)
```js
function auth(req, res, next) {
  const header = req.headers.authorization || "";
  const token = header.startsWith("Bearer ") ? header.slice(7) : null;
  if (!token) return res.status(401).json({ error: "Missing token" });

  // verify token (pseudo)
  req.user = { id: "u1" };
  next();
}

app.get("/me", auth, (req, res) => res.json(req.user));
```

---

## 6) Error Handling

### 6.1 Error middleware signature
```js
(err, req, res, next) => {}
```

### 6.2 推荐：统一错误出口（Global error handler）
```js
app.use((err, req, res, next) => {
  console.error(err);

  const status = err.statusCode || 500;
  res.status(status).json({
    error: {
      code: err.code || "INTERNAL_ERROR",
      message: err.message || "Something went wrong",
      request_id: req.id
    }
  });
});
```

**面试点：**
- 你要能说：Express 里错误处理通常放在所有 routes **后面**。
- `next(err)` 会把错误交给 error middleware。

---

## 7) Request Validation（校验）—— 真实项目必做

推荐你会讲两种方式：
1) 简单手写校验（not recommended）
2) 用 schema validator（推荐）比如 **Zod/Joi**

Example (Zod 伪代码感受):
```js
// const schema = z.object({ email: z.string().email() })

app.post("/users", (req, res, next) => {
  try {
    // schema.parse(req.body)
    res.status(201).json({ ok: true });
  } catch (e) {
    e.statusCode = 400;
    e.code = "VALIDATION_ERROR";
    next(e);
  }
});
```

---

## 8) REST Best Practices（Express 写 REST 的必备点）

你必须掌握：
- URL 用资源名（nouns）：`/users/:id`
- 正确方法：GET/POST/PATCH/DELETE
- 状态码：
  - `201 Created` 创建成功
  - `204 No Content` 删除成功（无 body）
  - `400/422` 校验失败
  - `401` 未登录
  - `403` 无权限
  - `404` 不存在
- 一致的错误响应结构（error.code/error.message/request_id）

---

## 9) Async handlers（异步处理）常见坑

Express route 用 async 时，错误可能不会自动进入 error middleware（取决于版本/写法）。  
推荐写法：**catch + next(err)**

```js
app.get("/data", async (req, res, next) => {
  try {
    const result = await fetchSomething();
    res.json(result);
  } catch (err) {
    next(err);
  }
});
```

---

## 10) CORS（跨域）你必须知道

如果前端域名不同（比如 Vercel），浏览器会触发 CORS：
- 用 `cors` middleware 或手写 header

你至少要知道：  
CORS = browser security policy（浏览器安全策略），server 必须允许 origin。

---

## 11) Security checklist（Express 后端最常见安全点）

- 不要信任用户输入（validate）
- password 用 bcrypt/argon2 hash
- JWT 验证 signature + exp
- rate limiting 防暴力
- 不记录敏感信息到 logs
- 使用 HTTPS（部署层）
- 防 NoSQL injection（Mongo）/ SQL injection（SQL）

---

## 12) Testing — Express 面试加分项

你至少要知道：
- Jest 做单测
- Supertest 做 API 集成测试

Example:
```js
import request from "supertest";
import app from "../app.js";

test("GET /health", async () => {
  const res = await request(app).get("/health");
  expect(res.statusCode).toBe(200);
  expect(res.body.status).toBe("ok");
});
```

---

## 13) Production readiness（生产环境你要能讲）

- structured logs（结构化日志）+ request_id
- health endpoint（/health）
- rate limiting
- timeouts（对外部依赖）
- graceful shutdown（SIGTERM）
- 部署：Docker + env vars

---

## 14) Question（Express）

### Q1: What is middleware in Express?
A: Middleware is a function that runs in the request-response cycle and can modify `req/res` or terminate the request, or call `next()` to pass control.

### Q2: How do you handle errors globally?
A: Use an error-handling middleware `(err, req, res, next)` registered after routes.

### Q3: How do you protect routes?
A: Add auth middleware (JWT verification) before protected handlers.

### Q4: How do you validate requests?
A: Use schema validation (Zod/Joi) and return consistent error responses.

---

## 15) One-minute cheat sheet（1分钟速记）

- Express 是 Node.js 的 web framework（后端）
- 核心：routing + middleware + global error handler
- REST：方法/状态码/资源 URL
- auth：JWT middleware
- validation：Zod/Joi
- testing：Jest + Supertest
- production：logs, request_id, health, rate limit, docker