# Writing TSX in Next.js (App Router) — A Practical Guide

This guide teaches you how to write TSX like the example you shared: a **Next.js page** that uses **React state**, **event handlers**, and **fetch** to talk to a backend.

---

## 1) What “TSX” is

- **TS** = TypeScript (typed JavaScript)
- **X** = JSX (HTML-like syntax inside JS)

So **TSX** means: *TypeScript + JSX together*, commonly used in React/Next.js files like `page.tsx` or `Component.tsx`.

---

## 2) The standard TSX file structure (mental template)

A typical Next.js + React TSX file looks like:

1. **Client/Server directive** (optional): `"use client";`
2. **Imports**
3. **Constants / config** (optional)
4. **Component function**
   - State hooks (`useState`)
   - Handlers (functions)
   - Return JSX (UI)

---

## 3) When you need `"use client"`

In Next.js App Router, components are **Server Components by default**.

Add `"use client"` if you use any of these:
- `useState`, `useEffect`
- event handlers: `onClick`, `onChange`, etc.
- browser-only APIs: `window`, `localStorage`

Example:

```tsx
"use client";
import { useState } from "react";

export default function Page() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

---

## 4) Imports (React hooks + TypeScript types)

A clean pattern:

```tsx
import { useState, type ChangeEvent } from "react";
```

- `useState` is a hook
- `ChangeEvent` is a **TypeScript type**
- `type` keyword helps TS treat it as type-only import

---

## 5) Config constants (backend URL pattern)

Instead of hard-coding `http://localhost:8000`, store your backend base URL in `.env.local`.

**frontend/.env.local** (must be next to `package.json`):
```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000
```

Then in TSX:

```tsx
const API_BASE =
  process.env.NEXT_PUBLIC_API_BASE_URL ?? "http://localhost:8000";
```

Notes:
- Anything with `NEXT_PUBLIC_` is visible to the browser.
- Do **not** put secrets here.

---

## 6) State (useState) — how to choose types

Example:

```tsx
const [file, setFile] = useState<File | null>(null);
const [loading, setLoading] = useState(false);
const [result, setResult] = useState<any>(null);
const [error, setError] = useState<string>("");
```

### Why these types?
- `File | null`: file input can be empty, so allow `null`
- `boolean`: loading flags
- `string`: error message
- `any`: quick MVP for backend response (fine for prototype)

### Better than `any` (recommended)
Define a response type:

```ts
type UploadResponse = {
  filename: string;
  bytes?: number;
  pages?: number;
  text_preview?: string;
};
```

Then:

```tsx
const [result, setResult] = useState<UploadResponse | null>(null);
```

---

## 7) Event handlers (typing them correctly)

### File input handler

```tsx
const handleFileChange = (e: ChangeEvent<HTMLInputElement>) => {
  setError("");
  setResult(null);
  setFile(e.target.files?.[0] ?? null);
};
```

Key idea:
- `e.target.files` can be `null`
- so we use `?.[0]` and `?? null`

---

## 8) Async actions (fetch pattern)

Your upload handler follows a standard pattern:

1. validate input
2. set UI state (loading, clear errors)
3. `try/catch/finally`
4. check `res.ok`
5. parse JSON

### Template you can reuse

```tsx
const handleAction = async () => {
  setLoading(true);
  setError("");
  try {
    const res = await fetch("...");
    if (!res.ok) {
      const text = await res.text();
      throw new Error(text || "Request failed");
    }
    const data = await res.json();
    setResult(data);
  } catch (e: any) {
    setError(e?.message ?? "Unknown error");
  } finally {
    setLoading(false);
  }
};
```

---

## 9) Uploading files with FormData (important rules)

### Build FormData
```tsx
const form = new FormData();
form.append("file", file); // key must match FastAPI param name
```

### Send request
```tsx
const res = await fetch(`${API_BASE}/upload`, {
  method: "POST",
  body: form,
});
```

**Rule:** Do **NOT** manually set `Content-Type` when using FormData.  
The browser automatically sets the correct boundary.

---

## 10) JSX rendering (UI section)

A common structure:

```tsx
return (
  <main>
    {/* title */}
    <h1>...</h1>

    {/* input */}
    <input type="file" onChange={handleFileChange} />

    {/* button */}
    <button onClick={handleUpload} disabled={!file || loading}>
      {loading ? "Uploading..." : "Upload"}
    </button>

    {/* error message */}
    {error && <p style={{ color: "crimson" }}>Error: {error}</p>}

    {/* result */}
    {result && <pre>{JSON.stringify(result, null, 2)}</pre>}
  </main>
);
```

### Conditional rendering patterns
- `condition && <Component />` shows something only when condition is truthy
- Use `disabled` to prevent repeated clicks during `loading`

---

## 11) A “fill-in-the-blank” TSX starter template

Copy/paste and edit:

```tsx
"use client";

import { useState } from "react";

const API_BASE =
  process.env.NEXT_PUBLIC_API_BASE_URL ?? "http://localhost:8000";

type ApiResponse = {
  message?: string;
};

export default function Page() {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string>("");
  const [data, setData] = useState<ApiResponse | null>(null);

  const handleClick = async () => {
    setLoading(true);
    setError("");
    setData(null);

    try {
      const res = await fetch(`${API_BASE}/YOUR_ENDPOINT`, { method: "GET" });
      if (!res.ok) throw new Error("Request failed");
      setData(await res.json());
    } catch (e: any) {
      setError(e?.message ?? "Unknown error");
    } finally {
      setLoading(false);
    }
  };

  return (
    <main style={{ maxWidth: 720, margin: "40px auto", padding: 20 }}>
      <h1>My Page</h1>

      <button onClick={handleClick} disabled={loading}>
        {loading ? "Loading..." : "Run"}
      </button>

      {error && <p style={{ color: "crimson" }}>Error: {error}</p>}

      {data && (
        <pre style={{ marginTop: 16, background: "#f6f6f6", padding: 12 }}>
          {JSON.stringify(data, null, 2)}
        </pre>
      )}
    </main>
  );
}
```

---

## 12) Where to place this file in Next.js (App Router)

- `app/page.tsx` → route `/` (home page)
- `app/upload/page.tsx` → route `/upload`
- `app/components/YourComponent.tsx` → reusable component (import into pages)

Recommended:
- Keep pages small
- Move UI parts into `/components`

---

## 13) Common mistakes (and fixes)

### Mistake 1: “CORS error” in browser
Fix in FastAPI:
- Add CORS middleware allowing `http://localhost:3000`

### Mistake 2: Forgetting to restart Next.js after editing `.env.local`
Fix:
- Stop `npm run dev`
- Run `npm run dev` again

### Mistake 3: Setting `Content-Type` manually for FormData
Fix:
- Remove headers and let the browser set it

### Mistake 4: Using `React.ChangeEvent` without importing `React`
Fix:
- Use `import { type ChangeEvent } from "react";`

---

## 14) Practice exercises (quick)

1. Add a “Clear” button that sets `file` to null and clears `result/error`.
2. Add a client-side check: if file size > 10MB, show error.
3. Replace `any` with a typed response interface.

---

## Next step (recommended)

Once upload works, extend the same TSX structure to:
- `/ask` (JSON POST)
- `/translate` (JSON POST)
- display results in nicer UI cards instead of raw JSON

If you share your FastAPI response schema (what fields you return), I can help you define the exact TypeScript types.
