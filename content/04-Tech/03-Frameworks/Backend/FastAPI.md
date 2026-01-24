## 0) What is FastAPI?

**FastAPI** is a modern Python web framework for building APIs with:
- **High performance** (built on Starlette + Pydantic)
- **Type hints first** (Python typing drives validation + docs)
- **Automatic OpenAPI/Swagger docs**
- **Dependency Injection** (`Depends`)
- Strong support for **async** and modern API patterns

FastAPI mainly helps me build clean REST APIs quickly: 
- Using a **route module** to group and organize a set of API endpoints
- **Pydantic** for request/response validation, 
- **Dependencies** for reusable auth/DB logic, and auto-generated OpenAPI docs. 

The key ideas are standard backend concepts—validation, authentication, error handling, and testing.

---

## 1) FastAPI vs REST vs Starlette

- **REST**: an API design style (not a framework)
- **FastAPI**: a framework to implement APIs (RESTful or not)
- **Starlette**: the ASGI toolkit FastAPI is built on (routing, middleware, websockets)
- **Pydantic**: data validation + serialization (schemas)

---

## 2) Minimal app (hello world)

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/health")
def health():
    return {"status": "ok"}
```

Run locally:
```bash
uvicorn main:app --reload
```

- `main` = file name `main.py`
- `app` = FastAPI instance

---

## 3) Routing Basics

### Path operations
- `@app.get()`
- `@app.post()`
- `@app.put()`
- `@app.patch()`
- `@app.delete()`

### Path parameters
```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

### Query parameters
```python
@app.get("/items")
def list_items(limit: int = 20, q: str | None = None):
    return {"limit": limit, "q": q}
```

### Request body
```python
from pydantic import BaseModel

class CreateItem(BaseModel):
    name: str
    price: float

@app.post("/items")
def create_item(payload: CreateItem):
    return payload
```

---

## 4) Pydantic Models (Schemas)

Pydantic models give you:
- validation
- serialization (`.model_dump()` in Pydantic v2)
- OpenAPI schema generation

### Common patterns
```python
class ItemIn(BaseModel):
    name: str
    price: float

class ItemOut(BaseModel):
    id: str
    name: str
    price: float
```

### Response model
```python
@app.get("/items/{id}", response_model=ItemOut)
def get_item(id: str):
    return {"id": id, "name": "pen", "price": 1.2}
```

FastAPI will:
- validate response shape
- filter extra fields (depending on config)

---

## 5) Validation (what you should know)

Use Pydantic field constraints:
```python
from pydantic import BaseModel, Field

class UserIn(BaseModel):
    email: str
    age: int = Field(ge=0, le=130)
```

FastAPI automatically returns `422 Unprocessable Entity` when body validation fails.

---

## 6) Status Codes & Responses

### Set status code
```python
from fastapi import status

@app.post("/items", status_code=status.HTTP_201_CREATED)
def create_item(...):
    ...
```

### Return custom Response
```python
from fastapi.responses import JSONResponse

return JSONResponse(status_code=201, content={"id": "123"})
```

### Common HTTPException
```python
from fastapi import HTTPException

raise HTTPException(status_code=404, detail="Not found")
```

---

## 7) Dependencies (Depends) — FastAPI’s “DI”

Dependencies are the key feature for:
- auth
- DB sessions
- reusable logic (pagination, validation, rate limit)
- shared config

```python
from fastapi import Depends

def get_current_user():
    return {"user_id": "u1"}

@app.get("/me")
def me(user=Depends(get_current_user)):
    return user
```

### Dependency with parameters
```python
def pagination(limit: int = 20, offset: int = 0):
    return {"limit": limit, "offset": offset}

@app.get("/items")
def items(p=Depends(pagination)):
    return p
```

### Dependency scopes (important concept)
Dependencies can run:
- per request (default)
- with caching (same dependency called multiple times in a request runs once)

---

## 8) Routers (APIRouter) & Project Structure

Use routers to keep code organized.

```python
from fastapi import APIRouter

router = APIRouter(prefix="/users", tags=["users"])

@router.get("/{user_id}")
def get_user(user_id: str):
    return {"user_id": user_id}
```

Register:
```python
app.include_router(router)
```

### Recommended structure
```
app/
  main.py
  api/
    routes/
      users.py
      auth.py
  core/
    config.py
    security.py
  schemas/
  services/
  storage/
  tests/
```

---

## 9) Middleware

Middleware runs around every request/response.

Examples:
- CORS
- logging / request-id
- timing
- auth gateways

### CORS
```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://your-frontend.com"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## 10) Auth & Security (what interviewers expect)

FastAPI provides security helpers:
- `OAuth2PasswordBearer`
- `HTTPBearer`
- API key schemes

### Basic Bearer token extraction
```python
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

bearer = HTTPBearer()

def get_token(creds: HTTPAuthorizationCredentials = Depends(bearer)):
    return creds.credentials
```

### JWT notes (conceptual)
- Validate signature, `exp`, `iss`, `aud`
- Don’t store secrets in JWT payload
- Use scopes/roles for authorization checks

---

## 11) Async vs Sync (important)

FastAPI supports both.

- Use `def` for sync handlers
- Use `async def` for async handlers

### Rules of thumb
- If you call blocking code (e.g., standard DB driver), keep it sync or use threadpool.
- If you use async libraries (async DB clients), use `async def`.

**Common pitfall:** using blocking IO inside `async def` without offloading.

---

## 12) Background Tasks

For non-critical work after responding (email, logging, simple processing):
```python
from fastapi import BackgroundTasks

