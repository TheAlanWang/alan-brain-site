# Next.js Guide — Practical, App Router First

This guide focuses on **Next.js (App Router)** and the React concepts you’ll actually use when building a real app (e.g., a PDF assistant with a FastAPI backend).

---

## 0) What is Next.js?

**React** is a UI library. **Next.js** is a framework built on React that adds:

- File-based **routing**
- **Server Components** (default) + **Client Components** (interactive UI)
- Rendering strategies (**SSR/SSG/ISR**)
- Performance features (code-splitting, image optimization)
- Production build + deployment support

---

## 1) Create a project

```bash
npx create-next-app@latest
```

Recommended choices:
- TypeScript: **Yes**
- ESLint: **Yes**
- App Router: **Yes**

Run dev server:
```bash
npm run dev
```

Open:
- http://localhost:3000

---

## 2) Project structure (App Router)

Typical:
```
my-app/
  app/
    layout.tsx
    page.tsx
    globals.css
  public/
  package.json
  next.config.ts (optional)
  .env.local (you create this)
```

> Some projects use `src/app/...` instead of `app/...`. Both are valid.  
> The **Next.js project root** is the folder with `package.json`.

---

## 3) Routing (file-based)
In App Router, folders map to routes:
```
app/page.tsx                 -> /
app/about/page.tsx           -> /about
app/dashboard/page.tsx       -> /dashboard
```

### Dynamic routes
```
app/users/[id]/page.tsx      -> /users/123
```

Example:
```tsx
type Props = { params: { id: string } };

export default function UserPage({ params }: Props) {
  return <div>User id: {params.id}</div>;
}
```

---

## 4) Layouts and shared UI
`app/layout.tsx` wraps all pages. Great for navbar/footer/global styles.

Example:
```tsx
import "./globals.css";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <header>My App</header>
        {children}
      </body>
    </html>
  );
}
```

---

## 5) Server Components vs Client Components (IMPORTANT)
### Server Components (default)
- Run on the **server**
- Can fetch data securely
- Do **not** ship component JS to the browser
- Cannot use React hooks like `useState`, `useEffect`, or event handlers like `onClick`

### Client Components
- Run in the **browser**
- Required for interactivity (state, effects, events)
- Must include `"use client"` at the top

Example client component:
```tsx
"use client";
import { useState } from "react";

export default function Counter() {
  const [n, setN] = useState(0);
  return <button onClick={() => setN(n + 1)}>{n}</button>;
}
```

### Quick rule
- Needs **state/effects/events/browser APIs** → Client Component  
- Otherwise → keep as Server Component for performance

---

## 6) Data fetching patterns

### A) Fetch on the server (recommended when possible)
Server Components can do:
```tsx
export default async function Page() {
  const res = await fetch("https://example.com/api", { cache: "no-store" });
  const data = await res.json();
  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

Common cache options:
- `{ cache: "no-store" }` → always fresh
- `{ next: { revalidate: 60 } }` → revalidate every 60s (ISR-like)

### B) Fetch on the client (for interactive screens)
```tsx
"use client";
import { useEffect, useState } from "react";

export default function Page() {
  const [data, setData] = useState<any>(null);

  useEffect(() => {
    fetch("/api/health")
      .then((r) => r.json())
      .then(setData);
  }, []);

  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

---

## 7) Environment variables (`.env.local`)

Create `.env.local` in the **Next.js project root** (same folder as `package.json`).

Example:
```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000
```

Access in code:
```ts
const base = process.env.NEXT_PUBLIC_API_BASE_URL;
```

Important:
- Any variable starting with `NEXT_PUBLIC_` is visible in the browser.
- Do **not** put secrets in `NEXT_PUBLIC_` vars.
- Restart `npm run dev` after changing `.env.local`.

---

## 8) Connect Next.js frontend to FastAPI backend

You have two common setups:
### Option A: Browser calls FastAPI directly (simple)
- Next.js runs on `http://localhost:3000`
- FastAPI runs on `http://localhost:8000`
- You must enable **CORS** in FastAPI.

FastAPI CORS example:
```py
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

Client request:
```ts
const base = process.env.NEXT_PUBLIC_API_BASE_URL!;
const res = await fetch(`${base}/health`);
```

### Option B: Proxy through Next.js (often best for production)
Browser calls Next.js → Next.js server calls FastAPI  
Advantages: avoids CORS and hides backend URL.

Create a route handler:
`app/api/ask/route.ts`
```ts
export async function POST(req: Request) {
  const body = await req.json();
  const res = await fetch(`${process.env.BACKEND_URL}/ask`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(body),
  });
  const data = await res.json();
  return Response.json(data, { status: res.status });
}
```

Then the browser calls:
```ts
await fetch("/api/ask", { method: "POST", body: JSON.stringify({ question: "hi" }) });
```

Use `.env.local`:
```env
BACKEND_URL=http://localhost:8000
```
> `BACKEND_URL` is **server-only** (no `NEXT_PUBLIC_`), so it stays secret from the browser.

---

## 9) File upload (Next.js → FastAPI)

### Next.js client upload
```tsx
"use client";
import { useState } from "react";

export default function UploadWidget() {
  const [file, setFile] = useState<File | null>(null);

  async function upload() {
    if (!file) return;
    const base = process.env.NEXT_PUBLIC_API_BASE_URL!;
    const form = new FormData();
    form.append("file", file);

    const res = await fetch(`${base}/upload`, { method: "POST", body: form });
    const data = await res.json();
    console.log(data);
  }

  return (
    <div>
      <input type="file" accept="application/pdf" onChange={(e) => setFile(e.target.files?.[0] ?? null)} />
      <button onClick={upload}>Upload</button>
    </div>
  );
}
```

Important:
- Do **not** manually set `Content-Type` for `FormData` requests.

### FastAPI endpoint
```py
from fastapi import UploadFile, File

@app.post("/upload")
async def upload_pdf(file: UploadFile = File(...)):
    contents = await file.read()
    return {"filename": file.filename, "bytes": len(contents)}
```

---

## 10) Common gotchas (quick fixes)

- **“Hooks can only be used in Client Components”**  
  Add `"use client"` and ensure the component is a Client Component.

- **CORS errors** (when calling FastAPI directly)  
  Enable CORS in FastAPI for `http://localhost:3000`.

- **Env var not updating**  
  Restart `npm run dev` after editing `.env.local`.

- **Wrong `.env.local` location**  
  It must be in the folder containing `package.json`.

- **`localhost` in production**  
  In production, `localhost` refers to the user’s machine. Use environment-specific URLs.

---

## 11) Commands you’ll use most (npm)

From the Next.js project root:
- `npm run dev` — start dev server
- `npm run build` — build for production
- `npm run start` — run production server
- `npm run lint` — lint

Install a library:
```bash
npm install axios
```

---

## 12) Suggested learning path (fast)

1. React: components, props, state, events [[React Components, Props, State, Effects Guide]]
2. Next.js routing + layouts
3. Client vs Server Components
4. Fetching data + error handling
5. Env vars + deployment basics
6. Auth (JWT/cookies) if needed