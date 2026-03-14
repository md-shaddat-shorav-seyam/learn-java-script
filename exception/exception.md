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


# 2