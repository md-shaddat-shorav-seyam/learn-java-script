# JavaScript Array Operations — Full Guide with Examples

JavaScript arrays are one of the most important data structures in JS.

```js
let arr = [1, 2, 3, 4];
```

---

# 1. Creating Arrays

## Using Literal Syntax

```js
const numbers = [1, 2, 3];
```

## Using `new Array()`

```js
const arr = new Array(1, 2, 3);
```

## Empty Array

```js
const arr = [];
```

---

# 2. Accessing Elements

```js
const fruits = ["apple", "banana", "mango"];

console.log(fruits[0]); // apple
console.log(fruits[1]); // banana
```

---

# 3. Modifying Elements

```js
const arr = [10, 20, 30];

arr[1] = 50;

console.log(arr); // [10, 50, 30]
```

---

# 4. Array Length

```js
const arr = [1, 2, 3];

console.log(arr.length); // 3
```

---

# 5. Adding Elements

## `push()` → Add to End

```js
const arr = [1, 2];

arr.push(3);

console.log(arr); // [1,2,3]
```

## `unshift()` → Add to Beginning

```js
const arr = [2, 3];

arr.unshift(1);

console.log(arr); // [1,2,3]
```

---

# 6. Removing Elements

## `pop()` → Remove Last

```js
const arr = [1, 2, 3];

arr.pop();

console.log(arr); // [1,2]
```

## `shift()` → Remove First

```js
const arr = [1, 2, 3];

arr.shift();

console.log(arr); // [2,3]
```

---

# 7. Looping Through Arrays

## `for`

```js
const arr = [10, 20, 30];

for(let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
}
```

## `for...of`

```js
const arr = [1,2,3];

for(const value of arr){
    console.log(value);
}
```

## `forEach()`

```js
const arr = [1,2,3];

arr.forEach((value, index) => {
    console.log(index, value);
});
```

---

# 8. Searching Operations

## `includes()`

```js
const arr = [1,2,3];

console.log(arr.includes(2)); // true
```

## `indexOf()`

```js
const arr = [10,20,30];

console.log(arr.indexOf(20)); // 1
```

## `find()`

```js
const users = [
    {id:1, name:"John"},
    {id:2, name:"Alex"}
];

const user = users.find(u => u.id === 2);

console.log(user);
```

## `findIndex()`

```js
const arr = [5,10,15];

const index = arr.findIndex(v => v === 10);

console.log(index); // 1
```

---

# 9. Transforming Arrays

## `map()`

Creates a new array.

```js
const nums = [1,2,3];

const doubled = nums.map(n => n * 2);

console.log(doubled); // [2,4,6]
```

## `filter()`

```js
const nums = [1,2,3,4];

const even = nums.filter(n => n % 2 === 0);

console.log(even); // [2,4]
```

## `reduce()`

```js
const nums = [1,2,3,4];

const sum = nums.reduce((acc, curr) => acc + curr, 0);

console.log(sum); // 10
```

---

# 10. Sorting Arrays

## `sort()`

```js
const nums = [4,1,3,2];

nums.sort();

console.log(nums);
```

### Correct Number Sorting

```js
nums.sort((a,b) => a - b);
```

Descending:

```js
nums.sort((a,b) => b - a);
```

---

# 11. Reversing Arrays

```js
const arr = [1,2,3];

arr.reverse();

console.log(arr); // [3,2,1]
```

---

# 12. Combining Arrays

## `concat()`

```js
const a = [1,2];
const b = [3,4];

const c = a.concat(b);

console.log(c);
```

## Spread Operator

```js
const arr = [...a, ...b];
```

---

# 13. Extracting Parts

## `slice()`

Does NOT modify original array.

```js
const arr = [1,2,3,4,5];

const result = arr.slice(1,4);

console.log(result); // [2,3,4]
```

---

# 14. Modifying with `splice()`

Changes original array.

```js
const arr = [1,2,3,4];

arr.splice(1,2);

console.log(arr); // [1,4]
```

Add items:

```js
arr.splice(1,0,"new");
```

