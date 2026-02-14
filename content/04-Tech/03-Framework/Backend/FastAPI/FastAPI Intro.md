## 0) What is FastAPI?

FastAPI is a modern Python web framework for building APIs. It’s popular because it provides:

- **High performance** (built on **Starlette** + **Pydantic**)
- **Type hints first** → validation + docs
- Automatic **OpenAPI / Swagger** documentation
- **Dependency Injection** with `Depends`
- Strong support for **async** and modern API patterns


## 1) FastAPI vs REST vs Starlette vs Pydantic

- **REST**: an API design style (not a framework)
- **FastAPI**: a framework to implement APIs (RESTful or not)
- **Starlette**: the ASGI toolkit FastAPI is built on (routing, middleware, websockets)
- **Pydantic**: data validation + serialization (schemas)


## 2) Installation & run your first API

### Install
```bash
pip install fastapi uvicorn
```

### Minimal `main.py`
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def root():
    return {"message": "Hello, FastAPI!"}
```

### Run locally
```bash
uvicorn main:app --reload
```

- `main` = filename (`main.py`)
- `app` = FastAPI instance name
- `--reload` = auto-restart on code changes (dev only)


## 3) Core concepts (mental model)

- **App**: `app = FastAPI()`
- **Route / Endpoint**: a function attached to a path + HTTP method
- **Request**: what the client sends
- **Response**: what your API returns (typically JSON)
- **Schema / Model**: a typed shape for data (Pydantic)
- **Dependency**: reusable logic injected into routes (auth, DB, etc.)


## 4) Routing basics (HTTP methods)

### Path operations
- `@app.get()`
- `@app.post()`
- `@app.put()`
- `@app.patch()`
- `@app.delete()`

### Examples
```python
@app.get("/health")
def health():
    return {"status": "ok"}

@app.post("/items")
def create_item():
    return {"created": True}

@app.delete("/items/{item_id}")
def delete_item(item_id: int):
    return {"deleted": item_id}
```


## 5) Path parameters & query parameters

### Path parameter
```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

### Query parameter
```python
@app.get("/search")
def search(q: str, limit: int = 10):
    return {"q": q, "limit": limit}
```

Request example:
- `/search?q=fastapi&limit=5`


## 6) Request body + validation (Pydantic models)

FastAPI validates request JSON automatically using Pydantic.

```python
from pydantic import BaseModel, Field

class CreateItem(BaseModel):
    name: str
    price: float

class AskRequest(BaseModel):
    question: str
    top_k: int = 5

class UserIn(BaseModel):
    email: str
    age: int = Field(ge=0, le=130)
```

When request body validation fails, FastAPI returns:
- **422 Unprocessable Entity**


## 7) Response models (guarantee output shape)

Use `response_model=` to enforce a stable response schema.

```python
from pydantic import BaseModel

class ItemOut(BaseModel):
    id: str
    name: str
    price: float

@app.get("/items/{id}", response_model=ItemOut)
def get_item(id: str):
    return {"id": id, "name": "pen", "price": 1.2}
```

Benefits:
- Consistent responses
- Better documentation
- Prevents accidental extra fields from leaking


## 8) Status codes & custom responses

### Set status code
```python
from fastapi import status

@app.post("/items", status_code=status.HTTP_201_CREATED)
def create_item(...):
    ...
```

### Return a custom response
```python
from fastapi.responses import JSONResponse

return JSONResponse(status_code=201, content={"id": "123"})
```


## 9) Error handling with `HTTPException`

```python
from fastapi import HTTPException

@app.get("/secure")
def secure(is_allowed: bool = False):
    if not is_allowed:
        raise HTTPException(status_code=401, detail="Unauthorized")
    return {"ok": True}
```


## 10) Dependencies (Depends) — FastAPI’s DI

Dependencies are the key feature for:
- auth
- DB sessions
- reusable logic (pagination, validation, rate limit)
- shared config

```python
from fastapi import Depends

def get_current_user():
    # verify token, load user, etc.
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

**Note:** A dependency called multiple times in one request can be cached and run once.


## 11) Routers (APIRouter) & project structure

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
from fastapi import FastAPI
app = FastAPI()
app.include_router(router)
```

### Recommended structure
```
app/
  main.py
  routers/
    health.py
    upload.py
    ask.py
    translate.py
  models/
    schemas.py
  services/
    llm.py
    retriever.py
  core/
    config.py
```


## 12) Middleware (CORS is the most common)

If you have a browser frontend (React/Next.js), you likely need CORS.

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```


## 13) Auth & security (common patterns)

FastAPI provides security helpers:
- `OAuth2PasswordBearer`
- `HTTPBearer`
- API key schemes

### Basic Bearer token extraction
```python
from fastapi import Depends
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

bearer = HTTPBearer()

def get_token(creds: HTTPAuthorizationCredentials = Depends(bearer)):
    return creds.credentials
