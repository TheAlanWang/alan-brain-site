# Components, Props, State, Effects

This guide teaches the core React concepts you’ll use constantly in Next.js and React apps:
- **Components**
- **Props**
- **State**
- **Effects** (`useEffect`)

Examples use modern **function components** and **hooks**.

---

## 1) What is React?

React is a library for building user interfaces by composing small reusable **components**.

Think of a React app as:
- **Data** (state/props) + **UI** (JSX) + **Events** (handlers)
- When data changes, React **re-renders** the UI.

---

## 2) Components (the building blocks)

A **component** is a JavaScript/TypeScript function that returns **JSX**.

### 2.1 A simple component

```tsx
export default function Hello() {
  return <h1>Hello React</h1>;
}
```

### 2.2 Using a component

```tsx
import Hello from "./Hello";

export default function Page() {
  return (
    <div>
      <Hello />
    </div>
  );
}
```

### 2.3 JSX basics

JSX looks like HTML but it’s actually JavaScript expressions:

```tsx
const name = "Alan";
return <p>Hello, {name}</p>;
```

Rules:
- You can embed expressions with `{ ... }`
- You must return **one parent element** (wrap with `<div>` or `<>...</>`)

---

## 3) Props (inputs to a component)

**Props** are values passed from a parent component to a child component.

### 3.1 Define props

```tsx
type GreetingProps = {
  name: string;
};

export function Greeting({ name }: GreetingProps) {
  return <h2>Hello, {name}!</h2>;
}
```

### 3.2 Pass props from parent

```tsx
import { Greeting } from "./Greeting";

export default function Page() {
  return <Greeting name="Alan" />;
}
```

Key ideas:
- Props are **read-only** (don’t mutate props)
- Props make components reusable

---

## 4) State (data that changes over time)

**State** is internal data a component owns and can update.

Use `useState`:

```tsx
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

### 4.1 Common state patterns

#### A) Text input state

```tsx
"use client";
import { useState } from "react";

export default function NameForm() {
  const [name, setName] = useState("");

  return (
    <div>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Type your name"
      />
      <p>You typed: {name}</p>
    </div>
  );
}
```

#### B) Boolean state (loading)

```tsx
const [loading, setLoading] = useState(false);

<button disabled={loading} onClick={async () => {
  setLoading(true);
  // do async work...
  setLoading(false);
}}>
  {loading ? "Loading..." : "Run"}
</button>
```

### 4.2 State update tips

- Updating state triggers a **re-render**
- Never mutate state directly (avoid `state.push(...)` on arrays)
- Use functional updates when needed:

```tsx
setCount((prev) => prev + 1);
```

---

## 5) Effects (useEffect): reacting to changes or running side effects

An **effect** is code that runs after render to do “side effects” like:
- fetching data
- subscribing to events
- timers
- reading browser APIs

### 5.1 Basic useEffect (run once on mount)

```tsx
"use client";
import { useEffect } from "react";

export default function Page() {
  useEffect(() => {
    console.log("Runs once after first render");
  }, []);

  return <div>Check console</div>;
}
```

`[]` means: run once when component mounts.

### 5.2 Fetch data with useEffect

```tsx
"use client";
import { useEffect, useState } from "react";

type Health = { status: string };

export default function HealthCheck() {
  const [data, setData] = useState<Health | null>(null);
  const [error, setError] = useState("");

  useEffect(() => {
    const run = async () => {
      try {
        const res = await fetch("http://localhost:8000/health");
        if (!res.ok) throw new Error("Request failed");
        setData(await res.json());
      } catch (e: any) {
        setError(e?.message ?? "Unknown error");
      }
    };

    run();
  }, []);

  return (
    <div>
      <h3>Backend health</h3>
      {error && <p style={{ color: "crimson" }}>{error}</p>}
      {data ? <pre>{JSON.stringify(data, null, 2)}</pre> : <p>Loading...</p>}
    </div>
  );
}
```

### 5.3 Dependency array: when effects run

```tsx
useEffect(() => {
  // runs when `query` changes
}, [query]);
```

Rules:
- `[]` → run once
- `[x]` → run when `x` changes
- no array → run after every render (usually not what you want)

### 5.4 Cleanup (important)

Use cleanup for subscriptions/timers:

```tsx
useEffect(() => {
  const id = setInterval(() => console.log("tick"), 1000);

  return () => {
    clearInterval(id); // cleanup on unmount
  };
}, []);
```

---

## 6) Putting it all together: a small example

A component that:
- takes **props** (title)
- uses **state** (count)
- runs an **effect** when count changes

```tsx
"use client";
import { useEffect, useState } from "react";

type Props = { title: string };

export default function CounterCard({ title }: Props) {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `${title}: ${count}`;
  }, [title, count]);

  return (
    <div style={{ border: "1px solid #ddd", padding: 12, borderRadius: 8 }}>
      <h2>{title}</h2>
      <button onClick={() => setCount((c) => c + 1)}>+</button>
      <span style={{ marginLeft: 8 }}>{count}</span>
    </div>
  );
}
```

---

## 7) Quick checklist (memorize this)

- **Component**: a function that returns JSX
- **Props**: inputs passed from parent → child (read-only)
- **State**: internal data that can change (`useState`)
- **Effect**: side effects after render (`useEffect`)
- **Re-render** happens when state/props change
- In Next.js App Router, interactive components need `"use client"`

---

## 8) Common beginner mistakes

1. Forgetting `"use client"` in Next.js when using hooks/events
2. Mutating state directly (arrays/objects)
3. Missing `[]` or wrong dependency array in `useEffect`
4. Calling async directly in useEffect (should wrap in function)

Bad:
```tsx
useEffect(async () => { ... }, []);
```

Good:
```tsx
useEffect(() => {
  const run = async () => { ... };
  run();
}, []);
```

---

## 9) Practice tasks

1. Build a `Greeting` component that takes `name` and `age` as props.
2. Build a `Counter` with + and - buttons, and disable when count < 0.
3. Build a `HealthCheck` component that calls `/health` on mount and shows loading/error states.

---

If you want, I can also provide a short cheat sheet version (one page) you can keep next to you while coding.
