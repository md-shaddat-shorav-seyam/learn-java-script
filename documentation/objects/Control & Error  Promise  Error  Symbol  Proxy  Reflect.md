Below are the **core “Control & Error” tools** in modern JS projects for: **Promise, Error, Symbol, Proxy, Reflect** — with **real project-style patterns** and **copy-pasteable code**.

---

## 1) Promise (control flow, async pipeline, reliability)

### A) “API call” with timeout + retry (real-world)

```js
// timeout helper
function withTimeout(promise, ms, label = "Timeout") {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(label), ms);

  const wrapped = (async () => {
    try {
      const result = await promise(controller.signal);
      return result;
    } finally {
      clearTimeout(timeoutId);
    }
  })();

  return wrapped;
}

// fetch wrapper
async function fetchJSON(url, { signal } = {}) {
  const res = await fetch(url, { signal });
  if (!res.ok) throw new HttpError(res.status, `HTTP ${res.status} for ${url}`);
  return res.json();
}

// retry helper
async function retry(fn, { times = 3, baseDelayMs = 200 } = {}) {
  let lastErr;
  for (let i = 0; i < times; i++) {
    try {
      return await fn(i);
    } catch (e) {
      lastErr = e;
      const backoff = baseDelayMs * Math.pow(2, i);
      await new Promise(r => setTimeout(r, backoff));
    }
  }
  throw lastErr;
}

// usage
async function loadUser(userId) {
  return retry(() =>
    withTimeout((signal) => fetchJSON(`/api/users/${userId}`, { signal }), 2500)
  );
}
```

### B) Parallel + “best effort” results (Promise.allSettled)

```js
async function loadDashboard() {
  const [user, posts, recs] = await Promise.allSettled([
    fetch("/api/user").then(r => r.json()),
    fetch("/api/posts").then(r => r.json()),
    fetch("/api/recommendations").then(r => r.json()),
  ]);

  return {
    user: user.status === "fulfilled" ? user.value : null,
    posts: posts.status === "fulfilled" ? posts.value : [],
    recs: recs.status === "fulfilled" ? recs.value : [],
    errors: [user, posts, recs]
      .filter(x => x.status === "rejected")
      .map(x => x.reason),
  };
}
```

### C) Concurrency limit (real: uploading many files / crawling URLs)

```js
async function mapLimit(items, limit, mapper) {
  const results = Array(items.length);
  let i = 0;

  const workers = Array.from({ length: limit }, async () => {
    while (i < items.length) {
      const idx = i++;
      results[idx] = await mapper(items[idx], idx);
    }
  });

  await Promise.all(workers);
  return results;
}

// usage
// await mapLimit(files, 3, uploadFile)
```

### D) Promise patterns you’ll use a lot

* `Promise.all()` → **fail fast** (good for “all must succeed”)
* `Promise.allSettled()` → **best effort**
* `Promise.race()` → **first result wins**
* `Promise.any()` → **first success wins** (ignores rejections until all fail)
* `finally()` → cleanup (stop loaders, close DB, clear timers)

---

## 2) Error (custom errors, wrapping, safe boundaries)

### A) Custom error classes (real: APIs & services)

```js
class AppError extends Error {
  constructor(message, { code = "APP_ERROR", cause } = {}) {
    super(message);
    this.name = this.constructor.name;
    this.code = code;
    if (cause) this.cause = cause; // Node 16+ supports Error({cause}) too
  }
}

class HttpError extends AppError {
  constructor(status, message, opts = {}) {
    super(message, { code: "HTTP_ERROR", ...opts });
    this.status = status;
  }
}
```

### B) Wrap errors with context (best practice)

```js
async function parseUserPayload(payload) {
  try {
    const data = JSON.parse(payload);
    if (!data.id) throw new AppError("Missing id", { code: "VALIDATION" });
    return data;
  } catch (e) {
    throw new AppError("Failed to parse user payload", { code: "PARSE", cause: e });
  }
}
```

### C) Error boundary style “safe runner” (use in jobs / UI actions)

```js
async function safeRun(fn, { fallback = null, onError = console.error } = {}) {
  try {
    return await fn();
  } catch (e) {
    onError(e);
    return fallback;
  }
}

// usage
const result = await safeRun(() => loadUser(5), { fallback: { id: 0 } });
```

### D) Global error handling (Node.js)

```js
process.on("unhandledRejection", (reason) => {
  console.error("Unhandled Rejection:", reason);
});

process.on("uncaughtException", (err) => {
  console.error("Uncaught Exception:", err);
  // In production, usually exit + restart (PM2/Docker/K8s)
});
```

---

## 3) Symbol (avoid collisions, hide internals, advanced hooks)

### A) “Private” object keys (real: libs & frameworks)

