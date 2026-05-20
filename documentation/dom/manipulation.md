# DOM Manipulation Full Tutorial

The **DOM (Document Object Model)** is a programming interface for HTML and XML documents.
JavaScript uses the DOM to:

* Access HTML elements
* Change content/styles
* Handle events
* Create/remove elements dynamically

---

# 1. What is DOM?

When a browser loads an HTML page, it creates a tree-like structure.

Example HTML:

```html
<!DOCTYPE html>
<html>
<head>
  <title>DOM</title>
</head>
<body>
  <h1 id="title">Hello</h1>
  <p class="text">Welcome</p>
</body>
</html>
```

DOM Tree:

```text
Document
 └── html
      ├── head
      │    └── title
      └── body
           ├── h1
           └── p
```

JavaScript can manipulate every node in this tree.

---

# 2. Selecting Elements

---

## getElementById()

Select by ID.

```html
<h1 id="title">Hello</h1>

<script>
const title = document.getElementById("title");

console.log(title);
</script>
```

---

## getElementsByClassName()

Returns HTMLCollection.

```html
<p class="text">A</p>
<p class="text">B</p>

<script>
const items = document.getElementsByClassName("text");

console.log(items[0]);
</script>
```

---

## getElementsByTagName()

```javascript
const paragraphs = document.getElementsByTagName("p");
```

---

## querySelector()

Returns first matching element.

```javascript
const el = document.querySelector(".text");
```

Examples:

```javascript
document.querySelector("#id");
document.querySelector(".class");
document.querySelector("div");
```

---

## querySelectorAll()

Returns NodeList.

```javascript
const items = document.querySelectorAll(".text");

items.forEach(item => {
    console.log(item);
});
```

---

# 3. Changing Content

---

## innerText

```javascript
const title = document.getElementById("title");

title.innerText = "New Text";
```

---

## innerHTML

Allows HTML insertion.

```javascript
title.innerHTML = "<span>New HTML</span>";
```

---

## textContent

```javascript
title.textContent = "Simple Text";
```

Difference:

| Method      | Supports HTML |
| ----------- | ------------- |
| innerText   | No            |
| textContent | No            |
| innerHTML   | Yes           |

---

# 4. Changing Styles

```javascript
const title = document.getElementById("title");

title.style.color = "red";
title.style.backgroundColor = "black";
title.style.fontSize = "40px";
```

---

# 5. Working with Classes

---

## classList.add()

```javascript
element.classList.add("active");
```

---

## classList.remove()

```javascript
element.classList.remove("active");
```

---

## classList.toggle()

```javascript
element.classList.toggle("dark");
```

---

## classList.contains()

```javascript
console.log(element.classList.contains("active"));
```

---

# 6. Attributes

---

## getAttribute()

```javascript
const link = document.querySelector("a");

console.log(link.getAttribute("href"));
```

---

## setAttribute()

```javascript
link.setAttribute("href", "https://google.com");
```

---

## removeAttribute()

```javascript
link.removeAttribute("target");
```

---

# 7. Creating Elements

---

## createElement()

```javascript
const newDiv = document.createElement("div");

newDiv.innerText = "Hello";
```

---

## appendChild()

```javascript
document.body.appendChild(newDiv);
```

---

## append()

```javascript
document.body.append("Text");
document.body.append(newDiv);
```

---

# 8. Removing Elements

---

## remove()

```javascript
const item = document.querySelector(".box");

item.remove();
```

---

## removeChild()

```javascript
parent.removeChild(child);
```

---

# 9. Event Handling

Events are actions:

* click
* mouseover
* keydown
* submit
* input

---

## addEventListener()

```html
<button id="btn">Click</button>

<script>
const btn = document.getElementById("btn");

btn.addEventListener("click", () => {
    alert("Button clicked");
});
</script>
```

---

# 10. Common Events

---

## Click Event

```javascript
btn.addEventListener("click", function() {
    console.log("Clicked");
});
```

---

## Mouse Events

```javascript
box.addEventListener("mouseenter", () => {
    console.log("Mouse entered");
});
```

---

## Keyboard Events

```javascript
document.addEventListener("keydown", (e) => {
    console.log(e.key);
});
```

---

## Input Event

```javascript
input.addEventListener("input", (e) => {
    console.log(e.target.value);
});
```

---

## Submit Event

```javascript
form.addEventListener("submit", (e) => {
    e.preventDefault();

    console.log("Submitted");
});
```

---

# 11. Traversing the DOM

---

## parentElement

```javascript
console.log(element.parentElement);
```

---

## children

```javascript
console.log(element.children);
```

---

