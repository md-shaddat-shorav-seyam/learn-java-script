Below is a **real-project style** guide for the main **Browser APIs** you listed. I’ll show the **most used methods/properties**, then **small, copy-paste patterns** you’ll actually use in production (not “theory only”).

---

## 1) `fetch` (Network API)

### Core methods/options you’ll use

* `fetch(url, options)`
* `Response`: `ok`, `status`, `headers`, `json()`, `text()`, `blob()`, `arrayBuffer()`
* `Headers`: `get()`, `set()`
* `AbortController` to cancel requests
* `credentials` for cookies: `"include"` / `"same-origin"`
* `cache`, `mode`, `redirect`, `referrerPolicy` (occasionally)

### Real project patterns

#### A) “Safe JSON fetch” wrapper (handles errors + timeouts)

```js
export async function fetchJSON(url, {
  method = "GET",
  headers = {},
  body,
  timeoutMs = 15000,
  credentials = "same-origin",
} = {}) {
  const controller = new AbortController();
  const timer = setTimeout(() => controller.abort(), timeoutMs);

  try {
    const res = await fetch(url, {
      method,
      headers: {
        "Accept": "application/json",
        ...(body ? { "Content-Type": "application/json" } : {}),
        ...headers,
      },
      body: body ? JSON.stringify(body) : undefined,
      credentials,
      signal: controller.signal,
    });

    // Try to parse error body too
    const contentType = res.headers.get("content-type") || "";
    const isJSON = contentType.includes("application/json");
    const data = isJSON ? await res.json() : await res.text();

    if (!res.ok) {
      const msg = typeof data === "string" ? data : (data?.message || "Request failed");
      const err = new Error(msg);
      err.status = res.status;
      err.data = data;
      throw err;
    }

    return data;
  } finally {
    clearTimeout(timer);
  }
}
```

Usage:

```js
import { fetchJSON } from "./api.js";

const user = await fetchJSON("/api/me");
await fetchJSON("/api/profile", { method: "PATCH", body: { name: "Seyam" } });
```

#### B) Upload a file (FormData)

```js
async function uploadAvatar(file) {
  const fd = new FormData();
  fd.append("avatar", file);

  const res = await fetch("/api/avatar", {
    method: "POST",
    body: fd,           // do NOT set Content-Type manually
    credentials: "include"
  });

  if (!res.ok) throw new Error("Upload failed");
  return res.json();
}
```

#### C) Cancel request on route change / component unmount

```js
const controller = new AbortController();

fetch("/api/search?q=react", { signal: controller.signal })
  .then(r => r.json())
  .then(console.log)
  .catch(err => {
    if (err.name === "AbortError") return;
    console.error(err);
  });

// later:
controller.abort();
```

#### D) Download file (Blob → link)

```js
async function downloadReport() {
  const res = await fetch("/api/report.pdf");
  if (!res.ok) throw new Error("Failed");
  const blob = await res.blob();

  const a = document.createElement("a");
  a.href = URL.createObjectURL(blob);
  a.download = "report.pdf";
  document.body.appendChild(a);
  a.click();
  a.remove();

  URL.revokeObjectURL(a.href);
}
```

---

## 2) `localStorage` (Web Storage API)

### Core methods

* `localStorage.getItem(key)`
* `localStorage.setItem(key, value)`
* `localStorage.removeItem(key)`
* `localStorage.clear()`
* `localStorage.key(index)`
* `localStorage.length`
* `storage` event (cross-tab sync)

> Only stores **strings** → JSON encode/decode.

### Real project patterns

#### A) JSON helper (safe parse)

```js
export const storage = {
  get(key, fallback = null) {
    try {
      const raw = localStorage.getItem(key);
      return raw === null ? fallback : JSON.parse(raw);
    } catch {
      return fallback;
    }
  },
  set(key, value) {
    localStorage.setItem(key, JSON.stringify(value));
  },
  remove(key) {
    localStorage.removeItem(key);
  }
};
```

Usage:

```js
storage.set("theme", { mode: "dark" });
const theme = storage.get("theme", { mode: "light" });
```

#### B) Persist UI state (sidebar open/close)

```js
const KEY = "sidebar_open";

function setSidebar(open) {
  document.body.classList.toggle("sidebar-open", open);
  localStorage.setItem(KEY, open ? "1" : "0");
}

setSidebar(localStorage.getItem(KEY) === "1");
```

