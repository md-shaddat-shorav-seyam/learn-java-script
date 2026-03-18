# 1. shadowing
Your code:

```js
let e = "i am e"
let z ="i am z"
let f= e=>console.log(e ,z);
f("hi every one")

console.log(e);
```

### Step-by-step what happens

1. `let e = "i am e"`
   → A variable **e** is created with value `"i am e"`.

2. `let z = "i am z"`
   → Variable **z** is created.

3. `let f = e => console.log(e, z);`
   → Function **f** is created with a **parameter also named `e`**.

   ⚠️ This `e` **shadows** the outer `e`.
   Inside the function, `e` refers to the **parameter**, not the outer variable.

4. `f("hi every one")`
   → Calls the function with `"hi every one"`.

   Inside the function:

   * `e` = `"hi every one"` (parameter)
   * `z` = `"i am z"` (from outer scope)

   Output:

   ```
   hi every one i am z
   ```

5. `console.log(e);`
   → This refers to the **original outer variable `e`**.

   Output:

   ```
   i am e
   ```

### Final Output

```
hi every one i am z
i am e
```

### Key Concept

This demonstrates **variable shadowing** and **closure** in JavaScript.

* **Shadowing**: the parameter `e` hides the outer `e`.
* **Closure**: the function still accesses `z` from the outer scope.

### Cleaner version (to avoid confusion)

```js
let e = "i am e";
let z = "i am z";

let f = msg => console.log(msg, z);

f("hi every one");
console.log(e);
```

If you want, I can also explain **scope chain in JavaScript (very important for interviews)** with simple diagrams.


end+++++++++++++++++++++++++++++++++++++++++++++++
----

------


# 2. **reference behavior in JavaScript arrays (and objects)**.

---

### 🔹 What you wrote:

```js
let e1 = [1,2,3];
let e2 = e1;

e2.pop();
console.log(e1, e2);
```

---

### 🔹 What you EXPECTED:

* `e2` is a copy of `e1`
* So changing `e2` should NOT affect `e1`

---

### 🔹 What ACTUALLY happens:

👉 Both `e1` and `e2` point to the **same array in memory**

So when you do:

```js
let e2 = e1;
```

You're NOT copying the array
You're just copying the **reference (address)**

---

### 🔹 Visual Explanation:

```
e1 ─────┐
        ├──> [1, 2, 3]
e2 ─────┘
```

After:

```js
e2.pop();
```

```
e1 ─────┐
        ├──> [1, 2]
e2 ─────┘
```

👉 Same array → both change

---

### 🔹 Why this happens?

Because arrays are **reference types** in JavaScript (not primitive values like numbers or strings)

---

### 🔹 How to FIX it (make a copy)

#### ✅ Method 1: Spread operator

```js
let e1 = [1,2,3];
let e2 = [...e1];

e2.pop();
console.log(e1, e2);
// [1,2,3] [1,2]
```

---

#### ✅ Method 2: slice()

```js
let e2 = e1.slice();
```

---

#### ✅ Method 3: Array.from()

```js
let e2 = Array.from(e1);
```

---

### 🔥 Important Concept

| Type         | Behavior       |
| ------------ | -------------- |
| Primitive    | Copy value     |
| Array/Object | Copy reference |

---

### ⚡ Quick Rule:

> If you see `[ ]` or `{ }` → be careful → it’s **reference**, not copy

---

If you want, I can show you **deep copy vs shallow copy** (very important for React & advanced JS 🔥).


end
---
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