Replace items:

```js
arr.splice(1,1,"hello");
```

---

# 15. Joining Arrays into String

## `join()`

```js
const arr = ["a","b","c"];

console.log(arr.join("-")); // a-b-c
```

---

# 16. Converting String to Array

## `split()`

```js
const text = "apple,banana,mango";

const arr = text.split(",");

console.log(arr);
```

---

# 17. Checking Array Type

```js
const arr = [1,2,3];

console.log(Array.isArray(arr)); // true
```

---

# 18. Flatten Arrays

## `flat()`

```js
const arr = [1, [2,3], [4,[5]]];

console.log(arr.flat()); 
```

Deep flatten:

```js
arr.flat(Infinity);
```

---

# 19. Fill Arrays

## `fill()`

```js
const arr = new Array(5).fill(0);

console.log(arr);
```

---

# 20. Creating Arrays from Values

## `Array.from()`

```js
const str = "hello";

const arr = Array.from(str);

console.log(arr);
```

---

# 21. Destructuring Arrays

```js
const arr = [10,20,30];

const [a,b,c] = arr;

console.log(a); // 10
```

---

# 22. Copying Arrays

## Shallow Copy

```js
const arr1 = [1,2,3];

const arr2 = [...arr1];
```

---

# 23. Multidimensional Arrays

```js
const matrix = [
    [1,2],
    [3,4]
];

console.log(matrix[1][0]); // 3
```

---

# 24. Some Important Array Methods

| Method       | Purpose         |
| ------------ | --------------- |
| `push()`     | Add end         |
| `pop()`      | Remove end      |
| `shift()`    | Remove first    |
| `unshift()`  | Add first       |
| `map()`      | Transform       |
| `filter()`   | Filter          |
| `reduce()`   | Accumulate      |
| `find()`     | Find item       |
| `sort()`     | Sort            |
| `slice()`    | Copy portion    |
| `splice()`   | Modify          |
| `forEach()`  | Loop            |
| `concat()`   | Merge           |
| `includes()` | Check existence |

---

# 25. Real Project Example

## Shopping Cart Total

```js
const cart = [
    {name:"Laptop", price:500},
    {name:"Mouse", price:20},
    {name:"Keyboard", price:30}
];

const total = cart.reduce((sum, item) => {
    return sum + item.price;
}, 0);

console.log(total); // 550
```

---

# 26. Chaining Array Methods

```js
const nums = [1,2,3,4,5,6];

const result = nums
    .filter(n => n % 2 === 0)
    .map(n => n * 10);

console.log(result);
```

---

# 27. Advanced Methods

## `every()`

```js
const nums = [2,4,6];

console.log(nums.every(n => n % 2 === 0));
```

## `some()`

```js
const nums = [1,3,4];

console.log(nums.some(n => n % 2 === 0));
```

---

# 28. New Modern Methods

## `at()`

```js
const arr = [10,20,30];

console.log(arr.at(-1)); // 30
```

## `toSorted()`

Does not mutate original array.

```js
const nums = [3,1,2];

const sorted = nums.toSorted();

console.log(sorted);
```

---

# 29. Removing Duplicates

```js
const nums = [1,1,2,3,3];

const unique = [...new Set(nums)];

console.log(unique);
```

---

# 30. Important Interview Questions

## Reverse Array

```js
const arr = [1,2,3];

const reversed = [...arr].reverse();
```

## Maximum Value

```js
const nums = [5,10,20];

const max = Math.max(...nums);

console.log(max);
```

## Sum of Array

```js
const sum = nums.reduce((a,b) => a+b,0);
```

---

# Summary

JavaScript array operations are mainly divided into:

* Creation
* Access
* Modification
* Iteration
* Searching
* Transformation
* Sorting
* Combining
* Advanced operations

The most used methods in real projects are:

* `map()`
* `filter()`
* `reduce()`
* `find()`
* `sort()`
* `forEach()`
* `push()`
* `splice()`

These are heavily used in frameworks like React, Next.js, and backend apps using Node.js.