```js
const _meta = Symbol("meta");

function attachMeta(obj, meta) {
  obj[_meta] = meta;
}

function getMeta(obj) {
  return obj[_meta];
}

const user = { name: "Seyam" };
attachMeta(user, { createdAt: Date.now() });

console.log(Object.keys(user));     // ["name"]  (symbol hidden)
console.log(user[_meta]);           // meta visible only if you have the Symbol
```

### B) Symbol.for / global registry (real: plugins)

```js
// shared across modules
const PLUGIN_KEY = Symbol.for("myapp.plugin.key");

export function registerPlugin(obj, pluginApi) {
  obj[PLUGIN_KEY] = pluginApi;
}
```

### C) Well-known Symbols (real: iteration & customization)

**Make your object iterable** (used by `for...of`, spread)

```js
const range = {
  from: 1,
  to: 5,
  *[Symbol.iterator]() {
    for (let i = this.from; i <= this.to; i++) yield i;
  }
};

console.log([...range]); // [1,2,3,4,5]
```

**Customize string tag** (debugging)

```js
const obj = {
  get [Symbol.toStringTag]() {
    return "CacheStore";
  }
};
console.log(Object.prototype.toString.call(obj)); // [object CacheStore]
```

---

## 4) Proxy (validation, logging, reactive state, access control)

### A) Validate object writes (real: config/state guard)

```js
function createValidatedUser(user) {
  return new Proxy(user, {
    set(target, prop, value) {
      if (prop === "age") {
        if (typeof value !== "number" || value < 0) {
          throw new AppError("Invalid age", { code: "VALIDATION" });
        }
      }
      target[prop] = value;
      return true;
    }
  });
}

const u = createValidatedUser({ name: "Seyam", age: 18 });
u.age = 19;   // ok
// u.age = -1; // throws
```

### B) “Readonly” view (real: prevent mutation bugs)

```js
function readonly(obj) {
  return new Proxy(obj, {
    set() { throw new AppError("Readonly object", { code: "IMMUTABLE" }); },
    deleteProperty() { throw new AppError("Readonly object", { code: "IMMUTABLE" }); }
  });
}
```

### C) Logging + timing (real: debugging service layer)

```js
function wrapService(service) {
  return new Proxy(service, {
    get(target, prop, receiver) {
      const value = Reflect.get(target, prop, receiver);
      if (typeof value !== "function") return value;

      return async (...args) => {
        const start = performance.now();
        try {
          const res = await value.apply(target, args);
          return res;
        } catch (e) {
          throw new AppError(`Service call failed: ${String(prop)}`, { cause: e });
        } finally {
          const ms = performance.now() - start;
          console.log(`[${String(prop)}] ${ms.toFixed(1)}ms`);
        }
      };
    }
  });
}
```

### D) Reactive state mini (real: like Vue reactivity concept)

```js
function reactive(obj, onChange) {
  return new Proxy(obj, {
    set(target, prop, value, receiver) {
      const ok = Reflect.set(target, prop, value, receiver);
      onChange(prop, value);
      return ok;
    }
  });
}

const state = reactive({ count: 0 }, (k, v) => console.log("changed", k, v));
state.count++;
```

> Proxy is powerful, but don’t overuse it in hot code paths (performance & debugging complexity).

---

## 5) Reflect (safer meta-operations, pair with Proxy)

### A) Why Reflect is used in real projects

* returns **boolean** for `set/delete` instead of throwing weirdly
* respects **receiver / prototype** rules
* makes Proxy traps **consistent** and less bug-prone

### B) Proxy + Reflect best-practice template

```js
const handler = {
  get(target, prop, receiver) {
    // add behavior
    return Reflect.get(target, prop, receiver);
  },
  set(target, prop, value, receiver) {
    // validate/log
    return Reflect.set(target, prop, value, receiver);
  },
  has(target, prop) {
    return Reflect.has(target, prop);
  },
  deleteProperty(target, prop) {
    return Reflect.deleteProperty(target, prop);
  },
  ownKeys(target) {
    return Reflect.ownKeys(target);
  }
};

const proxied = new Proxy({ a: 1 }, handler);
```

### C) Dynamic construction (real: factories / DI containers)

```js
class UserService {
  constructor(http, cache) {
    this.http = http;
    this.cache = cache;
  }
}

function make(ClassType, deps) {
  return Reflect.construct(ClassType, deps);
}

const svc = make(UserService, [{ get(){} }, new Map()]);
```

---

## Quick “which one to use” cheat sheet

* **Promise** → async control: parallel, retry, timeouts, concurrency
* **Error** → structured failures: custom errors, wrapping, boundaries
* **Symbol** → hidden keys + no-collision plugin hooks + well-known behaviors
* **Proxy** → intercept reads/writes/calls for validation, readonly, reactivity
* **Reflect** → do meta-operations correctly (especially inside Proxy)

---

If you want, tell me one real project idea you’re building (e.g., “REST API client”, “state manager”, “form validation”, “router”), and I’ll combine these into a **single clean mini-project** that uses all five in a realistic way.