#### C) Cross-tab logout (sync)

```js
// tab A triggers logout:
localStorage.setItem("logout", String(Date.now()));

// all tabs listen:
window.addEventListener("storage", (e) => {
  if (e.key === "logout") {
    // redirect to login, clear UI, etc.
    location.href = "/login";
  }
});
```

#### D) Simple “expiry” cache

```js
function cacheSet(key, value, ttlMs) {
  localStorage.setItem(key, JSON.stringify({
    value,
    expiresAt: Date.now() + ttlMs
  }));
}

function cacheGet(key) {
  const raw = localStorage.getItem(key);
  if (!raw) return null;
  try {
    const { value, expiresAt } = JSON.parse(raw);
    if (Date.now() > expiresAt) {
      localStorage.removeItem(key);
      return null;
    }
    return value;
  } catch {
    return null;
  }
}
```

---

## 3) `document` (DOM API)

### Most-used methods/properties

**Selecting**

* `document.querySelector()`, `querySelectorAll()`
* `getElementById()`, `getElementsByClassName()` (less modern)

**Create/Update**

* `createElement()`, `createTextNode()`
* `append()`, `appendChild()`, `prepend()`, `remove()`, `replaceWith()`
* `classList.add/remove/toggle/contains`
* `setAttribute/getAttribute/removeAttribute`
* `textContent`, `innerHTML` (careful with XSS), `style`

**Events**

* `addEventListener()`, event delegation
* `DOMContentLoaded`

**Forms**

* `forms`, `FormData`, `input.value`, `checkValidity()`

### Real project patterns

#### A) Event delegation (fast + works for dynamic elements)

```js
document.addEventListener("click", (e) => {
  const btn = e.target.closest("[data-action]");
  if (!btn) return;

  const action = btn.dataset.action;
  if (action === "delete") {
    const id = btn.dataset.id;
    console.log("delete item", id);
  }
});
```

HTML:

```html
<button data-action="delete" data-id="42">Delete</button>
```

#### B) Build DOM safely (avoid innerHTML)

```js
function renderUserCard(user) {
  const card = document.createElement("div");
  card.className = "user-card";

  const name = document.createElement("h3");
  name.textContent = user.name;

  const email = document.createElement("p");
  email.textContent = user.email;

  card.append(name, email);
  return card;
}

document.querySelector("#users").append(renderUserCard({ name: "Seyam", email: "x@y.com" }));
```

#### C) Modal open/close (accessible-ish)

```js
const modal = document.querySelector("#modal");
const openBtn = document.querySelector("#openModal");
const closeBtn = document.querySelector("#closeModal");

function openModal() {
  modal.removeAttribute("hidden");
  modal.setAttribute("aria-hidden", "false");
  closeBtn.focus();
}

function closeModal() {
  modal.setAttribute("hidden", "");
  modal.setAttribute("aria-hidden", "true");
  openBtn.focus();
}

openBtn.addEventListener("click", openModal);
closeBtn.addEventListener("click", closeModal);

document.addEventListener("keydown", (e) => {
  if (e.key === "Escape" && !modal.hasAttribute("hidden")) closeModal();
});
```

#### D) Form submit → fetch

```js
document.querySelector("#profileForm").addEventListener("submit", async (e) => {
  e.preventDefault();
  const form = e.currentTarget;

  const fd = new FormData(form);
  const payload = Object.fromEntries(fd.entries());

  const res = await fetch("/api/profile", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(payload),
  });

  if (!res.ok) alert("Failed");
  else alert("Saved!");
});
```

---

## 4) `window` (Browser Environment API)

### Most-used methods/properties

* `window.location` (`href`, `assign()`, `replace()`, `reload()`)
* `window.history` (`pushState()`, `replaceState()`, `back()`)
* `setTimeout`, `setInterval`, `requestAnimationFrame`
* `matchMedia` (dark mode / responsive)
* `addEventListener('resize' | 'scroll' | 'popstate' | 'online' | 'offline')`
* `postMessage` (iframe communication)
* `navigator` (info like `onLine`, `clipboard` lives under `navigator.clipboard`)

### Real project patterns

#### A) Basic SPA routing (history API)

```js
function navigate(path) {
  history.pushState({}, "", path);
  renderRoute();
}

function renderRoute() {
  const app = document.querySelector("#app");
  const path = location.pathname;

  if (path === "/") app.textContent = "Home";
  else if (path === "/about") app.textContent = "About";
  else app.textContent = "404";
}

window.addEventListener("popstate", renderRoute);
renderRoute();

// usage: navigate("/about")
```

