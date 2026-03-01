Here’s a **real-project cheat sheet** for **String + RegExp in JavaScript**: the methods that actually get used, what they do, and small **copy-pasteable** examples.

---

## ✅ String (most used in real projects)

### 1) Getting characters

```js
const s = "Hello";

s.charAt(1);      // "e"
s.at(-1);         // "o" (supports negative index)
s[0];             // "H"
s.codePointAt(0); // 72 (unicode code point)
```

### 2) Checking / searching

```js
const s = "email: test@example.com";

s.includes("test");        // true
s.startsWith("email:");    // true
s.endsWith(".com");        // true

s.indexOf("@");            // first position
s.lastIndexOf("e");        // last position
```

### 3) Extracting pieces

```js
const s = "https://site.com/path?q=1";

s.slice(8, 16);     // "site.com"
s.slice(-4);        // "q=1"
s.substring(0, 5);  // "https" (no negative indexes)
s.substr?.(0, 5);   // deprecated (avoid)
```

### 4) Splitting / joining (very common)

```js
"apple,banana,orange".split(","); // ["apple","banana","orange"]
"one   two   three".split(/\s+/); // ["one","two","three"]

["a","b","c"].join("-");          // "a-b-c"
```

### 5) Replace (super common in real apps)

#### replace (first match) + replaceAll (all matches)

```js
"foo bar foo".replace("foo", "x");       // "x bar foo"
"foo bar foo".replaceAll("foo", "x");    // "x bar x"
```

#### Replace with RegExp + callback (real-world)

```js
const text = "Price: $5 and $12";
const out = text.replace(/\$(\d+)/g, (_, n) => `৳${Number(n) * 110}`);
console.log(out); // "Price: ৳550 and ৳1320"
```

### 6) Trimming whitespace (inputs/forms)

```js
"   hello  ".trim();      // "hello"
"   hello  ".trimStart(); // "hello  "
"   hello  ".trimEnd();   // "   hello"
```

### 7) Padding (IDs, invoices)

```js
"7".padStart(3, "0"); // "007"
"AB".padEnd(5, ".");  // "AB..."
```

### 8) Case conversion

```js
"Hello".toLowerCase(); 
"hello".toUpperCase();

"istanbul".toLocaleUpperCase("tr"); // locale-aware
```

### 9) Repeat

```js
"-".repeat(10); // "----------"
```

### 10) Compare strings (sorting)

```js
["z", "ä", "a"].sort((a,b) => a.localeCompare(b, "en")); 
```

### 11) Normalize Unicode (when comparing user text)

```js
const a = "é";         // could be single codepoint
const b = "e\u0301";   // e + accent

a === b;                     // false
a.normalize() === b.normalize(); // true
```

### 12) Convert to/from numbers safely

```js
Number("12");      // 12
parseInt("12px");  // 12
parseFloat("3.14"); // 3.14
```

---

## ✅ RegExp in JS (real project patterns)

### A) RegExp basics

```js
const re1 = /abc/i;              // literal
const re2 = new RegExp("abc","i"); // dynamic pattern

re1.test("ABC"); // true
```

**Flags you’ll use**

* `g` global (find all)
* `i` case-insensitive
* `m` multiline (`^` and `$` work per line)
* `s` dotAll (`.` matches newline)
* `u` unicode
* `y` sticky (advanced)

---

## ✅ String methods that accept RegExp

### 1) `match()` (get matches)

```js
"one two two".match(/two/);    // ["two", index:4, ...]
"one two two".match(/two/g);   // ["two","two"]
```

### 2) `matchAll()` (best for capturing groups + global)

```js
const text = "id:12 id:99";
const re = /id:(\d+)/g;

for (const m of text.matchAll(re)) {
  console.log(m[0], m[1]); // "id:12" "12" then "id:99" "99"
}
```

### 3) `search()` (index of first match)

```js
"abc123".search(/\d+/); // 3
```

### 4) `replace()` / `replaceAll()`