```

### JWT notes (conceptual)
- Validate signature and claims like `exp`, `iss`, `aud`
- Don’t store secrets in JWT payload
- Use scopes/roles for authorization checks


## 14) `async def` vs `def` (when to use which)

- Use **`async def`** when you do **I/O** (network calls, reading files, DB calls with async drivers).
- Use **`def`** for simple logic or CPU work.

```python
@app.get("/sync")
def sync_route():
    return {"mode": "sync"}

@app.get("/async")
async def async_route():
    return {"mode": "async"}
```

**Pitfall:** doing blocking work inside an async route can hurt performance.


## 15) Background tasks

For non-critical work after responding (logging, small processing):

```python
from fastapi import BackgroundTasks

def write_log(msg: str):
    ...

@app.post("/events")
def create_event(bg: BackgroundTasks):
    bg.add_task(write_log, "event created")
    return {"ok": True}
```

For heavy jobs, use a real queue system (Celery/RQ/etc.).


## 16) File uploads

```python
from fastapi import UploadFile, File

@app.post("/upload")
async def upload(file: UploadFile = File(...)):
    content = await file.read()
    return {"filename": file.filename, "bytes": len(content)}
```

Tips:
- Prefer `UploadFile` (streaming-friendly)
- Avoid reading huge files into memory at once


## 17) Streaming responses

```python
from fastapi.responses import StreamingResponse

def iter_data():
    yield b"chunk1\n"
    yield b"chunk2\n"

@app.get("/stream")
def stream():
    return StreamingResponse(iter_data(), media_type="text/plain")
```


## 18) WebSockets (real-time)

```python
from fastapi import WebSocket

@app.websocket("/ws")
async def ws(ws: WebSocket):
    await ws.accept()
    await ws.send_text("hello")
    await ws.close()
```


## 19) OpenAPI / Swagger docs

- Swagger UI: `/docs`
- ReDoc: `/redoc`
- OpenAPI JSON: `/openapi.json`

### Metadata
```python
from fastapi import FastAPI
app = FastAPI(title="DocuBot API", version="1.0.0")
```


## 20) Global error handling (custom exception handler)

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


## 21) Testing FastAPI

Install:
```bash
pip install pytest
```

Example:
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


## 22) Configuration & environment variables

Best practice: store secrets in **environment variables**, not in code.

```python
import os

OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
if not OPENAI_API_KEY:
    raise RuntimeError("Missing OPENAI_API_KEY")
```

- Local dev: use `.env` (commonly via `python-dotenv`)
- Production: set env vars in the platform (Docker/ECS/K8s/Vercel/etc.)

Security rule:
- **Never commit `.env`** (commit `.env.example` instead)


## 23) Docker (typical)

Dockerfile sketch:
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```


## 24) Deployment basics

### Dev
```bash
uvicorn main:app --reload
```

### Production (common)
```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --workers 4
```

Or Gunicorn + Uvicorn workers:
```bash
gunicorn -k uvicorn.workers.UvicornWorker main:app -w 4 -b 0.0.0.0:8000
```

Notes:
- In real production, use a reverse proxy (e.g., Nginx), health checks (`/health`), and proper timeouts.


## 25) Performance & reliability checklist

- [ ] Use correct async model (avoid blocking inside `async def`)
- [ ] Validate inputs (Pydantic)
- [ ] Return consistent error format
- [ ] Add request ID + structured logging
- [ ] Add rate limiting (if needed)
- [ ] Add timeouts for external calls
- [ ] Health checks + metrics
- [ ] Use pagination on list endpoints
- [ ] Secure endpoints (auth + authorization)


## 26) Common mistakes

- Forgetting CORS for browser frontends → requests blocked by the browser
- Putting secrets in frontend code (anything exposed to the browser is not secret)
- Blocking operations inside `async def` routes
- Committing `.env` to GitHub by accident


## 27) Common FastAPI interview questions (quick answers)

1) **Why is FastAPI fast?**  
Built on ASGI (Starlette) + efficient request handling + async support; uses Pydantic for validation.

2) **What is `Depends`?**  
Dependency injection system for reusable logic (auth, DB sessions, config).

3) **Why does `422` happen?**  
Request body didn’t match the Pydantic schema validation.

4) **Sync vs async?**  
Use `async def` for non-blocking IO with async libraries; avoid blocking operations inside async handlers.

5) **How do you structure a FastAPI app?**  
Use `APIRouter`, separate layers (routes/schemas/services/storage), and central config.

6) **How to test auth?**  
Use `TestClient` + dependency overrides for `get_current_user`.

7) **How are docs generated?**  
Type hints + Pydantic schemas → OpenAPI → Swagger/ReDoc.


## 28) Cheat sheet (1-minute scan)

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


## 29) 中文速记版

- FastAPI 是 Python API 框架（不是 REST 本身）
- 亮点：类型提示 + 自动校验 + 自动文档 + `Depends` 依赖注入
- 常见：router 拆分、Pydantic schema、JWT/Bearer、CORS、中间件、异常统一处理、测试覆盖
- 部署：uvicorn/gunicorn + docker + health check