## firstElementChild

```javascript
console.log(element.firstElementChild);
```

---

## lastElementChild

```javascript
console.log(element.lastElementChild);
```

---

## nextElementSibling

```javascript
console.log(element.nextElementSibling);
```

---

## previousElementSibling

```javascript
console.log(element.previousElementSibling);
```

---

# 12. Forms and Input Handling

```html
<input type="text" id="name">
<button id="btn">Submit</button>

<script>
const input = document.getElementById("name");
const btn = document.getElementById("btn");

btn.addEventListener("click", () => {
    console.log(input.value);
});
</script>
```

---

# 13. DOM Projects

---

# Project 1: Counter App

```html
<h1 id="count">0</h1>

<button id="inc">+</button>
<button id="dec">-</button>

<script>
let count = 0;

const countEl = document.getElementById("count");

document.getElementById("inc").addEventListener("click", () => {
    count++;
    countEl.innerText = count;
});

document.getElementById("dec").addEventListener("click", () => {
    count--;
    countEl.innerText = count;
});
</script>
```

---

# Project 2: Dark Mode Toggle

```html
<button id="toggle">Toggle</button>

<script>
document.getElementById("toggle").addEventListener("click", () => {
    document.body.classList.toggle("dark");
});
</script>

<style>
.dark {
    background: black;
    color: white;
}
</style>
```

---

# Project 3: Todo App

```html
<input type="text" id="task">
<button id="add">Add</button>

<ul id="list"></ul>

<script>
const task = document.getElementById("task");
const list = document.getElementById("list");

document.getElementById("add").addEventListener("click", () => {

    const li = document.createElement("li");

    li.innerText = task.value;

    list.appendChild(li);

    task.value = "";
});
</script>
```

---

# 14. Event Bubbling

```html
<div id="parent">
    <button id="child">Click</button>
</div>

<script>
document.getElementById("parent")
.addEventListener("click", () => {
    console.log("Parent");
});

document.getElementById("child")
.addEventListener("click", () => {
    console.log("Child");
});
</script>
```

Output:

```text
Child
Parent
```

Because events bubble upward.

---

## stopPropagation()

```javascript
event.stopPropagation();
```

---

# 15. Event Delegation

Useful for dynamic elements.

```javascript
document.getElementById("list")
.addEventListener("click", (e) => {

    if(e.target.tagName === "LI") {
        console.log("Item clicked");
    }
});
```

---

# 16. setTimeout()

Runs once after delay.

```javascript
setTimeout(() => {
    console.log("Hello");
}, 2000);
```

---

# 17. setInterval()

Runs repeatedly.

```javascript
setInterval(() => {
    console.log("Running");
}, 1000);
```

---

# 18. DOM Loading Events

---

## DOMContentLoaded

```javascript
document.addEventListener("DOMContentLoaded", () => {
    console.log("DOM Ready");
});
```

---

# 19. Best Practices

✅ Use `querySelector()` for flexibility
✅ Use `addEventListener()` instead of inline JS
✅ Avoid excessive DOM updates
✅ Use event delegation for many elements
✅ Keep JS separate from HTML

---

# 20. Mini DOM Cheat Sheet

| Task           | Code                        |
| -------------- | --------------------------- |
| Select ID      | `document.getElementById()` |
| Select class   | `document.querySelector()`  |
| Change text    | `element.innerText`         |
| Change HTML    | `element.innerHTML`         |
| Change style   | `element.style.color`       |
| Add class      | `element.classList.add()`   |
| Create element | `document.createElement()`  |
| Remove element | `element.remove()`          |
| Listen event   | `addEventListener()`        |

---

# 21. Advanced Topics to Learn Next

After mastering DOM:

* Async JavaScript
* Fetch API
* LocalStorage
* Debouncing
* Virtual DOM
* React
* Browser Rendering
* Shadow DOM

---

# 22. Practice Ideas

Build these using only DOM:

1. Calculator
2. Notes App
3. Weather App
4. Quiz App
5. Chat UI
6. Stopwatch
7. Drag and Drop
8. Kanban Board
9. Infinite Scroll
10. Image Slider

---

# 23. Recommended Resources

* [MDN DOM Documentation](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model?utm_source=chatgpt.com)
* [JavaScript.info DOM Guide](https://javascript.info/document?utm_source=chatgpt.com)
* [freeCodeCamp JavaScript DOM Course](https://www.freecodecamp.org/news/javascript-dom-manipulation/?utm_source=chatgpt.com)
* [W3Schools DOM Tutorial](https://www.w3schools.com/js/js_htmldom.asp?utm_source=chatgpt.com)