#### B) Debounced resize (performance)

```js
function debounce(fn, wait = 200) {
  let t;
  return (...args) => {
    clearTimeout(t);
    t = setTimeout(() => fn(...args), wait);
  };
}

window.addEventListener("resize", debounce(() => {
  console.log("resized:", window.innerWidth, window.innerHeight);
}, 250));
```

#### C) Dark mode detection

```js
const media = window.matchMedia("(prefers-color-scheme: dark)");

function applyTheme() {
  document.documentElement.classList.toggle("dark", media.matches);
}
media.addEventListener("change", applyTheme);
applyTheme();
```

#### D) Scroll progress bar

```js
window.addEventListener("scroll", () => {
  const h = document.documentElement;
  const progress = (h.scrollTop / (h.scrollHeight - h.clientHeight)) * 100;
  document.querySelector("#bar").style.width = `${progress}%`;
}, { passive: true });
```

---

## 5) `console` (Debug API)

### Most-used methods

* `console.log()`, `info()`, `warn()`, `error()`
* `console.table()`
* `console.group()`, `groupCollapsed()`, `groupEnd()`
* `console.time()`, `timeEnd()`
* `console.assert()`
* `console.trace()`
* `console.clear()` (use carefully)
* `console.count()`, `countReset()`

### Real project patterns

#### A) Structured logs with groups

```js
function logRequest(url, options, result) {
  console.groupCollapsed("API:", url);
  console.log("options:", options);
  console.log("result:", result);
  console.groupEnd();
}
```

#### B) Performance timing

```js
console.time("render");
doHeavyUIWork();
console.timeEnd("render");
```

#### C) Table for debugging lists

```js
console.table([
  { id: 1, name: "A", ok: true },
  { id: 2, name: "B", ok: false },
]);
```

#### D) Assertions (catch impossible states)

```js
function setAge(age) {
  console.assert(Number.isFinite(age) && age > 0, "Age must be a positive number");
}
```

---

## A mini “real project” example using all 5 together

**Goal:** Load products, cache them in localStorage, render them in DOM, handle navigation, log debugging.

```js
// fetch wrapper
async function fetchJSON(url) {
  const res = await fetch(url, { headers: { "Accept": "application/json" }});
  const data = await res.json().catch(() => null);
  if (!res.ok) throw new Error(data?.message || `HTTP ${res.status}`);
  return data;
}

function cacheSet(key, value) {
  localStorage.setItem(key, JSON.stringify({ value, t: Date.now() }));
}
function cacheGet(key, maxAgeMs) {
  const raw = localStorage.getItem(key);
  if (!raw) return null;
  try {
    const { value, t } = JSON.parse(raw);
    if (Date.now() - t > maxAgeMs) return null;
    return value;
  } catch { return null; }
}

function renderProducts(list) {
  const root = document.querySelector("#app");
  root.innerHTML = ""; // OK if you control this container
  for (const p of list) {
    const div = document.createElement("div");
    div.className = "product";
    div.innerHTML = `<strong>${p.name}</strong> <button data-id="${p.id}">View</button>`;
    root.append(div);
  }
}

async function loadProducts() {
  console.time("loadProducts");

  const cached = cacheGet("products", 60_000);
  if (cached) {
    console.info("Using cache");
    renderProducts(cached);
    console.timeEnd("loadProducts");
    return;
  }

  const data = await fetchJSON("/api/products");
  cacheSet("products", data);
  renderProducts(data);

  console.timeEnd("loadProducts");
}

document.addEventListener("click", (e) => {
  const btn = e.target.closest("button[data-id]");
  if (!btn) return;

  const id = btn.dataset.id;
  history.pushState({}, "", `/product/${id}`);
  console.log("Navigated to", location.pathname);
});

window.addEventListener("popstate", () => {
  console.log("Back/forward:", location.pathname);
});

loadProducts().catch(err => console.error(err));
```

---

If you want, tell me **your project type** (portfolio website / e-commerce / admin dashboard / SPA) and I’ll give you a **clean folder structure** + the **top 20 browser APIs** you should learn next (like `IntersectionObserver`, `Clipboard`, `Notification`, `WebSocket`, `Service Worker`, `IndexedDB`, etc.) with real project code.
