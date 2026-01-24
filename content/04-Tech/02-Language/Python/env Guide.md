# .env Manual (Knowledge Guide)
A practical, stack-agnostic guide to understanding `.env` files and environment variables.

---
## 1) What is a `.env` file?

A `.env` file is a **plain text file** that stores **environment variables** (configuration values) as:
```env
KEY=value
```

It commonly contains:
- API keys (OpenAI, AWS, Stripe, etc.)
- Database URLs / credentials
- JWT secrets
- Redis URLs
- Feature flags

---

## 2) Why use environment variables?

### Separation of code vs. configuration
Your **code stays the same**, while config varies by environment:
- local development
- staging
- production

### Security
Secrets should not be hard-coded in your source code or committed to Git.

### Deployment friendliness
Most platforms (Docker, ECS, Kubernetes, Vercel, etc.) have standard ways to inject env vars.

> **Key idea:** `.env` is mainly a **local developer convenience**.  
> In production, env vars are usually set in the platform (not a file).

---

## 3) `.env` vs. “environment variable” (important difference)

- **Environment variable**: a variable set in the OS/process environment.
  Example in terminal:
  ```bash
  export OPENAI_API_KEY="abc"
  ```

- **.env file**: a file that *contains lines you want to load into environment variables*.

Your program can only “see” `.env` values **if something loads them**.

---

## 4) Who loads `.env`?

Different stacks load it differently:

### Node / Next.js
- Next.js can automatically read `.env.local`, `.env.development`, `.env.production` depending on mode.
- Browser-exposed variables must start with `NEXT_PUBLIC_`.

### Python / FastAPI
Python does **not** automatically read `.env`. You usually need:
- a library like `python-dotenv`, or
- load variables via OS / Docker / platform settings.
```python
from dotenv import load_dotenv
import os

load_dotenv()
key = os.getenv("OPENAI_API_KEY")
```

### Docker / Docker Compose
- Docker can load env vars from a file via `--env-file`.
- Docker Compose supports `env_file:`.

### AWS ECS / Kubernetes
Usually **don’t use `.env` files directly**.
You set variables in:
- ECS Task Definition (env vars / secrets)
- Kubernetes ConfigMaps / Secrets
- SSM Parameter Store / Secrets Manager, etc.

---

## 5) `.env` format rules (common pitfalls)

### Basic format
```env
KEY=value
```

### Comments
```env
# This is a comment
KEY=value
```

### No spaces around `=`
✅ `KEY=value`  
❌ `KEY = value`

### Quoting
Use quotes only when needed (spaces/special chars):
```env
JWT_SECRET="hello world"
```

### One variable per line
Avoid putting multiple values on a single line.

### Not JSON (no commas)
❌ `KEY=value,`  
✅ `KEY=value`

---

## 6) Common `.env` file names

You may see:
- `.env` — default local values
- `.env.local` — local overrides (common in Next.js)
- `.env.development` / `.env.production` — environment-specific
- `.env.example` — template committed to Git (recommended)

**Best practice:**
- commit `.env.example`
- ignore real `.env*` files containing secrets

---

## 7) Precedence (which value wins?)

Depends on the stack, but typically:

**OS environment variables override `.env` values**

Example:
- `.env`: `LOG_LEVEL=INFO`
- production platform sets: `LOG_LEVEL=WARNING`
→ effective value: `WARNING`

---

## 8) Typical backend variables

```env
# API
OPENAI_API_KEY=...
OPENAI_MODEL=...

# Auth
JWT_SECRET_KEY=...
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# Database
DATABASE_URL=postgresql://...

# Cache
REDIS_URL=redis://localhost:6379/0

# App config
ENV=development
LOG_LEVEL=INFO
```

---

## 9) Typical frontend variables (Next.js)

### Rule: if the browser can access it, it’s NOT secret

In Next.js, variables exposed to client-side JS must start with:

```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000
```

So **never** put secrets under `NEXT_PUBLIC_`.

---

## 10) Security rules (memorize these)

1) **Never commit real `.env` files**  
2) **Never put secrets in frontend env** (anything exposed to the browser)  
3) **Rotate keys immediately if leaked**  
4) Prefer production secrets storage:
   - AWS Secrets Manager / SSM Parameter Store
   - Vercel env vars
   - GitHub Actions secrets
   - Kubernetes Secrets

---

## 11) Quick mental model

Think of env vars as **runtime inputs** to your app:

`App(code, ENV_VARS) → behavior`

`.env` is just one way to provide those inputs locally.

---

## Appendix: `.gitignore` suggestion

```gitignore
.env
.env.*
!.env.example
```
