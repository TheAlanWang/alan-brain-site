Vue is a **progressive JavaScript framework** for building user interfaces: you can start with a simple page and gradually add components, routing, state management, etc., without needing the full stack from the beginning.

---

## 1. What is Vue

- **Declarative**: Use templates to describe "what the interface looks like" and data to describe "current state"; when data changes, the interface updates automatically.
- **Reactive**: When you modify values in `ref` / `reactive`, the DOM that depends on these values will automatically re-render, without manually writing `document.querySelector` to make changes.
- **Component-based**: Break pages into reusable "components" (this portfolio uses CDN single-page, components not yet split, but Vue's core concepts remain the same).

This project uses **Vue 3**, introduced via CDN, using the **Composition API** (`setup`, `ref`, `onMounted`, etc.) to write logic.

---

## 2. Minimal Example

```html
<div id="app">
  <p>{{ message }}</p>
  <button @click="count++">Clicked {{ count }} times</button>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const { createApp, ref } = Vue;
  createApp({
    setup() {
      const message = ref('Hello Vue');
      const count = ref(0);
      return { message, count };
    }
  }).mount('#app');
</script>
```

- `ref`: Defines reactive data; use `.value` to modify, not needed in templates.
- `{{ message }}`: Text interpolation, displays the value of `message`.
- `@click="count++"`: Executes `count++` on click; after the number changes, the "Clicked x times" on the button will automatically update.

---

## 3. Core Concepts Overview

### 3.1 Reactive Data: `ref` and `reactive`

**`ref`**: Suitable for single values (numbers, strings, booleans, etc.).

```js
const count = ref(0);
count.value++;        // Modify
console.log(count.value);  // Read (need .value in JS)
```

In templates, use `count` directly, no need for `.value`.

**`reactive`**: Suitable for objects, the entire object is reactive.

```js
const state = reactive({ name: 'Alan', age: 25 });
state.name = 'Bob';   // Directly modify properties
```

This portfolio mainly uses `ref`, such as `mobileMenuOpen`, `currentTitle`, `profileData`, etc.

---

### 3.2 Template Syntax

| Usage | Description | Example |
|------|------|------|
| `{{ }}` | Text interpolation, displays JS expression value | `{{ profileData.name }}` |
| `v-bind` / `:` | Bind attributes | `:src="photo"`, `:href="url"` |
| `v-on` / `@` | Bind events | `@click="handleClick"`, `@submit="onSubmit"` |
| `v-if` / `v-else` / `v-show` | Conditional display | `v-if="isOK"`, `v-show="visible"` |
| `v-for` | Loop render list | `v-for="item in list" :key="item.id"` |
| `v-model` | Two-way binding (commonly used for forms) | `v-model="inputText"` |

- `v-if`: When false, element is removed from DOM.
- `v-show`: Always in DOM, only toggles `display: none`.

---

### 3.3 Exposing Data and Methods to Templates

In `setup()`, only content returned can be used in templates:

```js
setup() {
  const count = ref(0);
  const add = () => { count.value++; };
  return { count, add };   // Template can use count, add
}
```

```html
<button @click="add">Add One</button>
<p>{{ count }}</p>
```

---

### 3.4 Lifecycle Hooks

In `setup`, use `onXxx` to register lifecycle callbacks, common ones:

| Hook | Timing |
|------|------|
| `onMounted` | Component mounted, DOM rendered, can access elements via `ref` |
| `onUnmounted` | Component about to unmount, cleanup (e.g., unbind events, cancel timers) |

```js
import { onMounted, onUnmounted } from 'Vue';

setup() {
  onMounted(() => {
    console.log('Mounted, can run GSAP, bind scroll, etc.');
  });
  onUnmounted(() => {
    console.log('About to unmount, cleanup');
  });
}
```

This portfolio uses `onMounted` for GSAP animations, ScrollTrigger, anchor scrolling, navigation highlighting, etc.

---

### 3.5 Getting DOM: `ref` References

Add `ref="xxx"` to elements in templates, use `ref(null)` in `setup` to receive, after mounting `.value` is the real DOM:

```html
<h1 ref="title">Title</h1>
```

```js
const title = ref(null);
onMounted(() => {
  console.log(title.value);  // <h1>...</h1>
  gsap.from(title.value, { opacity: 0 });
});
return { title };
```

---

## 4. Common Directives Summary

| Directive | Purpose |
|------|------|
| `v-if` / `v-else-if` / `v-else` | Conditional rendering |
| `v-show` | Conditional show/hide (change `display`) |
| `v-for="x in list"` | Iterate list to render |
| `v-bind:attr` / `:attr` | Bind attributes |
| `v-on:event` / `@event` | Bind events |
| `v-model` | Two-way binding (commonly used for input, textarea, select) |
| `v-html` | Render string as HTML (only for trusted content) |
| `v-pre` | Don't compile, output as-is |
| `v-once` | Render once, don't update afterwards |

---

## 5. Relationship with This Project

- **Data**: `profileData`, `currentTitle`, `mobileMenuOpen`, etc. managed with `ref`.
- **Templates**: In `index.html` under `#app`, `{{ }}`, `v-for`, `v-if`, `:src`, `@click`, etc.
- **Logic**: In `js/app.js`, `createApp`, `setup`, `return`, `onMounted`.
- **Animations & DOM**: In `onMounted`, use DOM obtained via `ref` for GSAP, ScrollTrigger, etc.

Master the above "data + templates + events + lifecycle + ref", and you'll have enough to understand and modify the current portfolio; for deeper learning, study **components**, **routing**, **Pinia/Vuex**, etc.

---

## 6. Further Reading

- Official Documentation (Chinese): [https://cn.vuejs.org/](https://cn.vuejs.org/)
- This Repository: [VUE Build Manual](./VUE_BUILD_MANUAL.md) — How to build this portfolio step by step with Vue