```js
"2026-02-27".replace(/-/g, "/"); // "2026/02/27"
```

### 5) `split()` with regex

```js
"one,two;three  four".split(/[,; ]+/); // ["one","two","three","four"]
```

---

## ✅ RegExp methods you’ll use

### 1) `test()` (validation checks)

```js
/^\d{4}-\d{2}-\d{2}$/.test("2026-02-27"); // true
```

### 2) `exec()` (manual iteration; older style)

```js
const re = /(\w+)=(\d+)/g;
const s = "a=1 b=22";

let m;
while ((m = re.exec(s)) !== null) {
  console.log(m[1], m[2]); // a 1 then b 22
}
```

---

## ✅ Real project “recipes” (copy-paste)

### 1) Email validation (basic, practical)

```js
function isEmail(s) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(s.trim());
}
```

### 2) Password rule (min 8, upper, lower, number)

```js
function strongPassword(p) {
  return /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$/.test(p);
}
```

### 3) Extract all hashtags

```js
const text = "Post about #js and #web_dev!";
const tags = [...text.matchAll(/#([a-z0-9_]+)/gi)].map(m => m[1]);
console.log(tags); // ["js","web_dev"]
```

### 4) Slugify (URLs)

```js
function slugify(input) {
  return input
    .trim()
    .toLowerCase()
    .normalize("NFKD")
    .replace(/[\u0300-\u036f]/g, "")   // remove accents
    .replace(/[^a-z0-9]+/g, "-")       // non-alnum -> -
    .replace(/^-+|-+$/g, "");          // trim -
}

slugify("  Café & Milk Tea!!  "); // "cafe-milk-tea"
```

### 5) Sanitize user input (basic safe display)

*(For HTML security you usually use a library, but for plain text cleanup:)*

```js
function cleanText(s) {
  return s.replace(/\s+/g, " ").trim(); // collapse spaces
}
```

### 6) Parse query string

```js
function parseQuery(url) {
  const q = url.split("?")[1] ?? "";
  const obj = {};
  for (const part of q.split("&").filter(Boolean)) {
    const [k, v=""] = part.split("=");
    obj[decodeURIComponent(k)] = decodeURIComponent(v);
  }
  return obj;
}

parseQuery("x.com?a=1&name=MD%20Seyam");
// { a: "1", name: "MD Seyam" }
```

### 7) Remove all non-digits (phone cleanup)

```js
const raw = "+880 17-1234-5678";
const digits = raw.replace(/\D/g, "");
console.log(digits); // "8801712345678"
```

### 8) Highlight search terms in UI (escape + replace)

```js
function escapeRegExp(s) {
  return s.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");
}

function highlight(text, query) {
  if (!query) return text;
  const re = new RegExp(`(${escapeRegExp(query)})`, "gi");
  return text.replace(re, "<mark>$1</mark>");
}
```

### 9) Validate a Bangladeshi mobile number (simple practical)

```js
function isBDMobile(s) {
  const d = s.replace(/\D/g, "");
  // common forms: 01XXXXXXXXX, 8801XXXXXXXXX
  return /^(?:01\d{9}|8801\d{9})$/.test(d);
}
```

---

## ✅ Quick RegExp cheats (what symbols mean)

* `.` any char (except newline unless `s`)
* `\d` digit, `\D` non-digit
* `\w` word char, `\W` non-word
* `\s` whitespace, `\S` non-space
* `^` start, `$` end
* `+` one or more, `*` zero or more, `?` optional
* `{n}` exactly n, `{n,}` at least n, `{n,m}` range
* `[...]` set, `[^...]` not in set
* `( )` capture group, `(?: )` non-capturing
* `|` OR
* `(?=...)` lookahead, `(?!...)` negative lookahead

---

If you want, I can also give you a **“top 20 project-ready regex patterns”** (URLs, IPv4, usernames, dates, money formats, etc.) or a **mini project**: *form validator + input formatter* using these methods.
