# Next.js + FastAPI PDF Upload MVP (Implementation Guide)

This guide shows the **minimum working setup** to connect a **Next.js (React)** frontend with a **FastAPI** backend for **uploading a PDF**.

**Goal:** Browser selects a PDF → sends `multipart/form-data` → FastAPI receives the file → reads bytes → returns JSON.

---

## 0) Project Structure

Recommended layout:

```
pdf-assistant/
  backend/
    main.py
    requirements.txt (optional)
  frontend/
    (Next.js app)
```

---

## 1) Backend: FastAPI (Receive and Read Uploaded PDF)

### 1.1 Create a virtual environment + install dependencies

```bash
cd pdf-assistant
mkdir backend
cd backend

python3 -m venv .venv
source .venv/bin/activate

pip install fastapi uvicorn python-multipart
```

> **Important:** `python-multipart` is required to parse `multipart/form-data` uploads.

Optional (if you want to parse PDF text):
```bash
pip install pypdf
```

(Optional) Save dependencies:
```bash
pip freeze > requirements.txt
```

---

### 1.2 Create `backend/main.py` (minimal working)

```python
from fastapi import FastAPI, UploadFile, File
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

# Allow Next.js dev server (CORS)
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/health")
def health():
    return {"status": "ok"}

@app.post("/upload")
async def upload_pdf(file: UploadFile = File(...)):
    contents = await file.read()  # bytes in RAM for this request
    return {
        "filename": file.filename,
        "content_type": file.content_type,
        "bytes": len(contents),
    }
```

---

### 1.3 Run the backend

```bash
uvicorn main:app --reload --port 8000
```

Verify:
- `http://localhost:8000/health`
- `http://localhost:8000/docs`

---

## 2) Frontend: Next.js (Upload UI)

### 2.1 Create a Next.js app

```bash
cd ../
npx create-next-app@latest frontend
cd frontend
npm run dev
```

Open:
- `http://localhost:3000`

---

### 2.2 Configure backend base URL (recommended)

Create `frontend/.env.local`:

```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000
```

Restart Next.js after editing `.env.local`:
```bash
npm run dev
```

---

### 2.3 Implement upload UI (App Router)

Edit `frontend/src/app/page.tsx`:

```tsx
"use client";

import { useState, type ChangeEvent } from "react";

const API_BASE =
  process.env.NEXT_PUBLIC_API_BASE_URL ?? "http://localhost:8000";

export default function Home() {
  const [file, setFile] = useState<File | null>(null);
  const [loading, setLoading] = useState(false);
  const [result, setResult] = useState<any>(null);
  const [error, setError] = useState<string>("");

  const handleFileChange = (e: ChangeEvent<HTMLInputElement>) => {
    setError("");
    setResult(null);
    setFile(e.target.files?.[0] ?? null);
  };

  const handleUpload = async () => {
    if (!file) {
      setError("Please select a PDF file.");
      return;
    }

    setLoading(true);
    setError("");
    setResult(null);

    try {
      const form = new FormData();
      form.append("file", file); // MUST match FastAPI param name: file

      const res = await fetch(`${API_BASE}/upload`, {
        method: "POST",
        body: form,
        // IMPORTANT: do NOT set Content-Type manually for FormData
      });

      if (!res.ok) {
        const text = await res.text();
        throw new Error(text || "Upload failed");
      }

      setResult(await res.json());
    } catch (e: any) {
      setError(e?.message ?? "Unknown error");
    } finally {
      setLoading(false);
    }
  };

  return (
    <main style={{ maxWidth: 720, margin: "40px auto", padding: 20 }}>
      <h1>PDF Upload MVP</h1>

      <input type="file" accept="application/pdf" onChange={handleFileChange} />

      <div style={{ marginTop: 12 }}>
        <button onClick={handleUpload} disabled={!file || loading}>
          {loading ? "Uploading..." : "Upload"}
        </button>
      </div>

      {error && <p style={{ color: "crimson" }}>Error: {error}</p>}

      {result && (
        <pre style={{ marginTop: 16, background: "#f6f6f6", padding: 12 }}>
          {JSON.stringify(result, null, 2)}
        </pre>
      )}
    </main>
  );
}
```

---

## 3) End-to-end verification checklist

1. Start backend:
   ```bash
   cd backend
   source .venv/bin/activate
   uvicorn main:app --reload --port 8000
   ```

2. Start frontend:
   ```bash
   cd frontend
   npm run dev
   ```

3. Open the frontend and upload a PDF.
   - You should see JSON response like:
     - `{ "filename": "...", "bytes": 12345 }`

---

## 4) Common issues (quick fixes)

### A) Backend error: `python-multipart` is required

`python-multipart` is a library FastAPI uses to **parse `multipart/form-data` requests**, which is the standard format browsers use for **file uploads** (via `FormData`)

Fix:
```bash
pip install python-multipart
```

### B) CORS error in browser
Fix in FastAPI:
- Ensure `allow_origins` includes `http://localhost:3000`
- Restart backend after changes

### C) Upload breaks when setting `Content-Type`
Fix:
- For `FormData`, **do NOT set** `Content-Type`. The browser sets it with a boundary.

### D) `.env.local` not taking effect
Fix:
- Restart Next.js (`npm run dev`) after editing env files.

---

## 5) Optional extension: Parse PDF text preview (backend)

If you installed `pypdf`, you can extract a preview:

```python
from io import BytesIO
from pypdf import PdfReader

@app.post("/upload")
async def upload_pdf(file: UploadFile = File(...)):
    contents = await file.read()
    reader = PdfReader(BytesIO(contents))

    preview = ""
    if reader.pages:
        preview = (reader.pages[0].extract_text() or "")[:800]

    return {
        "filename": file.filename,
        "pages": len(reader.pages),
        "text_preview": preview,
    }
```

---

## 6) Next steps

Once upload works, typical next steps are:
- Extract full text
- Summarize
- Store document state (`doc_id`)
- Add `/ask` and `/translate` endpoints

If you tell me your desired response format (e.g., `summary`, `doc_id`, `sources`), I can help you extend this MVP cleanly.