def write_log(msg: str):
    ...

@app.post("/events")
def create_event(bg: BackgroundTasks):
    bg.add_task(write_log, "event created")
    return {"ok": True}
```

For heavy jobs (long processing), use a real queue system (Celery/RQ/etc.).

---

## 13) File Uploads

### Upload via multipart/form-data
```python
from fastapi import UploadFile, File

@app.post("/upload")
async def upload(file: UploadFile = File(...)):
    content = await file.read()
    return {"filename": file.filename, "size": len(content)}
```

---

## 14) Streaming Responses

Useful for large files or streaming text:
```python
from fastapi.responses import StreamingResponse

def iter_data():
    yield b"chunk1\n"
    yield b"chunk2\n"

@app.get("/stream")
def stream():
    return StreamingResponse(iter_data(), media_type="text/plain")
```

---

## 15) WebSockets (real-time)

```python
from fastapi import WebSocket

@app.websocket("/ws")
async def ws(ws: WebSocket):
    await ws.accept()
    await ws.send_text("hello")
    await ws.close()
```

---

## 16) OpenAPI / Swagger Docs (huge advantage)

- Swagger UI: `/docs`
- ReDoc: `/redoc`
- OpenAPI JSON: `/openapi.json`

### Tags and metadata
```python
app = FastAPI(title="DocuBot API", version="1.0.0")
```

---

## 17) Error Handling (Global)

### Custom exception handler
```python
from fastapi import Request
from fastapi.responses import JSONResponse

class MyError(Exception):
    def __init__(self, message: str):
        self.message = message

@app.exception_handler(MyError)
async def my_error_handler(request: Request, exc: MyError):
    return JSONResponse(status_code=400, content={"error": exc.message})
```

---

## 18) Testing FastAPI

### TestClient
```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_health():
    r = client.get("/health")
    assert r.status_code == 200
    assert r.json()["status"] == "ok"
```

### Dependency overrides (super important for tests)
```python
def fake_user():
    return {"user_id": "test"}

app.dependency_overrides[get_current_user] = fake_user
```

---

## 19) Dependency Injection for DB Sessions (Pattern)

Common pattern (sync SQLAlchemy example):
```python
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/users")
def list_users(db=Depends(get_db)):
    ...
```

Key idea: `yield` dependencies allow setup + teardown.

---

## 20) Configuration & Settings

Typical approach: environment variables + settings object.

Principle:
- don’t hardcode secrets
- use `.env` locally, env vars in prod

(Implementation varies; in practice many teams use Pydantic settings.)

---

## 21) Deployment Basics (Uvicorn/Gunicorn)

### Dev
```bash
uvicorn main:app --reload
```

### Production (common)
- Run with multiple workers:
```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4
```

Or use Gunicorn + Uvicorn workers:
```bash
gunicorn -k uvicorn.workers.UvicornWorker main:app -w 4 -b 0.0.0.0:8000
```

**Notes**
- Add a reverse proxy (Nginx) in real production deployments
- Use health checks (`/health`) and proper timeouts

---

## 22) Docker (Typical)

Dockerfile sketch:
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## 23) Performance & Reliability Checklist

- [ ] Use correct async model (avoid blocking inside `async def`)
- [ ] Validate inputs (Pydantic)
- [ ] Return consistent error format
- [ ] Add request ID + structured logging
- [ ] Add rate limiting (if needed)
- [ ] Add timeouts for external calls
- [ ] Health checks + metrics (integrations vary)
- [ ] Use pagination on list endpoints
- [ ] Secure endpoints (auth + authorization)

---

## 24) Common FastAPI Interview Questions (Quick Answers)

1) **Why FastAPI is fast?**  
   Built on ASGI (Starlette) + efficient request handling + async support; uses Pydantic for validation.

2) **What is `Depends`?**  
   Dependency injection system for reusable logic (auth, db sessions, config).

3) **Why `422` happens?**  
   Request body didn’t match the Pydantic schema validation.

4) **Sync vs async?**  
   `async def` for non-blocking IO with async libraries; avoid blocking operations inside async handlers.

5) **How do you structure a FastAPI app?**  
   Use `APIRouter`, separate layers (routes/schemas/services/storage), and central config.

6) **How to test auth?**  
   Use `TestClient` + dependency overrides for `get_current_user`.

7) **How docs are generated?**  
   Type hints + Pydantic schemas → OpenAPI → Swagger/ReDoc.

---

## 25) Cheat Sheet (1-minute scan)

- FastAPI = Starlette (ASGI) + Pydantic (validation)
- Routes: `@app.get/post/...`
- Validation: request/response models
- DI: `Depends` (auth, db session, config)
- Routers: `APIRouter` + `include_router`
- Errors: `HTTPException`, custom exception handlers
- Middleware: CORS, logging
- Testing: `TestClient` + dependency overrides
- Run: `uvicorn main:app`
- Deploy: multiple workers + Docker

---

## 中文速记版
- FastAPI 是 Python API 框架（不是 REST 本身）
- 亮点：类型提示 + 自动校验 + 自动文档 + `Depends` 依赖注入
- 常见：router 拆分、Pydantic schema、JWT/Bearer、CORS、中间件、异常统一处理、测试覆盖
- 部署：uvicorn/gunicorn + docker + health check

