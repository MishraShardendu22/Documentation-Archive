# React and Next.js Deep Dive Documentation

### 1. **React Internals**

#### a) Virtual DOM (VDOM) vs Real DOM

* **Virtual DOM**: In-memory JS object representation of the UI.
* **Real DOM**: Browser's actual DOM.
* **Why VDOM is faster**: React diffs VDOM trees and only applies minimal necessary changes to Real DOM.

#### b) Fiber Architecture

* Fiber is React's reconciliation engine.
* Represents **units of work**.
* Supports **update priorities, pausing, and incremental rendering**.
* Each Fiber node contains: type, props, stateNode, child, sibling, return, effectTag.
* **Render Phase**: build workInProgress tree, calculate changes.
* **Commit Phase**: apply changes to real DOM.

#### c) Hooks Internals

* `useState` stores state in Fiber's hook list.
* `useEffect` collects effects in linked list, executed after commit.
* `useLayoutEffect` runs before paint.
* Hooks only work on client-side, not SSR.

#### d) Reconciliation and Optimizations

* **Keys**: identify stable elements in lists.
* **Memoization**: `React.memo`, `useMemo` prevent unnecessary re-renders.
* **Lazy loading**: `React.lazy` for code splitting.

#### e) Summary

* React is a **JS layer orchestrating UI efficiently**.
* VDOM + Fiber = optimized updates.
* Final output is always **HTML, CSS, JS**.
* React shines for **dynamic, interactive apps**.

---

### 2. **Next.js Overview**

#### a) Differences from React

| Feature             | React                 | Next.js            |
| ------------------- | --------------------- | ------------------ |
| Rendering           | CSR by default        | SSR, SSG, ISR, CSR |
| Routing             | Manual (React Router) | File-based routing |
| Server-side code    | Not supported         | API routes         |
| SEO                 | Poor                  | Good with SSR/SSG  |
| Bundle optimization | Manual                | Automatic          |

#### b) Rendering Modes

**Client-Side Rendering (CSR)**

```jsx
useEffect(() => {
  fetch('/api/data').then(res => res.json()).then(setData);
}, []);
```

* Data fetched after page load.

**Server-Side Rendering (SSR)**

```jsx
export async function getServerSideProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  return { props: { data } };
}
```

* HTML generated on each request.
* Excellent SEO.

**Static Site Generation (SSG)**

```jsx
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  return { props: { data } };
}
```

* HTML generated at build time.
* Extremely fast, cached.

**Incremental Static Regeneration (ISR)**

```jsx
export async function getStaticProps() {
  return {
    props: { data },
    revalidate: 60, // regenerate every 60s
  };
}
```

* SSG + dynamic regeneration.

#### c) File-based Routing

```
/pages
  index.js       -> "/"
  about.js       -> "/about"
  blog/[id].js   -> "/blog/:id"
```

#### d) API Routes

```js
export default function handler(req, res) {
  res.status(200).json({ message: 'Hello from server!' });
}
```

* Runs server-side.
* Can access databases or secrets.

---

### 3. **SSR vs SSG**

| Feature         | SSR                | SSG                          |
| --------------- | ------------------ | ---------------------------- |
| HTML generation | Per request        | Build time                   |
| Data freshness  | Always up-to-date  | Fixed (until rebuild)        |
| Performance     | Slower under load  | Fastest, CDN-cached          |
| Use case        | Dynamic dashboards | Blogs, docs, marketing pages |

* **SSR** = dynamic HTML on-demand.
* **SSG** = static HTML pre-built at build time.
* `getServerSideProps` → SSR
* `getStaticProps` → SSG
* SSR does not automatically create SSG.

---

### 4. **SSR + Client-side Components (Partial Hydration)**

* SSR generates initial HTML.
* Client-side React hydrates the page.
* Use `use client` directive for interactive components.

```tsx
// pages/dashboard.tsx
import dynamic from 'next/dynamic';
const UserWidget = dynamic(() => import('../components/UserWidget'), { ssr: false });

export async function getServerSideProps() {
  return { props: { initialData: { welcome: 'Loading...' } } };
}

export default function Dashboard({ initialData }) {
  return (
    <div>
      <h1>{initialData.welcome}</h1>
      <UserWidget />
    </div>
  );
}

// components/UserWidget.tsx
'use client';
import { useEffect, useState } from 'react';
export default function UserWidget() {
  const [user, setUser] = useState(null);
  useEffect(() => {
    fetch('/api/user').then(res => res.json()).then(setUser);
  }, []);
  if (!user) return <p>Loading user...</p>;
  return <p>Welcome {user.name}</p>;
}
```

* Page HTML is SSR-rendered.
* Dynamic part (`UserWidget`) updates **client-side**.

---

### 5. **Netflix Example**

* Landing page performance experiment: temporarily switched from React → vanilla JS in 2017 for static content.
* Now, Netflix uses React for **both landing page and dynamic pages**.
* Rule: **Static pages → vanilla JS (experiment)**, **dynamic/interactive → React**.

**References:**

* [Why Netflix Moved From React to Vanilla JavaScript](https://medium.com/@kruthish18/why-netflix-moved-from-react-to-vanilla-javascript-and-what-it-means-for-developers-2b8eb087d44f)
* [A Netflix Web Performance Case Study](https://medium.com/dev-channel/a-netflix-web-performance-case-study-c0bcde26a9d9)

---

### 6. **Key Takeaways**

1. **React**: VDOM + Fiber = fast, interactive UI. Hooks run **client-side only**.
2. **Next.js**: React + SSR, SSG, ISR, routing, SEO optimizations.
3. **SSR**: HTML generated per request → fresh data, slower under load.
4. **SSG**: HTML generated at build → super fast, cached, static.
5. **Partial hydration**: SSR pages + client-side components = dynamic updates after initial render.
6. **Performance trade-offs**: SSR/SSG improve SEO and load times; React alone shines for highly dynamic apps.
