A practical guide to building a modern frontend with **React** and **Next.js** (focused on Next.js App Router, which is the default for new projects).

---

## 1) What are React and Next.js?

### React
React is a JavaScript library for building **UI components**. You build your app by composing reusable components.
- Component = a function that returns UI
- State = data that can change over time (e.g., form inputs)
- Props = inputs passed from parent to child components

### Next.js
Next.js is a REACT framework built on top of React that adds:
- **Routing** (pages by folder structure)
- **Server-side rendering** (SSR) and **static generation**
- **API routes** (optional backend endpoints)
- **Performance features** (code splitting, image optimization)
- **Production build tooling** out of the box

---

## 2) Prerequisites

You need Node.js installed.

Check versions:
```bash
node -v
npm -v
```

---

## 3) Create a Next.js app

Create a new project:
```bash
npx create-next-app@latest
```

When prompted, common choices:
- TypeScript: Yes (recommended)
- ESLint: Yes
- App Router: Yes (recommended)
- Tailwind: optional (nice for styling)

Go into the folder and run dev server:
```bash
cd your-project
npm run dev
```

Open:
- http://localhost:3000

---

## 4) Key files and folder structure (App Router)

Typical structure:
```
src/
  app/
    layout.tsx
    page.tsx
    globals.css
  components/
    ...
public/
  ...
package.json
next.config.js (optional)
```

### `src/app/page.tsx`
- The home page (`/`)

### `src/app/layout.tsx`
- The root layout wrapping all pages (header/footer, global styles)

### `public/`
- Static assets (images, icons, etc.)

---

## 5) Routing (App Router)

In App Router, folders map to routes.

Example:
```
src/app/
  page.tsx            -> /
  about/page.tsx      -> /about
  dashboard/page.tsx  -> /dashboard
```

Dynamic routes:
```
src/app/users/[id]/page.tsx  -> /users/123
```

In the page component:
```ts
type Props = { params: { id: string } };

export default function UserPage({ params }: Props) {
  return <div>User id: {params.id}</div>;
}
```

---

## 6) Components (React basics)

A basic component:
```tsx
export function Hello({ name }: { name: string }) {
  return <h1>Hello, {name}!</h1>;
}
```

Use it in a page:
```tsx
import { Hello } from "../components/Hello";

export default function Page() {
  return <Hello name="Alan" />;
}
```

---

## 7) Client Components vs Server Components (important)

In Next.js App Router:
- By default, components are **Server Components**
- If you need browser-only features (state, effects, event handlers), mark it as a **Client Component**

### When you need `"use client"`
Use it if you use:
- `useState`, `useEffect`
- click handlers like `onClick`
- direct browser APIs (localStorage, window)

Example client component:
```tsx
"use client";
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

---

## 8) Data fetching patterns

### A) Fetch on the server (recommended when possible)
In Server Components, you can fetch directly:

```tsx
export default async function Page() {
  const res = await fetch("https://example.com/api/data", { cache: "no-store" });
  const data = await res.json();
  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

### B) Fetch on the client (common for interactive UIs)
```tsx
"use client";
import { useEffect, useState } from "react";

export default function Page() {
  const [data, setData] = useState<any>(null);

  useEffect(() => {
    fetch("http://localhost:8000/health")
      .then((r) => r.json())
      .then(setData);
  }, []);

  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

---

## 9) Calling your FastAPI backend (common setup)

If your FastAPI runs on:
- `http://localhost:8000`
and Next.js runs on:
- `http://localhost:3000`

You’ll likely need **CORS** enabled in FastAPI:
- allow origin `http://localhost:3000`

### Recommended: store base URL in env
In `.env.local`:
```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000
```

Then in code:
```tsx
const base = process.env.NEXT_PUBLIC_API_BASE_URL;
const res = await fetch(`${base}/health`);
```

> Anything with `NEXT_PUBLIC_` is visible to the browser. Do not put secrets there.

---

## 10) Forms: upload + ask + translate (pattern)

Client-side form pattern:
```tsx
"use client";
import { useState } from "react";

export default function AskForm() {
  const [question, setQuestion] = useState("");
  const [answer, setAnswer] = useState("");

  async function onSubmit(e: React.FormEvent) {
    e.preventDefault();
    const base = process.env.NEXT_PUBLIC_API_BASE_URL!;
    const res = await fetch(`${base}/ask`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ question }),
    });
    const data = await res.json();
    setAnswer(data.answer);
  }

  return (
    <form onSubmit={onSubmit}>
      <input value={question} onChange={(e) => setQuestion(e.target.value)} />
      <button type="submit">Ask</button>
      <div>{answer}</div>
    </form>
  );
}
```

File upload pattern (FastAPI expects multipart):
```tsx
"use client";
import { useState } from "react";

export default function UploadForm() {
  const [file, setFile] = useState<File | null>(null);
  const [result, setResult] = useState<any>(null);

  async function onUpload() {
    if (!file) return;
    const base = process.env.NEXT_PUBLIC_API_BASE_URL!;
    const form = new FormData();
    form.append("file", file);

    const res = await fetch(`${base}/upload`, { method: "POST", body: form });
    setResult(await res.json());
  }

  return (
    <div>
      <input type="file" onChange={(e) => setFile(e.target.files?.[0] ?? null)} />
      <button onClick={onUpload}>Upload</button>
      <pre>{result ? JSON.stringify(result, null, 2) : ""}</pre>
    </div>
  );
}
```

---

## 11) Styling options

You can style with:
- Plain CSS (`globals.css`)
- CSS Modules (`Component.module.css`)
- Tailwind CSS
- UI libraries (shadcn/ui, MUI, Chakra)

Simple CSS Modules example:
```
components/Button.module.css
```

```css
.btn { padding: 8px 12px; border-radius: 8px; }
```

```tsx
import styles from "./Button.module.css";

export function Button({ children }: { children: React.ReactNode }) {
  return <button className={styles.btn}>{children}</button>;
}
```

---

## 12) Scripts you’ll use most (npm)

From `package.json`:
- `npm run dev` — start dev server
- `npm run build` — production build
- `npm run start` — run production server
- `npm run lint` — lint code

Install a package:
```bash
npm install axios
```

---

## 13) Common mistakes

- Forgetting `"use client"` in components that use state/effects/events
- Putting secrets in `NEXT_PUBLIC_` env vars (never do this)
- CORS not enabled in backend → browser blocks requests
- Using relative URLs incorrectly (decide: proxy vs full URL)
- Mixing server/client data fetching without understanding where code runs

---

## 14) A good “starter” checklist for your Doc Assistant UI

- [ ] Home page UI in `src/app/page.tsx`
- [ ] Client component for upload (FormData)
- [ ] Client component for ask (JSON POST)
- [ ] Client component for translate (JSON POST)
- [ ] `.env.local` for `NEXT_PUBLIC_API_BASE_URL`
- [ ] FastAPI CORS configured for `http://localhost:3000`
- [ ] Loading states + error handling
- [ ] Clean response rendering (answer, sources, progress)

---

## 15) Next steps (recommended learning path)

1. React basics: components, props, state, effects
2. Next.js routing + layouts
3. Client vs server components
4. Fetch patterns + error handling
5. Environment variables + deployment
6. Authentication (JWT/cookies) if needed

---
[[Sample tsx-nextjs]]