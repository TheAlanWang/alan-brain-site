# Plan B: Proxy FastAPI Through Next.js API Routes (App Router)

This guide shows how to avoid calling the backend like:

```ts
fetch("http://localhost:8000/upload", ...)
```

and instead call:

```ts
fetch("/api/upload", ...)
```

Then **Next.js** forwards (proxies) the request to **FastAPI** on the server side.

---

## Why Plan B works

- Browser calls **Next.js** at `http://localhost:3000/api/upload` (same origin) → less CORS pain.
- Next.js API route runs on the **server**, then calls FastAPI at `http://localhost:8000/upload`.
- Your frontend never needs the backend domain/port.

Request flow:

**Browser** → **Next.js API Route** → **FastAPI** → **Next.js** → **Browser**

---

## Prerequisites

- Next.js App Router project (you have an `app/` folder)
- FastAPI backend running on `http://localhost:8000`

---

## 0) Confirm your Next.js project root

Your `.env.local` must be in the folder that contains `package.json`.

Example (common for your setup):

```
frontend/my-app/
  package.json
  app/
  ...
```

So:
- `.env.local` goes into `frontend/my-app/.env.local`
- API route file goes into `frontend/my-app/app/api/...`

---

## 1) Add a server-side backend URL in Next.js

Create (or edit) this file:

**`frontend/my-app/.env.local`**
```env
BACKEND_URL=http://localhost:8000
```

Notes:
- This is **server-side** env for Next.js API routes.
- No `NEXT_PUBLIC_` prefix because the browser doesn’t need to read it.
- Restart Next dev server after changing env files.

Restart:
```bash
npm run dev
```

---

## 2) Create the proxy API route

Create:

**`frontend/app/api/{upload}/route.ts`**

```ts
import { NextResponse } from "next/server";

const BACKEND = process.env.BACKEND_URL ?? "http://localhost:8000";

export async function POST(req: Request) {
  // 1) Read the browser's multipart/form-data (file upload)
  const formData = await req.formData();

  // 2) Forward it to FastAPI
  const res = await fetch(`${BACKEND}/upload`, {
    method: "POST",
    body: formData,
    // IMPORTANT: do NOT set Content-Type manually
  });

  // 3) Return FastAPI response back to the browser
  const contentType = res.headers.get("content-type") || "";
  if (contentType.includes("application/json")) {
    const data = await res.json();
    return NextResponse.json(data, { status: res.status });
  }

  // Fallback if backend returns non-JSON
  const text = await res.text();
  return new NextResponse(text, { status: res.status });
}
```

---

## 3) Update your frontend fetch call

Change this:

```ts
await fetch("http://localhost:8000/upload", {
  method: "POST",
  body: formData,
});
```

To this:

```ts
await fetch("/api/upload", {
  method: "POST",
  body: formData,
});
```

That’s it. The browser now calls Next.js, and Next.js calls FastAPI.

---

## 4) Backend (FastAPI) still needs to accept file uploads

Your FastAPI endpoint should look like:

```python
from fastapi import FastAPI, UploadFile, File

app = FastAPI()

@app.post("/upload")
async def upload(file: UploadFile = File(...)):
    contents = await file.read()
    return {"filename": file.filename, "bytes": len(contents)}
```

### Ensure you installed `python-multipart`
FastAPI needs this to parse `multipart/form-data`:

```bash
pip install python-multipart
```

---

## 5) Do you still need CORS on FastAPI?

When using Plan B:
- **Browser → Next.js** is same-origin (usually no CORS problem)
- **Next.js → FastAPI** is server-to-server (CORS does not apply)

So in many setups you can **remove or relax** FastAPI CORS during development.

However, keeping CORS enabled doesn’t hurt (especially if you sometimes call FastAPI directly).

---

## 6) Debug checklist (quick)

### A) 404 on `/api/upload`
- Confirm file path:
  - `app/api/upload/route.ts`
- Confirm you are using App Router (`app/` folder exists)

### B) `BACKEND_URL` not working
- Confirm `.env.local` is next to `package.json`
- Restart Next dev server after changes

### C) Upload fails with “python-multipart is required”
- Install:
  ```bash
  pip install python-multipart
  ```

### D) You see “Failed to fetch”
- Confirm FastAPI is running at the URL in `BACKEND_URL`
- Test:
  - `http://localhost:8000/docs`
  - `http://localhost:8000/health` (if you have it)

---

## 7) Optional: proxy other endpoints (ask/translate)

You can add more routes the same way:

- `app/api/ask/route.ts` → forwards to `${BACKEND}/ask`
- `app/api/translate/route.ts` → forwards to `${BACKEND}/translate`

For JSON POST (not files), the proxy looks like:

```ts
import { NextResponse } from "next/server";

const BACKEND = process.env.BACKEND_URL ?? "http://localhost:8000";

export async function POST(req: Request) {
  const body = await req.json();

  const res = await fetch(`${BACKEND}/ask`, {
    method: "POST",
    headers: { "content-type": "application/json" },
    body: JSON.stringify(body),
  });

  const data = await res.json();
  return NextResponse.json(data, { status: res.status });
}
```

---

## 8) Summary

You accomplished:
- Frontend calls: `fetch("/api/upload")`
- Next.js forwards to: `http://localhost:8000/upload`
- Cleaner frontend code + fewer CORS headaches + easier deployment later.

If you paste your current FastAPI `/upload` code, I can tailor the proxy to match your exact response/error format.
