# Next.js + React Learning Checklist (DocuBot Track)
## App Router First + API-First (Minimal Contract)

This checklist is optimized for a **FastAPI backend + Next.js App Router frontend** workflow:
- **API-first (minimal contract)** so the frontend never “guesses” the backend
- **App Router first** for routing/layout/loading/error structure
- **Server-first, client-minimal** for a clean Next.js architecture

---

## How to Use This Checklist

- Work **top-to-bottom**.
- Each checkbox should end with something that **runs**.
- Keep **pages/layouts as Server Components** when possible.
- Put interactivity (forms, buttons, hooks) into **small Client Components**.

---

## 0) One-Time Setup (Foundation)

### 0.1 Install Tooling
- [ ] Install **Node.js (LTS)**
- [ ] Choose a package manager: `npm` or `pnpm`
- [ ] Install Git + set up GitHub (repo + commit workflow)

### 0.2 Create a Next.js App (App Router + TypeScript)
- [ ] Create the app: `npx create-next-app@latest`
- [ ] Choose these options:
  - TypeScript ✅
  - App Router ✅
  - ESLint ✅
  - Tailwind (optional, recommended) ✅
- [ ] Run `npm run dev` and confirm editing `app/page.tsx` updates the homepage

### 0.3 Suggested Project Structure
- [ ] Create these folders (recommended):
  - `app/` — routes + layouts
  - `components/` — shared UI components
  - `lib/` — API client + helpers
  - `types/` — shared TypeScript types
- [ ] Create `components/Button.tsx` and render it on the homepage

---

## 1) API-First (Minimal Contract) — Do This Early

> Goal: lock a **small, clear contract** so you don’t rewrite UI later.

### 1.1 Define the Minimal Endpoints (DocuBot Contract)
- [ ] Confirm the backend endpoints & responses (contract)

| Endpoint | Request | Response |
|---|---|---|
| `GET /health` | — | `{ status: "ok" }` |
| `POST /upload` | `multipart/form-data` (PDF) | `{ summary: string }` |
| `POST /translate` | `application/json` **or** `multipart/form-data` | `{ translation: string }` |
| `POST /ask` | `application/json` | `{ answer: string }` |

- [ ] Pick **one** consistent error format
  - Option A (FastAPI default): `{ detail: string }`
  - Option B (custom): `{ error: string }`
- [ ] Decide base URL via env:
  - `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`

### 1.2 Implement the “Contract Layer” in Next.js
- [ ] Create `types/api.ts` (response types)
  - `UploadResponse`, `TranslateResponse`, `AskResponse`
- [ ] Create `lib/api.ts` (API client functions)
  - `uploadPdf(file)`
  - `translate...(...)`
  - `askQuestion(...)`
- [ ] Quick smoke test
  - Call `GET /health` from the frontend and render `"ok"` somewhere in the UI

### 1.3 CORS / Proxy Decision (Avoid Getting Stuck)
- [ ] Choose one approach (either is fine):
  - **A) Enable CORS in FastAPI** for `http://localhost:3000`
  - **B) Proxy via Next.js API routes** (optional) [[nextjs-proxy Guide]]
- [ ] Confirm `POST /upload` can be called from the Next.js UI without CORS errors

---

## 2) React Fundamentals (Must-Have)

### 2.1 Components and JSX
- [ ] Create a basic component: `function Hello() { return <div>Hello</div>; }`
- [ ] JSX basics:
  - Expressions with `{ }`
  - Conditional rendering (`? :`, `&&`)
  - Lists with `.map()`
- [ ] Exercise: build `<UserCard />` (name + role + badge)

### 2.2 Props + Children
- [ ] Pass props: `<UserCard name="Alan" role="Student" />`
- [ ] Type props in TS: `type Props = { name: string; role: string }`
- [ ] Use `children` in a reusable `<Card />`
- [ ] Exercise: create `<Card />` and reuse it in 2 places

### 2.3 State + Forms
- [ ] `useState` + controlled inputs
- [ ] Exercise: NameForm (input → live preview)

### 2.4 Events
- [ ] `onClick`, `onChange`
- [ ] Exercise: Counter with min/max

