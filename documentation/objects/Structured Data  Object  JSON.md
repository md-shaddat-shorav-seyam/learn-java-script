Here are the **real-project ways** you’ll use **structured data** in JavaScript with **Objects + JSON** — with **methods + code** you can copy into production.

---

## 1) JavaScript Object essentials (the stuff you use daily)

### Create objects (3 common styles)

```js
// 1) Literal (most common)
const user = { id: 1, name: "Seyam", role: "admin" };

// 2) Object constructor (rare in modern code)
const config = new Object();
config.theme = "dark";

// 3) Factory function (common for reusable creation)
function createUser(id, name) {
  return { id, name, createdAt: new Date().toISOString() };
}
```

### Read / write / delete

```js
const u = { name: "Seyam", age: 18 };

console.log(u.name);     // dot
console.log(u["age"]);   // bracket (dynamic keys)

u.city = "Dhaka";        // add/update
u["age"] = 19;

delete u.city;           // delete property
```

### Optional chaining + nullish coalescing (real project safety)

```js
const profile = {}; 
const city = profile.address?.city ?? "Unknown";
```

---

## 2) Object methods you’ll use in real projects (with examples)

### `Object.keys()`, `Object.values()`, `Object.entries()`

Used for rendering lists, building queries, validation.

```js
const data = { q: "phone", page: 2, sort: "new" };

Object.keys(data);    // ["q","page","sort"]
Object.values(data);  // ["phone",2,"new"]
Object.entries(data); // [["q","phone"],["page",2],["sort","new"]]
```

### `Object.fromEntries()`

Used for converting entries back to object (filters, forms).

```js
const entries = [["q", "laptop"], ["page", "1"]];
const obj = Object.fromEntries(entries);
// { q: "laptop", page: "1" }
```

### `Object.assign()` and spread `{...obj}`

Used for merging state, config overrides.

```js
const base = { theme: "light", lang: "en" };
const override = { theme: "dark" };

const merged1 = Object.assign({}, base, override);
const merged2 = { ...base, ...override }; // preferred
```

### Deep merge (because spread is shallow)

Real projects often need deep merge for configs.

```js
function deepMerge(a, b) {
  const out = { ...a };
  for (const [k, v] of Object.entries(b)) {
    if (v && typeof v === "object" && !Array.isArray(v) && a?.[k] && typeof a[k] === "object") {
      out[k] = deepMerge(a[k], v);
    } else {
      out[k] = v;
    }
  }
  return out;
}
```

### `Object.hasOwn()` (modern) / `hasOwnProperty`

Used to avoid inherited keys issues.

```js
const obj = Object.create({ inherited: 1 });
obj.own = 2;

Object.hasOwn(obj, "own");       // true
Object.hasOwn(obj, "inherited"); // false
```

### `Object.freeze()`, `Object.seal()`

Used for “don’t mutate constants” and preventing accidental changes.

```js
const settings = Object.freeze({ apiUrl: "/api" });
// settings.apiUrl = "x"  // fails (or ignored) in strict mode
```

### `Object.create(proto)`

Used for prototypal inheritance / clean dictionaries (no prototype keys).

```js
const dict = Object.create(null);
dict["__proto__"] = "safe"; // no prototype pollution from this key
```

---

## 3) Destructuring + rest (used in APIs, React, Express, etc.)

```js
const reqBody = { email: "a@b.com", password: "123", role: "user" };

const { password, ...safe } = reqBody; // remove password
// safe -> { email, role }
```

---

## 4) Structured data patterns in real projects

### A) Backend API response shaping (DTO)

```js
function userDTO(user) {
  return {
    id: user.id,
    name: user.name,
    email: user.email,
    createdAt: user.createdAt
  };
}
```

### B) Form handling: turn FormData into object

```js
function formDataToObject(formEl) {
  const fd = new FormData(formEl);
  return Object.fromEntries(fd.entries());
}
```

### C) Query string from object

