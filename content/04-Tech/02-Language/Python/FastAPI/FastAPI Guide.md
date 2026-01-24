# FastAPI Guide (English)

A practical, beginner-friendly guide to building a production-ready API with **FastAPI**.

---

## 1) What is FastAPI?

FastAPI is a modern Python web framework for building APIs. It is popular because it provides:

- **Automatic request validation** (based on Python type hints)
- **Auto-generated API docs** (Swagger UI and ReDoc)
- **Great performance** (ASGI, async-friendly)
- **Clean, maintainable code** (Pydantic models + dependency injection)

Docs UI endpoints (when running locally):
- Swagger UI: `/docs`
- ReDoc: `/redoc`

---

## 2) Installation and running your first API

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

---

## 3) Core concepts (the mental model)

- **App**: `app = FastAPI()`
- **Route / Endpoint**: a function attached to a path + HTTP method
- **Request**: what the client sends
- **Response**: what your API returns (typically JSON)
- **Schema / Model**: a typed shape for data (Pydantic)
- **Dependency**: reusable logic injected into routes (auth, DB, etc.)

---

## 4) Routes and HTTP methods

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

Common methods:
- `GET` (read)
- `POST` (create)
- `PUT/PATCH` (update)
- `DELETE` (delete)

---

## 5) Path parameters and query parameters

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

---

## 6) Request body + validation (Pydantic models)

FastAPI validates request JSON automatically using Pydantic.

```python
from pydantic import BaseModel

class AskRequest(BaseModel):
    question: str
    top_k: int = 5

@app.post("/ask")
def ask(req: AskRequest):
    return {"question": req.question, "top_k": req.top_k}
```

If the client sends invalid JSON or wrong types, FastAPI returns a **422** error automatically.

---

## 7) Response models (guarantee output shape)

```python
from pydantic import BaseModel

class AskResponse(BaseModel):
    answer: str
    sources: list[str] = []

@app.post("/ask", response_model=AskResponse)
def ask(req: AskRequest):
    return AskResponse(answer="...", sources=["doc1", "doc2"])
```

Benefits:
- Consistent responses
- Better documentation
- Prevents accidental extra fields from leaking

---

## 8) `async def` vs `def` (when to use which)

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

**Important:** If you do blocking work in an async route, you can hurt performance.

---

## 9) File uploads (common for document apps)

```python
from fastapi import UploadFile, File

@app.post("/upload")
async def upload(file: UploadFile = File(...)):
    content = await file.read()
    return {"filename": file.filename, "bytes": len(content)}
```

Tips:
- Prefer `UploadFile` (streaming-friendly)
- Avoid reading huge files into memory at once for large uploads

---

## 10) Error handling with `HTTPException`

```python
from fastapi import HTTPException

@app.get("/secure")
def secure(is_allowed: bool = False):
    if not is_allowed:
        raise HTTPException(status_code=401, detail="Unauthorized")
    return {"ok": True}
```

---

## 11) Dependency Injection (reusable components)

Dependencies let you reuse logic like authentication, DB sessions, or rate limiting.

```python
from fastapi import Depends

def get_current_user():
    # verify token, load user, etc.
    return {"user_id": "123"}

@app.get("/me")
def me(user=Depends(get_current_user)):
    return user
```

---

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

---

## 13) Environment variables and `.env` (local vs production)

Best practice: **store secrets in environment variables**, not in code.

```python
import os

OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
if not OPENAI_API_KEY:
    raise RuntimeError("Missing OPENAI_API_KEY")
```

- Local development: commonly load from a `.env` file (via `python-dotenv`)
- Production: set env vars in the platform (Docker/ECS/K8s/Vercel/etc.)

Security rule:
- **Never commit `.env`** (commit `.env.example` instead)

---

## 14) Project structure (recommended)

A scalable structure for growing APIs:

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

In `main.py`, include routers:

```python
from fastapi import FastAPI
from app.routers import health, upload, ask, translate

app = FastAPI()
app.include_router(health.router)
app.include_router(upload.router)
app.include_router(ask.router)
app.include_router(translate.router)
```

---

## 15) Testing (highly recommended)

```bash
pip install pytest
```

Example test:

```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_health():
    r = client.get("/health")
    assert r.status_code == 200
    assert r.json()["status"] == "ok"
```

Run:
```bash
pytest -q
```

---

## 16) Deployment basics (quick notes)

### Local dev
```bash
uvicorn main:app --reload
```

### Production (conceptually)
- Run with a production server setup (often with multiple workers)
- Put secrets in the platform’s environment variables
- Add logging/monitoring

If you containerize:
- Use Docker
- Expose the correct port
- Run Uvicorn or a production ASGI server inside the container

---

## 17) Common mistakes

- Forgetting CORS for browser frontends → requests blocked by the browser
- Putting secrets in frontend code (anything exposed to the browser is not secret)
- Blocking operations inside `async def` routes
- Committing `.env` to GitHub by accident

---

## Quick checklist

- [ ] Routes defined (`GET/POST/...`)
- [ ] Request models for validation (Pydantic)
- [ ] Response models for consistency
- [ ] CORS configured (if frontend)
- [ ] Secrets in env vars, not code
- [ ] Basic tests
- [ ] Clean project structure