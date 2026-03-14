JavaScript **Collections** are built-in data structures used to store and manage groups of data. They help organize data efficiently and provide useful methods for accessing and manipulating that data.

The main **JavaScript collections** are:

* Array
* Map
* Set
* WeakMap
* WeakSet

I will explain each **from basic → advanced → syntax → methods → real project use cases**.

---

# 1. Array (Most Used Collection)

## What is an Array?

An **Array** is an ordered list of values stored in a single variable.

Features:

* Ordered collection
* Indexed (starts from 0)
* Can store any type
* Dynamic size

```
index → 0  1  2  3
value → A  B  C  D
```

---

# Array Syntax

### Create Array

```javascript
let arr = [];
```

```javascript
let arr = new Array();
```

### With Values

```javascript
let numbers = [1,2,3,4];
let fruits = ["apple","banana","mango"];
```

---

# Access Elements

```javascript
let fruits = ["apple","banana","mango"];

console.log(fruits[0]) // apple
console.log(fruits[1]) // banana
```

---

# Important Properties

### length

```javascript
let arr = [1,2,3,4];

console.log(arr.length)
```

---

# Important Array Methods

## Add Elements

### push()

Add at end

```javascript
let arr = [1,2,3]

arr.push(4)

console.log(arr)
```

Result

```
[1,2,3,4]
```

---

### unshift()

Add at beginning

```javascript
arr.unshift(0)
```

Result

```
[0,1,2,3]
```

---

# Remove Elements

### pop()

Remove last element

```javascript
arr.pop()
```

---

### shift()

Remove first element

```javascript
arr.shift()
```

---

# Search Methods

### includes()

```javascript
arr.includes(3)
```

---

### indexOf()

```javascript
arr.indexOf(3)
```

---

# Looping Array

### for loop

```javascript
for(let i=0;i<arr.length;i++){
console.log(arr[i])
}
```

---

### forEach

```javascript
arr.forEach((item)=>{
console.log(item)
})
```

---

### map

Transforms array

```javascript
let nums=[1,2,3]

let result=nums.map(n=>n*2)

console.log(result)
```

Output

```
[2,4,6]
```

---

# Real Project Example

### Todo List

```javascript
let todos = []

function addTodo(task){
    todos.push(task)
}

addTodo("study js")
addTodo("build project")

console.log(todos)
```

---

# 2. Map

## What is Map?

A **Map** is a collection of **key → value pairs**.

But unlike objects:

* Keys can be **any type**
* Maintains **insertion order**
* Better performance for frequent operations

---

# Map Syntax

### Create Map

```javascript
let map = new Map()
```

---

### Add values

```javascript
map.set("name","Seyam")
map.set("age",18)
```

---

### Get Value

```javascript
map.get("name")
```

---

### Check Key

```javascript
map.has("age")
```

---

### Delete

```javascript
map.delete("age")
```

---

### Size

```javascript
map.size
```

---

# Example

```javascript
let user = new Map()

user.set("name","Alex")
user.set("role","admin")

console.log(user.get("name"))
```

---

# Loop Map

```javascript
for(let [key,value] of user){

console.log(key,value)

}
```

---

# Convert Object → Map

```javascript
let obj = {
name:"alex",
age:22
}

let map = new Map(Object.entries(obj))
```

---

# Real Project Example

### Caching System

```javascript
let cache = new Map()

function getUser(id){

if(cache.has(id)){
return cache.get(id)
}

let data = "UserData"+id

cache.set(id,data)

return data
}
```

---

# 3. Set

## What is Set?

A **Set** is a collection of **unique values**.

No duplicates allowed.

Example

```
[1,2,3,3,4]
```

Set

```
[1,2,3,4]
```

---

# Set Syntax

### Create Set

```javascript
let set = new Set()
```

---

### Add

```javascript
set.add(1)
set.add(2)
set.add(3)
```

---

### Delete