```js
function toQueryString(params) {
  const usp = new URLSearchParams();
  for (const [k, v] of Object.entries(params)) {
    if (v === undefined || v === null) continue;
    usp.set(k, String(v));
  }
  return usp.toString();
}

// /search?q=phone&page=2
```

### D) Map array → object by id (fast lookup)

```js
const users = [{ id: 1, name: "A" }, { id: 2, name: "B" }];

const byId = Object.fromEntries(users.map(u => [u.id, u]));
// byId[2] => {id:2,name:"B"}
```

---

## 5) JSON in JS (real usage)

### JSON basics

* **Object** is JS runtime structure
* **JSON** is a **string format** used to send/store data

### `JSON.stringify()` (object → JSON string)

Used for localStorage, logging, sending to server.

```js
const obj = { id: 1, tags: ["a", "b"] };
const json = JSON.stringify(obj);
```

### `JSON.parse()` (JSON string → object)

Used when reading from API/localStorage.

```js
const obj2 = JSON.parse(json);
```

### Pretty printing JSON (debugging)

```js
console.log(JSON.stringify({ a: 1, b: 2 }, null, 2));
```

### Handle parse errors (production-safe)

```js
function safeJsonParse(str) {
  try { return { ok: true, value: JSON.parse(str) }; }
  catch (e) { return { ok: false, error: e.message }; }
}
```

### Custom stringify behavior (replacer)

Useful to remove secrets, drop undefined, etc.

```js
const payload = { email: "x@y.com", password: "secret" };

const clean = JSON.stringify(payload, (k, v) => {
  if (k === "password") return undefined; // remove
  return v;
});
```

### `toJSON()` for custom serialization

Used when you have Date-like objects or class instances.

```js
class User {
  constructor(id, name) { this.id = id; this.name = name; }
  toJSON() { return { id: this.id, name: this.name }; }
}
console.log(JSON.stringify(new User(1, "Seyam"))); // {"id":1,"name":"Seyam"}
```

---

## 6) Real project examples (copy-paste)

### Example 1: Frontend fetch (send JSON, receive JSON)

```js
async function login(email, password) {
  const res = await fetch("/api/login", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ email, password })
  });

  if (!res.ok) throw new Error("Login failed");
  return await res.json(); // JSON -> object
}
```

### Example 2: localStorage (persist structured data)

```js
function saveSettings(settings) {
  localStorage.setItem("settings", JSON.stringify(settings));
}

function loadSettings() {
  const raw = localStorage.getItem("settings");
  if (!raw) return { theme: "light" };
  try { return JSON.parse(raw); }
  catch { return { theme: "light" }; }
}
```

### Example 3: Express.js backend (parse JSON + respond JSON)

```js
import express from "express";
const app = express();

app.use(express.json());

app.post("/api/user", (req, res) => {
  const { name, email } = req.body;
  if (!name || !email) return res.status(400).json({ error: "Missing fields" });

  res.json({ id: 123, name, email });
});

app.listen(3000);
```

---

## 7) Common mistakes (and the correct way)

### ❌ JSON can’t store functions / undefined / BigInt

```js
JSON.stringify({ a: undefined, b: () => {}, c: BigInt(10) });
// a and b get dropped, BigInt throws error
```

✅ Fix BigInt by converting

```js
JSON.stringify({ c: BigInt(10).toString() });
```

### ❌ Circular objects crash stringify

```js
const a = {};
a.self = a;
JSON.stringify(a); // error
```

✅ Safe stringify for circular

```js
function safeStringify(obj) {
  const seen = new WeakSet();
  return JSON.stringify(obj, (k, v) => {
    if (typeof v === "object" && v !== null) {
      if (seen.has(v)) return "[Circular]";
      seen.add(v);
    }
    return v;
  });
}
```

---

If you want, tell me what you’re building (website, mobile app, backend API, e-commerce, etc.) and I’ll give you **a structured data folder pattern** (models/services/controllers) + a **mini real project** using Objects + JSON (frontend + backend).
