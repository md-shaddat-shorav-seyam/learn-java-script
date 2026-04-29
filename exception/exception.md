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


end+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
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








# 3. Event bubbling



**Event Bubbling in JavaScript** is a fundamental concept in DOM event handling.

---

## 🔹 What is Event Bubbling?

Event bubbling means:

> When an event happens on an element, it first runs on that element, then **bubbles up** to its parent, then grandparent… all the way to the `document`.

---

## 🔹 Simple Example

```html
<div id="parent">
  <button id="child">Click Me</button>
</div>
```

```js
document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent clicked");
});

document.getElementById("child").addEventListener("click", () => {
  console.log("Child clicked");
});
```

### 👉 When you click the button:

```
Child clicked
Parent clicked
```

✔ First → child
✔ Then → parent

This is **event bubbling** 🚀

---

## 🔹 Flow of Events

There are **3 phases** in DOM events:

1. **Capturing phase** (top → down)
2. **Target phase** (actual element)
3. **Bubbling phase** (bottom → up)

By default, JavaScript uses **bubbling phase**.

---

## 🔹 Stop Bubbling

Sometimes you don’t want the event to go up.

Use:

```js
element.addEventListener("click", (e) => {
  e.stopPropagation();
});
```

### Example:

```js
document.getElementById("child").addEventListener("click", (e) => {
  console.log("Child clicked");
  e.stopPropagation();
});
```

👉 Now output will be:

```
Child clicked
```

❌ Parent won’t run

---

## 🔹 Real World Use Case (🔥 Important)

### Event Delegation

Instead of adding event to many elements:

```js
document.querySelectorAll("li").forEach(li => {
  li.addEventListener("click", () => console.log("clicked"));
});
```

👉 Use bubbling:

```js
document.getElementById("list").addEventListener("click", (e) => {
  console.log(e.target);
});
```

✔ Better performance
✔ Works for dynamic elements

---

## 🔹 Key Points

* Events move **child → parent**
* Default behavior = **bubbling**
* Use `stopPropagation()` to stop it
* Useful for **event delegation**

---

If you want, I can:
✅ Show **event capturing vs bubbling visually**
✅ Give a **real project (like todo app using delegation)**
✅ Or give **interview-level tricky questions**


end++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
---












# 4. promise resolve only once



Let’s break this down carefully 👇

## 🔍 Your Code

```js
const myP = new Promise((res, rej) => {
    setInterval(() => {
        res("user data loaded");
    }, 2000);
});

myP.then(e => {
    console.log(e);
});
```

---

## ⚡ What actually happens?

### 1. Promise is created

* The `Promise` executor runs immediately.
* Inside it, you start a `setInterval` that runs every **2 seconds**.

---

### 2. After 2 seconds ⏱️

* `res("user data loaded")` is called for the **first time**
* ✅ Promise becomes **fulfilled**
* `.then()` runs → prints:

```
user data loaded
```

---

### 3. After that… (important part 🚨)

The `setInterval` **keeps running forever**, calling `res(...)` every 2 seconds.

BUT:

👉 A Promise can be resolved **only once**

So:

* First call → works ✅
* Next calls → ignored ❌

---

## 🧠 Final Behavior

* `"user data loaded"` is printed **ONLY ONCE**
* But `setInterval` keeps running → **wasting memory/CPU**

---

## ⚠️ Problem in your code

Using `setInterval` inside a Promise is usually a mistake here.

👉 Because:

* Promise resolves once
* Interval runs forever → unnecessary

---

## ✅ Correct version (use `setTimeout`)

```js
const myP = new Promise((res, rej) => {
    setTimeout(() => {
        res("user data loaded");
    }, 2000);
});

myP.then(e => {
    console.log(e);
});
```

✔ Runs once
✔ No memory waste
✔ Correct async pattern

---

## 🧩 Bonus: When to use `setInterval`?

Use it only if you want **repeated actions**, not a single Promise resolution.

Example:

```js
setInterval(() => {
    console.log("Fetching data...");
}, 2000);
```

---

## 💡 Key Takeaway

👉 **Promise = single result**
👉 **setInterval = repeated execution**

Mixing them like this = usually wrong 🚫

---

If you want, I can show you:

* how to convert interval-based logic into async/await loop 🔁
* or real-world API polling example 🚀





end++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++=
---

# 5. always use semi-colon before iifes 

In JavaScript, you sometimes see this pattern:

```js
;(() => {
  console.log("IIFE running");
})();
```

That **leading semicolon (`;`)** is not random—it’s a **safety guard**.

### 🔥 The real reason

JavaScript has something called **Automatic Semicolon Insertion (ASI)**. It tries to insert semicolons automatically when you don’t write them.

But ASI can fail in some cases—especially when an **IIFE (Immediately Invoked Function Expression)** comes right after another statement.

---

### ⚠️ Problem without semicolon

```js
const x = 10
(() => {
  console.log("Hello");
})();
```

JavaScript may interpret this as:

```js
const x = 10(() => {...})();
```

👉 Which means: “call `10` like a function” → ❌ ERROR

---

### ✅ Solution: Add semicolon

```js
const x = 10;
(() => {
  console.log("Hello");
})();
```

Now it's clearly separated:

* `const x = 10;`
* then a new expression (IIFE)

---

### 💡 Why specifically before IIFE?

IIFEs usually start with:

* `(` → function expression
* or `[` or `+` etc.

These can accidentally **attach to previous code** if no semicolon is present.

---

### 🧠 Simple rule

👉 If your previous line **doesn’t end with `;`**, and next line starts with `(` →
**always add `;` before IIFE**

---

### 🧪 Real-world example

```js
const arr = [1, 2, 3]

;(() => {
  console.log(arr);
})();
```

Without `;`, JS might think:

```js
[1,2,3](...)
```

which is invalid.

---

### ✅ Best practice

* Either **always use semicolons**
* OR use **defensive semicolon before IIFE**

---

If you want, I can show you **more ASI pitfalls (very important for interviews & real projects)** or give you tricky examples to test your understanding.
end ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
---











