Here are the most common ways to **reverse a string in JavaScript**:

---

### ✅ 1. Using built-in methods (most common way)

```js
function reverseString(str) {
  return str.split('').reverse().join('');
}

console.log(reverseString("hello")); 
// "olleh"
```

**How it works:**

* `split('')` → converts string to array
* `reverse()` → reverses the array
* `join('')` → converts array back to string

---

### ✅ 2. Using a loop

```js
function reverseString(str) {
  let reversed = '';
  for (let i = str.length - 1; i >= 0; i--) {
    reversed += str[i];
  }
  return reversed;
}

console.log(reverseString("hello"));
// "olleh"
```

---

### ✅ 3. Using recursion

```js
function reverseString(str) {
  if (str === '') return '';
  return reverseString(str.substr(1)) + str[0];
}

console.log(reverseString("hello"));
// "olleh"
```

---

### ✅ 4. ES6 spread operator

```js
const reverseString = str => [...str].reverse().join('');

console.log(reverseString("hello"));
// "olleh"
```

This is better than `split('')` when working with Unicode characters (like emojis).

---

If you'd like, I can also show:

* 🔹 How to reverse words instead of characters
* 🔹 How to reverse a string without built-in methods (interview style)
* 🔹 How to handle Unicode properly
* 🔹 Performance comparison between methods

Just tell