### 2.5 Effects
- [ ] `useEffect` + dependency array
- [ ] Exercise: localStorage save/load (e.g., last selected language)

### 2.6 Lifting State Up
- [ ] Parent owns shared state; children get props + callbacks
- [ ] Exercise: messages list (add message + render list)

---

## 3) Next.js App Router Core Concepts (Structure First)

### 3.1 Routing
- [ ] Create a route: `app/about/page.tsx` → `/about`
- [ ] Create a dynamic route: `app/docs/[id]/page.tsx`
- [ ] Use route groups to organize without changing URL:
  - `app/(marketing)/...`
  - `app/(app)/...`
- [ ] Exercise: `/docs` list + `/docs/[id]` detail page

### 3.2 Layouts and Shared UI
- [ ] Root layout: `app/layout.tsx` wraps everything
- [ ] Nested layout: `app/(app)/layout.tsx` wraps app pages only
- [ ] Shared UI examples: navbar, footer, sidebar, providers
- [ ] Exercise: navbar in root + sidebar only for `/dashboard/*`

### 3.3 Server vs Client Components (Critical)
- [ ] Default is Server Component (no `"use client"`)
- [ ] Add `"use client"` only when you need:
  - hooks (`useState`, `useEffect`)
  - event handlers (`onClick`, `onChange`)
  - browser APIs (`window`, `localStorage`)
- [ ] Exercise: Server page renders a client `UploadForm`

### 3.4 Navigation
- [ ] Use `<Link href="/x">`
- [ ] Use `useRouter()` in client components
- [ ] Exercise: active link highlighting

### 3.5 Loading / Error / Not Found
- [ ] Add `loading.tsx`, `error.tsx`, `not-found.tsx` where useful
- [ ] Exercise: simulate a slow request and confirm `loading.tsx` shows

### 3.6 Metadata
- [ ] Set global + per-page metadata
- [ ] Exercise: different title per route

---

## 4) DocuBot MVP (Best Learning ROI)

> Build features in the order that gives the fastest **end-to-end** progress.

### 4.1 MVP v0.1 — Upload → Summary
- [ ] Page `/` with two panels:
  - Upload panel
  - Summary result panel
- [ ] Client component: `UploadForm`
  - file input + submit button
  - calls `uploadPdf()` from `lib/api.ts`
- [ ] UI states: `idle / loading / success / error`
- [ ] Exercise: upload a real PDF and show the summary

### 4.2 MVP v0.2 — Translate
- [ ] Route `/translate`
- [ ] UI: language selector + translate button
- [ ] Calls translate API client function
- [ ] Exercise: show translation + copy-to-clipboard

### 4.3 MVP v0.3 — Q&A
- [ ] Route `/ask`
- [ ] UI: question input + answer output
- [ ] Save conversation history in state
- [ ] Exercise: allow follow-up questions; render messages list

---

## 5) Styling & UI (Choose One Track)

- [ ] Pick one: Tailwind **or** CSS Modules **or** a UI kit
- [ ] Exercise: responsive layout (stack on mobile, split on desktop)

---

## 6) TypeScript (Just Enough, Early)

- [ ] Prop types
- [ ] API response types (from `types/api.ts`)
- [ ] UI state union type: `"idle" | "loading" | "success" | "error"`

---

## 7) Optional: Next.js API Routes (Only If You Need Proxying)

- [ ] Add `app/api/health/route.ts` as a proxy (optional)
- [ ] Use when you want to avoid CORS or hide backend URL

---

## 8) Deployment Basics

- [ ] Use `.env.local` for dev
- [ ] Only expose safe values with `NEXT_PUBLIC_...`
- [ ] Run `npm run build`
- [ ] Deploy frontend (Vercel) + backend (your platform)

---

## Common Pitfalls

- Everything became a Client Component → keep pages/layouts server; only interactive blocks client
- CORS errors → enable CORS in FastAPI or use proxy
- Env vars not working → restart dev server; check `.env.local`; verify `NEXT_PUBLIC_`
- Infinite fetch loops → check `useEffect` dependencies

---

_Last updated: 2026-01-19_
