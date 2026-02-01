# 用 Vue 3 一步步搭建本 Portfolio 的手册

本手册按**从零到当前站点**的顺序，说明如何用 Vue 3（CDN）搭出这个作品集页面。适合边看边对照 `index.html` 和 `js/app.js`。

---

## 目录

1. [项目骨架与依赖](#1-项目骨架与依赖)
2. [挂载 Vue 应用](#2-挂载-vue-应用)
3. [准备数据：profileData](#3-准备数据profiledata)
4. [模板语法：把数据绑到页面](#4-模板语法把数据绑到页面)
5. [响应式状态与事件](#5-响应式状态与事件)
6. [DOM 引用：ref](#6-dom-引用ref)
7. [生命周期与动画：onMounted + GSAP](#7-生命周期与动画onmounted--gsap)
8. [各区块对应关系速查](#8-各区块对应关系速查)

---

## 1. 项目骨架与依赖

### 1.1 最小 HTML 结构

先有一个单页骨架，**整个可交互区域**包在 `<div id="app">` 里，Vue 会挂载到这里：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Alan Wang - Portfolio</title>
</head>
<body>
  <div id="app">
    <!-- 之后所有 Vue 模板都写在这里 -->
  </div>
</body>
</html>
```

### 1.2 引入 Vue 3 和样式、动画库

在 `</head>` 前加：

- **Vue 3**：用 CDN 的「全局构建」，会提供 `window.Vue`
- **Tailwind CSS**：用 CDN，负责布局和工具类
- **GSAP + ScrollTrigger + ScrollToPlugin**：滚动、入场动画、平滑滚动
- **自己的 CSS**：`/css/main.css`

```html
<link rel="stylesheet" href="/css/main.css">
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollToPlugin.min.js"></script>
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

在 `</body>` 前加：

```html
<script src="/js/app.js"></script>
```

这样，页面加载完后再执行 `app.js`，此时 `Vue`、`gsap` 都已存在。

---

## 2. 挂载 Vue 应用

在 `js/app.js` 里，用 **Composition API** 的 `createApp` + `setup` 创建并挂载应用：

```js
const { createApp, ref, onMounted } = Vue;

createApp({
  setup() {
    // 状态、方法、生命周期都写这里
    return {
      // return 出去的才能模板里用
    };
  }
}).mount('#app');
```

- `setup()` 在组件创建时执行，`return` 的对象会暴露给模板。
- `.mount('#app')` 表示把 Vue 实例挂到 `#app` 上，从此 `#app` 内的 HTML 可以用 Vue 语法。

---

## 3. 准备数据：profileData

所有「展示用」的静态数据放在一个对象里，方便维护和替换。本项目中叫 `profileData`：

```js
const profileData = {
  name: "Alan Wang",
  title: "Full-Stack Engineer ...",
  photo: "/assets/photo/photo.jpeg",
  photoPosition: "80% center",
  universityLogos: { northeastern: "...", sydney: "...", macquarie: "..." },
  about: { intro: "...", location: "...", education: "..." },
  projects: [
    { title: "...", tech: "...", gif: "...", description: "...", github: "..." },
    // ...
  ],
  experience: [
    { title: "...", company: "...", companyLogo: "...", date: "...", details: [...] },
    // ...
  ],
  skills: {
    languages: [ { name: "Python", icon: "python" }, ... ],
    frameworks: [ ... ],
    tools: [ ... ]
  },
  contact: {
    links: [ { label: "Gmail", url: "mailto:...", icon: "gmail" }, ... ]
  }
};
```

在 `setup()` 里 `return { profileData }`，模板里就能用 `profileData.xxx`。

---

## 4. 模板语法：把数据绑到页面

所有模板都写在 `#app` 里，使用 Vue 的模板语法。

### 4.1 文本插值 `{{ }}`

把 JS 数据渲染成文字：

```html
<h1>I'M {{ profileData.name }}</h1>
<p>{{ project.title }}</p>
```

### 4.2 属性绑定 `:attr`（或 `v-bind:attr`）

把 Vue 里的值绑定到 HTML 属性上：

```html
<img :src="profileData.photo" alt="Photo">
<a :href="project.github" target="_blank">GitHub</a>
```

`:src` 即 `v-bind:src`，冒号表示「后面是 JS 表达式」。

### 4.3 条件渲染 `v-if` / `v-show`

按条件显示/隐藏：

```html
<img v-if="exp.companyLogo" :src="exp.companyLogo" ...>
<span v-show="showCursor">|</span>
```

- `v-if`：不满足时元素不存在于 DOM。
- `v-show`：始终在 DOM，只切换 `display`。

### 4.4 列表渲染 `v-for`

遍历数组，渲染多份结构：

```html
<div v-for="project in profileData.projects" :key="project.title">
  <h3>{{ project.title }}</h3>
  <img :src="project.gif" :alt="project.title + ' demo'">
</div>
```

```html
<div v-for="exp in profileData.experience" :key="exp.title">
  <h3>{{ exp.title }}</h3>
  <p>{{ exp.company }}</p>
  <li v-for="detail in exp.details" :key="detail" v-html="'• ' + detail"></li>
</div>
```

- `:key` 必须是稳定、唯一的值（如 `title`），便于 Vue 做 diff。
- `v-html` 把字符串当 HTML 渲染，只在可信内容上用。

### 4.5 动态 class `:class`

根据状态切换 class：

```html
<ul class="nav-menu" :class="{ active: mobileMenuOpen }">
```

`mobileMenuOpen` 为 `true` 时，会加上 `active` class。

### 4.6 动态 style `:style`

```html
<img :style="{ objectPosition: profileData.photoPosition || 'center center' }" ...>
```

---

## 5. 响应式状态与事件

### 5.1 用 `ref` 定义响应式状态

在 `setup()` 里：

```js
const mobileMenuOpen = ref(false);   // 移动端菜单开关
const currentTitle = ref("");        // 副标题轮播当前文案
const showCursor = ref(true);        // 是否显示打字光标
```

- 修改时要用 `.value`：`mobileMenuOpen.value = true`
- 模板里不用写 `.value`，直接 `mobileMenuOpen` 即可。

### 5.2 事件处理 `@click`（或 `v-on:click`）

```html
<button @click="toggleMobileMenu">...</button>
<a href="#about" @click="closeMobileMenu">About</a>
```

在 `setup()` 里定义并 return：

```js
const toggleMobileMenu = () => { mobileMenuOpen.value = !mobileMenuOpen.value; };
const closeMobileMenu = () => { mobileMenuOpen.value = false; };
return { toggleMobileMenu, closeMobileMenu, mobileMenuOpen, ... };
```

### 5.3 其他事件：`@error`

图片加载失败时隐藏：

```html
<img :src="..." @error="handleLogoError">
```

```js
const handleLogoError = (e) => { e.target.style.display = 'none'; };
```

---

## 6. DOM 引用：ref

要在 JS 里直接操作某个 DOM（例如做 GSAP 动画），用 `ref`：

### 6.1 模板里声明 `ref="xxx"`

```html
<h1 ref="heroTitle">...</h1>
<img ref="heroImage" ...>
<span ref="subtitleWordRef">...</span>
<span ref="cursorRef" v-show="showCursor">|</span>
```

### 6.2 setup 里创建并 return

```js
const heroTitle = ref(null);
const heroImage = ref(null);
const subtitleWordRef = ref(null);
const cursorRef = ref(null);
// ...
return { heroTitle, heroImage, subtitleWordRef, cursorRef, ... };
```

挂载完成后，`heroTitle.value` 就是对应的 DOM 元素，可传给 `gsap.from(heroTitle.value, { ... })`。

---

## 7. 生命周期与动画：onMounted + GSAP

### 7.1 在 onMounted 里做「页面加载完后的逻辑」

DOM 已经渲染、`ref` 可用了，再注册 GSAP、做动画、绑滚动等：

```js
onMounted(() => {
  gsap.registerPlugin(ScrollTrigger, ScrollToPlugin);

  // 1. Hero 标题入场
  gsap.from(heroTitle.value, { x: -50, opacity: 0, duration: 1, ease: "power4.out" });

  // 2. 头像入场
  gsap.from(heroImage.value, { scale: 0.8, opacity: 0, duration: 1, delay: 0.4, ease: "power3.out" });

  // 3. 副标题轮播（用 subtitleWordRef、cursorRef、currentTitle、titleWords）
  // 4. Skills 标签滚动进入：ScrollTrigger
  // 5. Experience timeline 滚动进入
  // 6. 锚点平滑滚动：ScrollToPlugin
  // 7. 导航高亮：IntersectionObserver 监听 section[id]
});
```

副标题轮播、ScrollTrigger、锚点滚动、导航高亮等，都是在这一步里用「ref + gsap / 原生 DOM」实现的。

### 7.2 本项目中用到的 GSAP 能力

- `gsap.from(el, { ... })`：从某状态动画到当前样式（入场动画）。
- `gsap.to(el, { ... })`：从当前样式动画到目标状态（轮播、滚动等）。
- `ScrollTrigger`：元素进入视口时触发动画（如 skills、experience）。
- `ScrollToPlugin`：点击 `#about`、`#projects` 等锚点时平滑滚动。

---

## 8. 各区块对应关系速查

| 区块 | 数据来源 | 主要 Vue 用法 |
|------|----------|----------------|
| **Header / Nav** | `mobileMenuOpen` | `@click`、`:class="{ active: mobileMenuOpen }"` |
| **Hero** | `profileData.name`、`profileData.photo`、`currentTitle`、`showCursor` | `{{ }}`、`:src`、`:style`、`ref` |
| **About** | 部分写死 + `profileData.universityLogos` | `:src`、`@error` |
| **Projects** | `profileData.projects` | `v-for`、`:src`、`:href`、`:alt` |
| **Experience** | `profileData.experience` | `v-for`、`v-if`、`:src`、`v-html`、`@error` |
| **Skills** | `profileData.skills`（languages / frameworks / tools） | `v-for`、`:src`、`@error`、`ref`（给 GSAP） |
| **Contact** | `profileData.contact.links` | `v-for`、`:href`、`v-if`（按 icon 渲染 Gmail/GitHub/LinkedIn） |

---

## 小结：用 Vue 搭建的流程回顾

1. **HTML 骨架**：`#app` + 引入 Vue、Tailwind、GSAP、自己的 CSS/JS。
2. **createApp + setup + mount**：在 `app.js` 里挂载到 `#app`。
3. **profileData**：集中放展示数据，在 `setup` 里 `return` 出去。
4. **模板**：用 `{{ }}`、`v-for`、`v-if`、`:src`、`@click` 等把数据和交互绑上去。
5. **状态与事件**：用 `ref` 存 `mobileMenuOpen`、`currentTitle`、`showCursor` 等，用 `@click` 调方法更新。
6. **ref 拿 DOM**：给需要做动画的元素加 `ref`，在 `onMounted` 里用 `xxxRef.value` 操作。
7. **onMounted**：注册 GSAP 插件、写入场动画、滚动动画、锚点滚动、导航高亮。

按这个顺序对照 `index.html` 和 `js/app.js`，就能复现「用 Vue 一步步搭成现在这样」的过程。若你要从零重写，也可以按 1→2→3→4→5→6→7 依次实现。