```javascript
set.delete(2)
```

---

### Check

```javascript
set.has(3)
```

---

### Size

```javascript
set.size
```

---

# Example

```javascript
let numbers = new Set([1,2,2,3,4])

console.log(numbers)
```

Output

```
{1,2,3,4}
```

---

# Loop Set

```javascript
for(let value of numbers){
console.log(value)
}
```

---

# Convert Set → Array

```javascript
let arr = [...numbers]
```

---

# Real Project Example

### Remove duplicates

```javascript
let arr = [1,2,3,3,4,4]

let unique = [...new Set(arr)]

console.log(unique)
```

---

# 4. WeakMap

## What is WeakMap?

A **WeakMap** is similar to Map but:

Rules:

1. Keys must be **objects**
2. Keys are **weakly referenced**
3. Not iterable
4. Used for **private data**

---

# WeakMap Syntax

```javascript
let weak = new WeakMap()
```

---

### Add

```javascript
let obj = {}

weak.set(obj,"data")
```

---

### Get

```javascript
weak.get(obj)
```

---

### Check

```javascript
weak.has(obj)
```

---

### Delete

```javascript
weak.delete(obj)
```

---

# Example

```javascript
let wm = new WeakMap()

let user = {}

wm.set(user,"private data")

console.log(wm.get(user))
```

---

# Real Use Case

### Private Data Storage

```javascript
const privateData = new WeakMap()

class User{

constructor(name){

privateData.set(this,{name})

}

getName(){

return privateData.get(this).name

}

}
```

---

# 5. WeakSet

## What is WeakSet?

A **WeakSet** is like Set but:

Rules:

* Stores **only objects**
* Weak references
* Not iterable

---

# WeakSet Syntax

```javascript
let weakset = new WeakSet()
```

---

### Add

```javascript
let obj = {}

weakset.add(obj)
```

---

### Check

```javascript
weakset.has(obj)
```

---

### Delete

```javascript
weakset.delete(obj)
```

---

# Example

```javascript
let ws = new WeakSet()

let obj = {}

ws.add(obj)

console.log(ws.has(obj))
```

---

# Real Use Case

### Track visited objects

```javascript
let visited = new WeakSet()

function process(obj){

if(visited.has(obj)) return

visited.add(obj)

console.log("processing",obj)

}
```

---

# Comparison Table

| Feature       | Array | Map         | Set | WeakMap     | WeakSet     |
| ------------- | ----- | ----------- | --- | ----------- | ----------- |
| Ordered       | Yes   | Yes         | Yes | No          | No          |
| Key Value     | No    | Yes         | No  | Yes         | No          |
| Unique values | No    | Keys unique | Yes | Keys unique | Yes         |
| Iterable      | Yes   | Yes         | Yes | No          | No          |
| Key type      | Any   | Any         | Any | Object only | Object only |

---

# When To Use What

Use **Array**

When you need:

```
ordered list
loops
indexes
```

---

Use **Map**

When you need:

```
key value store
fast lookup
non-string keys
```

---

Use **Set**

When you need:

```
unique values
remove duplicates
```

---

Use **WeakMap**

When you need:

```
private data
memory safe caching
```

---

Use **WeakSet**

When you need:

```
track objects
avoid memory leaks
```

---

# Real World Example (Frontend)

Example: **Track logged in users**

```javascript
let onlineUsers = new Set()

function login(user){

onlineUsers.add(user)

}

function logout(user){

onlineUsers.delete(user)

}
```

---

If you want, I can also explain the **EXTREMELY IMPORTANT advanced topics** for JavaScript collections (very useful for serious JS developers like you):

* Time complexity of each collection
* Map vs Object deep comparison
* Set operations (Union / Intersection / Difference)
* WeakMap memory internals
* Advanced real-world systems using Map/Set
* How React internally uses Map/Set
* 10 advanced interview problems using collections.

Just tell me and I will explain them.
