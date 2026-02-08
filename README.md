# Python for JavaScript/TypeScript Engineers: The Definitive Guide

## About This Guide

This guide is written for experienced JavaScript and TypeScript developers who want to gain deep, production-level fluency in Python. It does not teach you how to program — it teaches you how to think in Python when you already think in JS/TS. Every concept is presented side-by-side with its JS/TS equivalent, and Python-only concepts with no JS/TS counterpart are clearly marked.

---

## 1. Philosophy and Mental Model Shift

### The Zen of Python vs. JavaScript's Flexibility

JavaScript is a multi-paradigm language born from the browser. It embraces flexibility, implicit coercion, and "many ways to do it." Python follows a starkly different philosophy, codified in PEP 20 — The Zen of Python. Run `import this` in a Python REPL to see it.

The key principles that will change how you write code:

- **"There should be one — and preferably only one — obvious way to do it."** In JS you can loop with `for`, `for...in`, `for...of`, `.forEach()`, `.map()`, `while`, etc. In Python, there is almost always one idiomatic way.
- **"Explicit is better than implicit."** Python avoids magic. No implicit type coercion, no hoisting, no automatic semicolon insertion.
- **"Readability counts."** Whitespace is syntactically significant. Code formatting is not a style choice — it is part of the language.

### Indentation Is Syntax

In JS/TS, braces `{}` define blocks. In Python, indentation defines blocks. This is non-negotiable.

```javascript
// JavaScript
if (condition) {
    doSomething();
    doMore();
}
```

```python
# Python
if condition:
    do_something()
    do_more()
```

The standard is 4 spaces per indent level (not tabs, not 2 spaces). Mixing tabs and spaces is a `TabError`.

### Naming Conventions

| Concept | JS/TS | Python |
|---------|-------|--------|
| Variables, functions | `camelCase` | `snake_case` |
| Classes | `PascalCase` | `PascalCase` |
| Constants | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` |
| Private members | `#field` or `_field` | `_field` (convention) or `__field` (name mangling) |
| File/module names | `camelCase.ts` or `kebab-case.ts` | `snake_case.py` |

### Semicolons, Braces, Parentheses

Python uses none of these for control flow. Colons `:` begin blocks. Newlines end statements. You can use `\` for line continuation or wrap in parentheses:

```python
# Line continuation
total = first_value + second_value + \
        third_value

# Implicit continuation inside parentheses (preferred)
total = (first_value + second_value +
         third_value)
```

---

## 2. Running Python and the Ecosystem

### Runtime Comparison

| Aspect | JS/TS | Python |
|--------|-------|--------|
| Runtime | Node.js / Deno / Bun / Browser | CPython (reference), PyPy, etc. |
| REPL | `node` | `python` or `ipython` |
| Package manager | npm / yarn / pnpm | pip / poetry / uv / pipenv |
| Package registry | npmjs.com | PyPI (pypi.org) |
| Manifest file | `package.json` | `pyproject.toml` (modern) or `setup.py` / `setup.cfg` |
| Lock file | `package-lock.json` / `yarn.lock` | `poetry.lock` / `uv.lock` / `requirements.txt` |
| Version manager | nvm / fnm / volta | pyenv / uv |
| Task runner | npm scripts / turbo | Makefile / tox / nox / just |
| Linter | ESLint | Ruff (modern) / Flake8 / Pylint |
| Formatter | Prettier | Ruff format (modern) / Black |
| Type checker | tsc (built into TS) | mypy / pyright / pytype |

### Virtual Environments (No Equivalent in JS/TS)

In Node.js, `node_modules` is project-local by default. Python installs packages globally by default, which creates conflicts. Virtual environments solve this:

```bash
# Create a virtual environment
python -m venv .venv

# Activate it
source .venv/bin/activate   # macOS/Linux
.venv\Scripts\activate       # Windows

# Now pip install goes into .venv/
pip install requests

# Deactivate
deactivate
```

Modern tools like `poetry` and `uv` manage this automatically:

```bash
# With uv (fastest, recommended in 2025+)
uv init myproject
cd myproject
uv add requests
uv run python main.py

# With poetry
poetry new myproject
cd myproject
poetry add requests
poetry run python main.py
```

### Project Structure

```
# JS/TS typical                    # Python typical
myproject/                         myproject/
├── src/                           ├── src/
│   ├── index.ts                   │   └── myproject/
│   ├── utils/                     │       ├── __init__.py
│   └── types/                     │       ├── main.py
├── tests/                         │       └── utils/
├── node_modules/                  │           ├── __init__.py
├── package.json                   │           └── helpers.py
├── tsconfig.json                  ├── tests/
└── .eslintrc.js                   │   ├── __init__.py
                                   │   └── test_main.py
                                   ├── pyproject.toml
                                   └── .venv/
```

The `__init__.py` files mark directories as Python packages (importable modules). In modern Python (3.3+), they can be empty but are still conventional.

---

## 3. Variables, Types, and Data Model

### Variable Declaration

```javascript
// JS/TS
const name = "Alice";       // Block-scoped, immutable binding
let age = 30;               // Block-scoped, mutable binding
var legacy = true;          // Function-scoped (avoid)
```

```python
# Python — no declaration keywords
name = "Alice"       # Just assign. Always mutable binding.
age = 30
legacy = True        # Note: True/False are capitalized
```

Python has no `const`. All bindings are reassignable. For constants, the convention is `UPPER_SNAKE_CASE`, enforced by linters, not the runtime.

```python
MAX_RETRIES = 3    # Convention: treat as constant
PI = 3.14159       # Nothing prevents reassignment at runtime
```

If you want immutability of the value itself, use immutable types (tuples, frozensets, NamedTuples).

### Type System Overview

| JS/TS Type | Python Type | Notes |
|-----------|-------------|-------|
| `number` | `int`, `float`, `complex` | Python separates integers and floats |
| `bigint` | `int` | Python ints are arbitrary precision by default |
| `string` | `str` | Immutable in both |
| `boolean` | `bool` | `True`/`False` (capitalized) |
| `null` | `None` | Singleton, use `is None` not `== None` |
| `undefined` | N/A | Does not exist in Python |
| `symbol` | N/A | No equivalent |
| `object` | `dict` | Python dicts are the equivalent of JS plain objects |
| `Array` | `list` | Mutable, ordered |
| `Tuple` (TS) | `tuple` | Immutable, ordered |
| `Set` | `set` | Mutable unordered unique collection |
| N/A | `frozenset` | Immutable set (no JS equivalent) |
| `Map` | `dict` | Python dicts maintain insertion order (3.7+) |
| `WeakMap` | `weakref.WeakValueDictionary` | Different API |
| `Uint8Array` etc. | `bytes`, `bytearray`, `memoryview` | Binary data types |

### Numbers in Depth

```javascript
// JS — everything is a 64-bit float
const x = 42;           // number
const big = 9007199254740993n;  // bigint
0.1 + 0.2 === 0.3       // false
```

```python
# Python — integers and floats are distinct
x = 42                   # int (arbitrary precision!)
big = 9007199254740993   # still just int, no special syntax needed
0.1 + 0.2 == 0.3        # False (same IEEE 754 issue)

# But Python has solutions built in:
from decimal import Decimal
Decimal("0.1") + Decimal("0.2") == Decimal("0.3")  # True

from fractions import Fraction
Fraction(1, 10) + Fraction(2, 10) == Fraction(3, 10)  # True

# Complex numbers (no JS equivalent)
z = 3 + 4j
z.real    # 3.0
z.imag    # 4.0
abs(z)    # 5.0 (magnitude)

# Integer operations
10 / 3     # 3.3333... (true division, returns float)
10 // 3    # 3 (floor division, returns int)
10 % 3     # 1 (modulo)
2 ** 10    # 1024 (exponentiation, not ^ which is XOR)
```

Python integers have no size limit. You can compute `2 ** 10000` and get an exact result.

### None vs null/undefined

```javascript
// JS has two "nothing" values
let a = null;        // Intentional absence
let b = undefined;   // Uninitialized / missing
typeof null          // "object" (infamous bug)
null == undefined    // true (loose equality)
null === undefined   // false
```

```python
# Python has exactly one: None
a = None
type(None)           # 

# ALWAYS use `is` for None checks, not ==
if a is None:
    print("It's None")

if a is not None:
    print("It has a value")
```

`None` is a singleton — there is exactly one `None` object in the entire program. That's why `is` (identity check) is preferred over `==` (equality check).

### Truthiness and Falsy Values

```javascript
// JS falsy: false, 0, -0, 0n, "", null, undefined, NaN
// Everything else is truthy, including [] and {}
```

```python
# Python falsy: False, 0, 0.0, 0j, "", (), [], {}, set(), frozenset(),
#               None, and any object with __bool__() returning False
#               or __len__() returning 0
# CRITICAL DIFFERENCE: empty collections are falsy in Python!

if []:          # False (empty list is falsy)
    pass
if [0]:         # True (non-empty list, even if element is falsy)
    pass
if {}:          # False (empty dict is falsy)
    pass
if "":          # False
    pass
if " ":         # True (non-empty string)
    pass
```

### Type Checking

```javascript
// JS
typeof "hello"        // "string"
typeof 42             // "number"
x instanceof Array    // true
Array.isArray(x)      // true
```

```python
# Python
type("hello")          # 
isinstance("hello", str)   # True (preferred — supports inheritance)
isinstance(42, (int, float))  # True (check multiple types with tuple)

# AVOID type() for checks — it doesn't handle inheritance
type(True) == int      # False (bool is a subclass of int)
isinstance(True, int)  # True
```

---

## 4. Strings

### String Basics

```javascript
// JS
const single = 'hello';
const double = "hello";
const template = `Hello ${name}, you are ${age} years old`;
const multiline = `line 1
line 2`;
const raw = String.raw`no \n escape`;
```

```python
# Python — single and double quotes are identical
single = 'hello'
double = "hello"

# f-strings (Python 3.6+) = template literals
template = f"Hello {name}, you are {age} years old"

# f-strings support any expression
result = f"Total: {price * quantity:.2f}"
debug = f"{some_var=}"  # Python 3.8+: prints "some_var=value"

# Triple quotes for multiline (either ''' or """)
multiline = """line 1
line 2"""

# Raw strings (no escape processing)
raw = r"no \n escape"

# Byte strings (no JS equivalent in this form)
data = b"raw bytes"
```

### String Methods Comparison

| JS | Python | Notes |
|----|--------|-------|
| `str.length` | `len(str)` | Python uses a built-in function |
| `str.toUpperCase()` | `str.upper()` | |
| `str.toLowerCase()` | `str.lower()` | |
| `str.trim()` | `str.strip()` | |
| `str.trimStart()` | `str.lstrip()` | |
| `str.trimEnd()` | `str.rstrip()` | |
| `str.includes(s)` | `s in str` | Python uses the `in` operator |
| `str.startsWith(s)` | `str.startswith(s)` | Note lowercase 'w' |
| `str.endsWith(s)` | `str.endswith(s)` | |
| `str.indexOf(s)` | `str.index(s)` or `str.find(s)` | `index` raises, `find` returns -1 |
| `str.slice(a, b)` | `str[a:b]` | Python uses slicing |
| `str.split(sep)` | `str.split(sep)` | Same concept |
| `str.replace(a, b)` | `str.replace(a, b)` | Python replaces ALL occurrences by default |
| `str.repeat(n)` | `str * n` | Operator overloading |
| `str.padStart(n, c)` | `str.rjust(n, c)` | |
| `str.padEnd(n, c)` | `str.ljust(n, c)` | |
| `str.charCodeAt(i)` | `ord(str[i])` | |
| `String.fromCharCode(n)` | `chr(n)` | |
| `[...str].join(sep)` | `sep.join(iterable)` | Python: `join` is on the separator |

### String Formatting (Python-only Depth)

```python
# f-strings (modern, preferred)
name = "Alice"
f"Hello, {name}"                    # Basic
f"{3.14159:.2f}"                    # "3.14"
f"{1000000:,}"                      # "1,000,000"
f"{42:08b}"                         # "00101010" (binary, 8-wide, zero-padded)
f"{value!r}"                        # Uses repr() instead of str()
f"{'hello':>20}"                    # Right-justify in 20-char field
f"{'hello':^20}"                    # Center in 20-char field
f"{'hello':-<20}"                   # Left-justify, pad with dashes

# .format() method (older but still useful)
"Hello, {}".format(name)
"Hello, {name}".format(name="Alice")
"{0} is {1}, {0} is great".format("Python", "fun")

# %-formatting (oldest, C-style)
"Hello, %s. You are %d." % (name, age)

# textwrap module for wrapping long text
import textwrap
textwrap.fill(long_string, width=80)
textwrap.dedent("""
    This text
    is indented
""")
```

---

## 5. Collections: Lists, Tuples, Dicts, Sets

### Lists (≈ Arrays)

```javascript
// JS Arrays
const arr = [1, 2, 3];
arr.push(4);               // [1,2,3,4]
arr.pop();                  // returns 4
arr.unshift(0);             // [0,1,2,3]
arr.shift();                // returns 0
arr.splice(1, 1);           // remove 1 element at index 1
arr.includes(2);            // true
arr.indexOf(2);             // 1
arr.concat([4, 5]);         // [1,2,3,4,5]
arr.slice(1, 3);            // [2,3]
arr.reverse();              // in-place
arr.sort();                 // in-place, lexicographic!
arr.sort((a, b) => a - b);  // numeric sort
const copy = [...arr];      // shallow copy
```

```python
# Python Lists
lst = [1, 2, 3]
lst.append(4)               # [1,2,3,4]
lst.pop()                   # returns 4, removes last
lst.insert(0, 0)            # [0,1,2,3] — insert at index
lst.pop(0)                  # returns 0, removes first
del lst[1]                  # remove by index (no return value)
lst.remove(2)               # remove first occurrence of VALUE 2
2 in lst                    # True (O(n) for lists)
lst.index(2)                # 1 (raises ValueError if not found)
lst + [4, 5]                # [1,2,3,4,5] (new list)
lst.extend([4, 5])          # in-place append multiple
lst[1:3]                    # [2,3] (slicing)
lst.reverse()               # in-place
lst.sort()                  # in-place, natural order (numbers work correctly!)
lst.sort(key=lambda x: x)   # custom key function
sorted(lst)                 # returns new sorted list (non-mutating)
copy = lst[:]               # shallow copy (or lst.copy() or list(lst))

# Python lists can hold mixed types (but generally avoid this)
mixed = [1, "hello", True, [2, 3]]
```

### Slicing (Python-only, Extremely Powerful)

```python
# Slicing: sequence[start:stop:step]
# start is inclusive, stop is exclusive (like JS slice)
lst = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

lst[2:5]        # [2, 3, 4]
lst[:3]         # [0, 1, 2]       (from start)
lst[7:]         # [7, 8, 9]       (to end)
lst[-3:]        # [7, 8, 9]       (last 3)
lst[:-2]        # [0,1,2,3,4,5,6,7] (all but last 2)
lst[::2]        # [0, 2, 4, 6, 8]  (every other)
lst[1::2]       # [1, 3, 5, 7, 9]  (odd indices)
lst[::-1]       # [9,8,7,6,5,4,3,2,1,0] (reversed)
lst[5:2:-1]     # [5, 4, 3]       (backwards slice)

# Slicing works on strings too
"Hello, World"[7:]  # "World"

# Slice assignment (mutating)
lst[2:5] = [20, 30]   # Replace elements at indices 2-4
lst[1:1] = [10, 11]   # Insert without removing
lst[2:4] = []          # Delete elements
```

### Tuples (No Direct JS Equivalent)

Tuples are immutable lists. They are used when you want a fixed collection that cannot change.

```python
# Creation
t = (1, 2, 3)
t = 1, 2, 3            # Parentheses are optional
single = (42,)          # Single-element tuple NEEDS trailing comma
not_a_tuple = (42)      # This is just int 42 in parentheses

# Operations
t[0]                    # 1 (indexing works)
t[1:3]                  # (2, 3) (slicing works)
len(t)                  # 3
1 in t                  # True
# t[0] = 99            # TypeError! Tuples are immutable

# Tuple unpacking (destructuring in JS)
a, b, c = (1, 2, 3)
first, *rest = [1, 2, 3, 4]    # first=1, rest=[2,3,4]
first, *middle, last = [1,2,3,4,5]  # first=1, middle=[2,3,4], last=5

# Use as dict keys (lists can't be dict keys because they're mutable)
locations = {(40.7, -74.0): "New York", (51.5, -0.1): "London"}

# Named tuples (like simple interfaces/types)
from collections import namedtuple
Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)
p.x  # 3
p.y  # 4
```

### Dictionaries (≈ Objects / Maps)

```javascript
// JS Objects and Maps
const obj = { name: "Alice", age: 30 };
obj.name;                   // "Alice"
obj["name"];                // "Alice"
obj.email = "a@b.com";     // add property
delete obj.age;             // delete property
"name" in obj;              // true
Object.keys(obj);           // ["name", "email"]
Object.values(obj);         // ["Alice", "a@b.com"]
Object.entries(obj);        // [["name","Alice"], ["email","a@b.com"]]
const { name, age } = obj;  // destructuring

// Map
const map = new Map();
map.set("key", "value");
map.get("key");
map.has("key");
map.delete("key");
map.size;
```

```python
# Python Dicts
d = {"name": "Alice", "age": 30}
d["name"]                   # "Alice"
d.get("name")               # "Alice" (returns None if missing, no error)
d.get("email", "default")   # "default" (custom default)
d["email"] = "a@b.com"      # add/update
del d["age"]                 # delete (raises KeyError if missing)
d.pop("age", None)           # delete and return (no error if missing)
"name" in d                  # True (checks keys)
d.keys()                     # dict_keys(["name", "email"]) — view object
d.values()                   # dict_values(["Alice", "a@b.com"])
d.items()                    # dict_items([("name","Alice"), ("email","a@b.com")])

# No destructuring syntax, but you can unpack in loops:
for key, value in d.items():
    print(f"{key}: {value}")

# Merging dicts
merged = {**d1, **d2}             # Spread-like (Python 3.5+)
merged = d1 | d2                   # Merge operator (Python 3.9+)
d1.update(d2)                      # In-place merge

# Dict comprehension
squares = {x: x**2 for x in range(10)}
# {0:0, 1:1, 2:4, 3:9, ...}

# defaultdict — auto-initializes missing keys
from collections import defaultdict
word_counts = defaultdict(int)       # Missing keys default to 0
word_counts["hello"] += 1           # No KeyError!

grouped = defaultdict(list)
grouped["fruits"].append("apple")   # Auto-creates the list

# OrderedDict (mostly historical, regular dicts maintain order since 3.7)
# Counter — counts occurrences
from collections import Counter
counts = Counter("mississippi")
# Counter({'s': 4, 'i': 4, 'p': 2, 'm': 1})
counts.most_common(2)  # [('s', 4), ('i', 4)]
```

### Sets

```javascript
// JS Set
const s = new Set([1, 2, 3]);
s.add(4);
s.has(2);
s.delete(2);
s.size;
// No built-in intersection/union/difference methods until recent proposals
```

```python
# Python Set — much more powerful
s = {1, 2, 3}                  # Literal syntax (NOT dict!)
empty_set = set()              # {} creates an empty DICT, not a set!
s.add(4)
2 in s                         # True (O(1) lookup)
s.remove(2)                    # Raises KeyError if missing
s.discard(2)                   # No error if missing
len(s)

# Set operations (mathematical sets!)
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}
a | b                          # Union: {1,2,3,4,5,6}
a & b                          # Intersection: {3,4}
a - b                          # Difference: {1,2}
a ^ b                          # Symmetric difference: {1,2,5,6}
a <= b                         # Subset check
a >= b                         # Superset check

# frozenset — immutable set (can be used as dict key or set element)
fs = frozenset([1, 2, 3])

# Set comprehension
evens = {x for x in range(20) if x % 2 == 0}
```

---

## 6. Control Flow

### Conditionals

```javascript
// JS
if (x > 0) {
    positive();
} else if (x < 0) {
    negative();
} else {
    zero();
}

// Ternary
const result = condition ? "yes" : "no";

// Switch
switch (value) {
    case "a": handleA(); break;
    case "b": handleB(); break;
    default: handleDefault();
}
```

```python
# Python
if x > 0:
    positive()
elif x < 0:           # elif, not "else if"
    negative()
else:
    zero()

# Conditional expression (ternary equivalent)
result = "yes" if condition else "no"

# Python has no switch statement, but has match/case (3.10+)
match value:
    case "a":
        handle_a()
    case "b":
        handle_b()
    case _:            # _ is the wildcard/default
        handle_default()
```

### Match/Case — Structural Pattern Matching (Python 3.10+, No JS Equivalent)

This is far more powerful than JS switch. It performs structural pattern matching:

```python
# Match on type and structure
match command:
    case {"action": "move", "direction": direction}:
        # Destructures the dict, binds `direction`
        move(direction)
    case {"action": "attack", "target": target, "weapon": weapon}:
        attack(target, weapon)
    case {"action": "quit"}:
        quit()

# Match with guards
match point:
    case (x, y) if x == y:
        print(f"On diagonal at {x}")
    case (x, y) if x > 0 and y > 0:
        print(f"In quadrant 1")
    case (0, y):
        print(f"On Y axis at {y}")

# Match on class instances
match event:
    case Click(position=(x, y)) if x > 100:
        handle_right_click(x, y)
    case KeyPress(key="q"):
        quit()
    case KeyPress(key=k):
        handle_key(k)

# Or patterns
match status:
    case 200 | 201 | 202:
        handle_success()
    case 404:
        handle_not_found()
    case 500 | 502 | 503:
        handle_server_error()
```

### Loops

```javascript
// JS
for (let i = 0; i < 10; i++) { ... }
for (const item of array) { ... }
for (const key in object) { ... }
while (condition) { ... }
do { ... } while (condition);
array.forEach((item, index) => { ... });
```

```python
# Python — NO C-style for loop, no do...while
for i in range(10):           # range(start, stop, step)
    ...

for item in some_list:        # Iterate directly (preferred)
    ...

for i, item in enumerate(some_list):   # With index
    ...

for key in some_dict:         # Iterates over keys
    ...

for key, value in some_dict.items():   # Key-value pairs
    ...

while condition:
    ...

# Python has NO do...while. Workaround:
while True:
    do_something()
    if not condition:
        break

# Loop with else (Python-only, very unique)
for item in items:
    if item == target:
        print("Found!")
        break
else:
    # This runs ONLY if the loop completed WITHOUT a break
    print("Not found!")

# Useful for search patterns — the else clause means "exhausted"
```

### Comprehensions (Python-only, Extremely Important)

Comprehensions are Python's most idiomatic feature. They replace `map`, `filter`, and simple loops:

```python
# List comprehension (≈ array.map + array.filter combined)
# [expression for item in iterable if condition]

# JS: array.map(x => x * 2)
doubles = [x * 2 for x in numbers]

# JS: array.filter(x => x > 0)
positives = [x for x in numbers if x > 0]

# JS: array.filter(x => x > 0).map(x => x * 2)
result = [x * 2 for x in numbers if x > 0]

# Nested loops
# JS: matrix.flat() or matrix.flatMap(row => row)
flat = [cell for row in matrix for cell in row]

# Dict comprehension
name_to_length = {name: len(name) for name in names}

# Set comprehension
unique_lengths = {len(name) for name in names}

# Generator expression (lazy, memory-efficient — covered in Generators chapter)
total = sum(x**2 for x in range(1000000))  # Doesn't create a list in memory

# Conditional expression in comprehension
labels = ["even" if x % 2 == 0 else "odd" for x in numbers]

# Nested comprehension (less common, prefer separate comprehensions for readability)
matrix = [[i * j for j in range(5)] for i in range(5)]
```

---

## 7. Functions

### Basic Functions

```javascript
// JS
function greet(name) {
    return `Hello, ${name}!`;
}

const greet = (name) => `Hello, ${name}!`;

// Default parameters
function connect(host, port = 3000) { ... }
```

```python
# Python
def greet(name):
    return f"Hello, {name}!"

# Lambda (single expression only — Python lambdas are limited)
greet = lambda name: f"Hello, {name}!"

# Default parameters
def connect(host, port=3000):
    ...

# WARNING: Mutable default argument trap!
def append_to(item, target=[]):    # BUG! The list is shared across calls
    target.append(item)
    return target

append_to(1)    # [1]
append_to(2)    # [1, 2] ← NOT [2] !!!

# Correct pattern:
def append_to(item, target=None):
    if target is None:
        target = []
    target.append(item)
    return target
```

### Arguments: Positional, Keyword, *args, **kwargs

This is where Python functions are far more expressive than JS:

```python
# Positional and keyword arguments
def create_user(name, age, email="", active=True):
    ...

create_user("Alice", 30)                          # Positional
create_user("Alice", 30, email="a@b.com")         # Mix of positional and keyword
create_user(name="Alice", age=30)                  # All keyword
create_user(age=30, name="Alice")                  # Order doesn't matter for kwargs

# *args — collect extra positional args into a tuple (≈ ...rest in JS)
def log(message, *args):
    print(message, *args)

log("hello", 1, 2, 3)  # message="hello", args=(1, 2, 3)

# **kwargs — collect extra keyword args into a dict (no JS equivalent)
def create_element(tag, **attrs):
    # attrs is a dict of all extra keyword arguments
    print(tag, attrs)

create_element("div", id="main", class_="container")
# tag="div", attrs={"id": "main", "class_": "container"}

# Combining all forms
def func(pos1, pos2, *args, kwonly1, kwonly2="default", **kwargs):
    # pos1, pos2: required positional
    # *args: extra positional (tuple)
    # kwonly1: keyword-only, required
    # kwonly2: keyword-only, with default
    # **kwargs: extra keyword args (dict)
    ...

# Keyword-only arguments (after * or *args)
def fetch(url, *, method="GET", headers=None):
    # method and headers MUST be passed as keywords
    ...

fetch("https://api.com", method="POST")    # OK
fetch("https://api.com", "POST")           # TypeError!

# Positional-only arguments (before /, Python 3.8+)
def pow(base, exp, /):
    return base ** exp

pow(2, 10)        # OK
pow(base=2, exp=10)  # TypeError!

# Full syntax showing all argument types:
def func(pos_only, /, normal, *, kw_only):
    ...
```

### Unpacking and Spreading

```javascript
// JS spread and rest
const arr = [1, 2, 3];
console.log(...arr);           // spread in call
const [a, ...rest] = arr;     // destructuring
const merged = [...arr1, ...arr2];

const obj = { a: 1, b: 2 };
const merged_obj = { ...obj1, ...obj2 };
```

```python
# Python unpacking
lst = [1, 2, 3]
print(*lst)                     # Spread in call: print(1, 2, 3)
a, *rest = lst                  # a=1, rest=[2,3]
merged = [*lst1, *lst2]        # [1,2,3,4,5,6]

d = {"a": 1, "b": 2}
merged_dict = {**d1, **d2}    # Merge dicts

# Unpack into function call
def add(x, y, z):
    return x + y + z

args = [1, 2, 3]
add(*args)                      # add(1, 2, 3)

kwargs = {"x": 1, "y": 2, "z": 3}
add(**kwargs)                   # add(x=1, y=2, z=3)
```

### Closures and Scope

```javascript
// JS closures work naturally
function counter() {
    let count = 0;
    return () => ++count;
}
```

```python
# Python closures need the `nonlocal` keyword to MODIFY enclosed variables
def counter():
    count = 0
    def increment():
        nonlocal count    # Without this, assignment creates a new local variable!
        count += 1
        return count
    return increment

c = counter()
c()  # 1
c()  # 2

# Without nonlocal, you get UnboundLocalError
# Reading enclosed variables works without nonlocal; only modification needs it
```

### Decorators (Python-only)

Decorators are a Pythonic way to wrap functions. They are heavily used in frameworks (Flask, FastAPI, Django, pytest).

```python
# A decorator is a function that takes a function and returns a function
import functools

def timer(func):
    @functools.wraps(func)    # Preserves original function's metadata
    def wrapper(*args, **kwargs):
        import time
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"{func.__name__} took {elapsed:.2f}s")
        return result
    return wrapper

@timer
def slow_function():
    import time
    time.sleep(1)

slow_function()  # Prints "slow_function took 1.00s"

# Equivalent to: slow_function = timer(slow_function)

# Decorator with arguments
def retry(max_attempts=3, delay=1):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    import time
                    time.sleep(delay)
        return wrapper
    return decorator

@retry(max_attempts=5, delay=2)
def unreliable_api_call():
    ...

# Stacking decorators
@decorator_a
@decorator_b
def func():
    ...
# Equivalent to: func = decorator_a(decorator_b(func))

# Class-based decorator (for stateful decorators)
class CountCalls:
    def __init__(self, func):
        functools.update_wrapper(self, func)
        self.func = func
        self.num_calls = 0

    def __call__(self, *args, **kwargs):
        self.num_calls += 1
        return self.func(*args, **kwargs)

@CountCalls
def say_hello():
    print("hello")
```

### Higher-Order Functions

```javascript
// JS
arr.map(x => x * 2);
arr.filter(x => x > 0);
arr.reduce((acc, x) => acc + x, 0);
arr.some(x => x > 10);
arr.every(x => x > 0);
arr.find(x => x > 10);
```

```python
# Python equivalents — but comprehensions are preferred over map/filter
list(map(lambda x: x * 2, lst))       # Use [x * 2 for x in lst] instead
list(filter(lambda x: x > 0, lst))    # Use [x for x in lst if x > 0] instead
from functools import reduce
reduce(lambda acc, x: acc + x, lst, 0)  # Use sum(lst) or a loop instead
any(x > 10 for x in lst)              # True if any match
all(x > 0 for x in lst)               # True if all match
next((x for x in lst if x > 10), None) # First match or None

# functools has many useful higher-order functions
from functools import partial, lru_cache

# Partial application (no native equivalent in JS without libraries)
def multiply(x, y):
    return x * y

double = partial(multiply, 2)
double(5)  # 10

# Memoization (caching)
@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

# Python 3.9+: @cache for unlimited cache
from functools import cache

@cache
def expensive_computation(n):
    ...
```

---

## 8. Object-Oriented Programming

### Classes

```javascript
// JS/TS
class Animal {
    #sound;  // Private field (JS)

    constructor(name, sound) {
        this.name = name;
        this.#sound = sound;
    }

    speak() {
        return `${this.name} says ${this.#sound}`;
    }

    static create(name) {
        return new Animal(name, "...");
    }

    get info() {
        return `${this.name} (${this.#sound})`;
    }

    set volume(v) {
        this.#sound = this.#sound.toUpperCase();
    }
}
```

```python
class Animal:
    # Class variable (shared by all instances, like static fields)
    kingdom = "Animalia"

    def __init__(self, name: str, sound: str):
        # Instance variables
        self.name = name           # Public
        self._sound = sound        # Convention: "private" (not enforced)
        self.__secret = "hidden"   # Name mangling: becomes _Animal__secret

    def speak(self) -> str:
        # `self` is explicit and REQUIRED (no implicit `this`)
        return f"{self.name} says {self._sound}"

    @staticmethod
    def create(name: str) -> "Animal":
        return Animal(name, "...")

    @classmethod
    def from_dict(cls, data: dict) -> "Animal":
        # cls is the class itself — works properly with inheritance
        return cls(data["name"], data["sound"])

    @property
    def info(self) -> str:
        """Getter — access as animal.info (no parentheses)"""
        return f"{self.name} ({self._sound})"

    @info.setter
    def info(self, value: str):
        """Setter — assign as animal.info = 'new value'"""
        self.name = value

    def __repr__(self) -> str:
        """Official string representation (for debugging)"""
        return f"Animal(name={self.name!r}, sound={self._sound!r})"

    def __str__(self) -> str:
        """Human-readable string (for display)"""
        return f"{self.name} the animal"
```

### Key Differences from JS Classes

1. **`self` is explicit.** Every instance method's first parameter is `self`. It's not a keyword — it's convention, but universally followed.
2. **No `new` keyword.** Just call the class: `animal = Animal("Dog", "woof")`
3. **`@classmethod` vs `@staticmethod`.** `@classmethod` receives the class as first arg (`cls`), enabling proper inheritance. `@staticmethod` receives nothing extra.
4. **`@property` replaces getters/setters.** Access looks like an attribute, not a method call.
5. **No true private fields.** `_single_underscore` is convention. `__double_underscore` triggers name mangling (becomes `_ClassName__attribute`), which makes accidental access harder but can still be accessed.

### Inheritance

```python
class Dog(Animal):
    def __init__(self, name: str, breed: str):
        super().__init__(name, "Woof")   # Call parent constructor
        self.breed = breed

    def speak(self) -> str:
        return f"{super().speak()}! (breed: {self.breed})"

# Multiple inheritance (JS does not support this)
class FlyingDog(Dog, FlyingMixin):
    pass

# Python uses Method Resolution Order (MRO) to resolve conflicts
FlyingDog.__mro__   # Shows the lookup order
```

### Multiple Inheritance and Mixins (Python-only)

```python
# Mixins — classes designed to be combined, not used alone
class SerializableMixin:
    def to_json(self):
        import json
        return json.dumps(self.__dict__)

class LoggableMixin:
    def log(self, message):
        print(f"[{self.__class__.__name__}] {message}")

class User(SerializableMixin, LoggableMixin):
    def __init__(self, name, email):
        self.name = name
        self.email = email

user = User("Alice", "alice@example.com")
user.to_json()       # '{"name": "Alice", "email": "alice@example.com"}'
user.log("created")  # [User] created
```

### Dataclasses (Python 3.7+, ≈ TypeScript interfaces + class combined)

Dataclasses eliminate boilerplate for classes that are primarily data containers:

```python
from dataclasses import dataclass, field

@dataclass
class User:
    name: str
    email: str
    age: int = 0
    tags: list[str] = field(default_factory=list)  # Mutable defaults need field()

    # __init__, __repr__, __eq__ are auto-generated!

# Equivalent to writing:
# def __init__(self, name, email, age=0, tags=None): ...
# def __repr__(self): ...
# def __eq__(self, other): ...

user = User("Alice", "alice@example.com", 30)
print(user)  # User(name='Alice', email='alice@example.com', age=30, tags=[])

# Frozen (immutable) dataclass
@dataclass(frozen=True)
class Point:
    x: float
    y: float

# With ordering
@dataclass(order=True)
class Version:
    major: int
    minor: int
    patch: int

# Post-init processing
@dataclass
class Rectangle:
    width: float
    height: float
    area: float = field(init=False)  # Computed, not in __init__

    def __post_init__(self):
        self.area = self.width * self.height

# Slots (Python 3.10+) for memory efficiency
@dataclass(slots=True)
class LightweightPoint:
    x: float
    y: float
```

### Dunder (Magic) Methods (Python-only)

Dunder methods (double underscore) let you define how your objects behave with operators, built-in functions, and Python protocols:

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    # String representations
    def __repr__(self):        return f"Vector({self.x}, {self.y})"
    def __str__(self):         return f"({self.x}, {self.y})"

    # Arithmetic operators
    def __add__(self, other):  return Vector(self.x + other.x, self.y + other.y)
    def __sub__(self, other):  return Vector(self.x - other.x, self.y - other.y)
    def __mul__(self, scalar): return Vector(self.x * scalar, self.y * scalar)
    def __rmul__(self, scalar): return self.__mul__(scalar)  # 3 * vector
    def __neg__(self):         return Vector(-self.x, -self.y)

    # Comparison
    def __eq__(self, other):   return self.x == other.x and self.y == other.y
    def __lt__(self, other):   return abs(self) < abs(other)

    # Built-in functions
    def __abs__(self):         return (self.x**2 + self.y**2) ** 0.5
    def __len__(self):         return 2
    def __bool__(self):        return self.x != 0 or self.y != 0

    # Container protocol
    def __getitem__(self, i):  return (self.x, self.y)[i]
    def __iter__(self):        yield self.x; yield self.y

    # Callable protocol — makes instance callable like a function
    def __call__(self, scale):
        return Vector(self.x * scale, self.y * scale)

    # Context manager protocol (see with statement section)
    def __enter__(self):       return self
    def __exit__(self, *args): pass

    # Hashing (for use in sets/dicts)
    def __hash__(self):        return hash((self.x, self.y))

v1 = Vector(1, 2)
v2 = Vector(3, 4)
v1 + v2        # Vector(4, 6)  — uses __add__
abs(v1)        # 2.236...      — uses __abs__
v1(10)         # Vector(10, 20) — uses __call__
list(v1)       # [1, 2]        — uses __iter__
```

### Abstract Base Classes (≈ TypeScript interfaces, partially)

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self) -> float:
        """Must be implemented by subclasses"""
        ...

    @abstractmethod
    def perimeter(self) -> float:
        ...

    def describe(self) -> str:
        """Concrete method — inherited as-is"""
        return f"Shape with area {self.area():.2f}"

# shape = Shape()  # TypeError: Can't instantiate abstract class

class Circle(Shape):
    def __init__(self, radius: float):
        self.radius = radius

    def area(self) -> float:
        import math
        return math.pi * self.radius ** 2

    def perimeter(self) -> float:
        import math
        return 2 * math.pi * self.radius
```

### Protocols (Python 3.8+, ≈ TypeScript Structural Typing)

```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None: ...

class Circle:
    def draw(self) -> None:
        print("Drawing circle")

# Circle is considered a Drawable even without explicit inheritance!
# This is structural (duck) typing enforced by the type checker.
def render(item: Drawable) -> None:
    item.draw()

render(Circle())  # Works — Circle has a draw() method
```

---

## 9. Error Handling

```javascript
// JS
try {
    riskyOperation();
} catch (error) {
    if (error instanceof TypeError) {
        handleType(error);
    } else {
        throw error;  // Re-throw
    }
} finally {
    cleanup();
}

// Custom errors
class AppError extends Error {
    constructor(message, code) {
        super(message);
        this.code = code;
    }
}
```

```python
# Python
try:
    risky_operation()
except TypeError as e:
    handle_type(e)
except (ValueError, KeyError) as e:    # Catch multiple types
    handle_value_or_key(e)
except Exception as e:                  # Catch all non-system exceptions
    print(f"Unexpected: {e}")
    raise                               # Re-raise the same exception
else:
    # Runs ONLY if no exception occurred (Python-only!)
    success_handler()
finally:
    cleanup()

# Custom exceptions
class AppError(Exception):
    def __init__(self, message: str, code: int):
        super().__init__(message)
        self.code = code

# Exception chaining (shows the cause)
try:
    fetch_data()
except ConnectionError as e:
    raise AppError("Failed to fetch", 500) from e

# Suppress exceptions with contextlib
from contextlib import suppress
with suppress(FileNotFoundError):
    os.remove("tempfile.txt")    # No error if file doesn't exist

# Common Python exception hierarchy:
# BaseException
#   ├── SystemExit
#   ├── KeyboardInterrupt
#   ├── GeneratorExit
#   └── Exception
#       ├── ArithmeticError (ZeroDivisionError, OverflowError)
#       ├── AttributeError
#       ├── EOFError
#       ├── ImportError (ModuleNotFoundError)
#       ├── LookupError (IndexError, KeyError)
#       ├── NameError (UnboundLocalError)
#       ├── OSError (FileNotFoundError, PermissionError, etc.)
#       ├── RuntimeError (RecursionError, NotImplementedError)
#       ├── StopIteration
#       ├── TypeError
#       └── ValueError (UnicodeError)
```

---

## 10. Modules and Imports

### Import Styles

```javascript
// JS/TS
import express from 'express';          // Default import
import { Router } from 'express';       // Named import
import * as fs from 'fs';               // Namespace import
const pkg = require('some-pkg');         // CommonJS
import type { User } from './types';    // Type-only import (TS)
```

```python
# Python
import os                              # Import entire module
from os import path                    # Import specific item
from os.path import join, exists       # Import multiple items
from os import path as ospath          # Alias
import numpy as np                     # Alias (extremely common convention)
from typing import List, Dict          # Type imports (no special syntax needed)

# Relative imports (within a package)
from . import sibling_module           # Same directory
from .. import parent_module           # Parent directory
from .sibling import some_function

# Conditional imports (common pattern)
try:
    import ujson as json    # Faster JSON library
except ImportError:
    import json             # Fallback to standard library

# Lazy imports (for performance)
def process_data():
    import pandas as pd     # Only imported when function is called
    return pd.DataFrame()

# __all__ controls what `from module import *` exports
__all__ = ["public_function", "PublicClass"]

# Import guards
if __name__ == "__main__":
    # Only runs when this file is executed directly, not when imported
    main()
```

### Module System Differences

| Aspect | JS/TS | Python |
|--------|-------|--------|
| Module = | One file | One file |
| Package = | Directory with package.json | Directory with `__init__.py` |
| Resolution | node_modules + complex algorithm | `sys.path` (simpler) |
| Circular imports | Generally work (with caveats) | More problematic (import at function level to fix) |
| Dynamic import | `await import('module')` | `importlib.import_module('module')` |
| Reload | Not built in | `importlib.reload(module)` |

---

## 11. Asynchronous Programming

### async/await

```javascript
// JS
async function fetchData(url) {
    const response = await fetch(url);
    const data = await response.json();
    return data;
}

// Concurrent execution
const [a, b] = await Promise.all([fetchA(), fetchB()]);
await Promise.allSettled([taskA(), taskB()]);
const first = await Promise.race([taskA(), taskB()]);
```

```python
# Python
import asyncio

async def fetch_data(url: str):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            data = await response.json()
            return data

# Concurrent execution
a, b = await asyncio.gather(fetch_a(), fetch_b())

# With error handling (like allSettled)
results = await asyncio.gather(task_a(), task_b(), return_exceptions=True)
for result in results:
    if isinstance(result, Exception):
        print(f"Task failed: {result}")

# Race (first to complete)
done, pending = await asyncio.wait(
    [asyncio.create_task(task_a()), asyncio.create_task(task_b())],
    return_when=asyncio.FIRST_COMPLETED
)
for task in pending:
    task.cancel()

# Running async code from synchronous context
asyncio.run(fetch_data("https://api.example.com"))

# Async iteration
async for item in async_generator():
    process(item)

# Async context manager
async with aiofiles.open("file.txt") as f:
    content = await f.read()

# TaskGroup (Python 3.11+, structured concurrency)
async with asyncio.TaskGroup() as tg:
    task1 = tg.create_task(fetch_a())
    task2 = tg.create_task(fetch_b())
# Both tasks guaranteed to be done here, exceptions propagate
```

### Key Differences

1. **Python async is not built into the language syntax as deeply.** You need `asyncio.run()` to enter the async world.
2. **No top-level await** in regular scripts (only in `asyncio.run()` or async REPL).
3. **The ecosystem is split.** Regular `requests` library is sync; you need `aiohttp` or `httpx` for async HTTP. Regular `open()` is sync; you need `aiofiles` for async file I/O.
4. **Python has the GIL** (Global Interpreter Lock), so async is for I/O concurrency, not CPU parallelism.

---

## 12. Iterators and Generators

### Generators (≈ JS Generators, but used far more heavily)

```javascript
// JS generators
function* range(n) {
    for (let i = 0; i < n; i++) {
        yield i;
    }
}
for (const i of range(5)) { console.log(i); }
```

```python
# Python generators
def range_gen(n):
    for i in range(n):
        yield i

for i in range_gen(5):
    print(i)

# Generator expressions (like list comprehensions but lazy)
squares_list = [x**2 for x in range(1000000)]   # Creates entire list in memory
squares_gen = (x**2 for x in range(1000000))     # Creates values on demand

# Generators are everywhere in Python
# range() itself is not a generator but a lazy iterable
# dict.keys(), dict.values(), dict.items() return view objects
# map(), filter(), zip(), enumerate() all return lazy iterators

# yield from — delegate to a sub-generator
def flatten(nested):
    for item in nested:
        if isinstance(item, list):
            yield from flatten(item)   # Recursively yield all items
        else:
            yield item

list(flatten([1, [2, [3, 4], 5], 6]))  # [1, 2, 3, 4, 5, 6]

# Two-way generators (send values in)
def accumulator():
    total = 0
    while True:
        value = yield total
        if value is None:
            break
        total += value

gen = accumulator()
next(gen)       # 0 (prime the generator)
gen.send(10)    # 10
gen.send(20)    # 30
gen.send(5)     # 35
```

### The Iterator Protocol

```python
# Any object with __iter__ and __next__ is an iterator
class Countdown:
    def __init__(self, start):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        self.current -= 1
        return self.current + 1

for n in Countdown(5):
    print(n)  # 5, 4, 3, 2, 1

# itertools — powerful iteration utilities (no JS equivalent)
import itertools

itertools.chain([1,2], [3,4])          # 1,2,3,4 (concatenate iterables)
itertools.islice(range(100), 5, 10)    # 5,6,7,8,9 (slice an iterator)
itertools.product("AB", "12")          # A1,A2,B1,B2 (cartesian product)
itertools.permutations("ABC", 2)       # AB,AC,BA,BC,CA,CB
itertools.combinations("ABCD", 2)      # AB,AC,AD,BC,BD,CD
itertools.groupby(data, key=func)      # Group consecutive items
itertools.accumulate([1,2,3,4])        # 1,3,6,10 (running totals)
itertools.zip_longest([1,2], [3,4,5])  # (1,3),(2,4),(None,5)
itertools.cycle([1,2,3])              # 1,2,3,1,2,3,... (infinite)
itertools.repeat(42, 5)               # 42,42,42,42,42
itertools.starmap(pow, [(2,3),(3,2)]) # 8, 9
```

---

## 13. Context Managers (The `with` Statement — Python-only)

This is one of Python's most important patterns with no direct JS equivalent:

```python
# The `with` statement handles setup and teardown automatically
# It guarantees cleanup even if an exception occurs

# File handling (the most common use case)
with open("file.txt", "r") as f:
    content = f.read()
# f is automatically closed here, even if an exception occurred

# JS equivalent would be try/finally:
# const f = fs.openSync("file.txt");
# try { ... } finally { fs.closeSync(f); }

# Multiple context managers
with open("input.txt") as inp, open("output.txt", "w") as out:
    out.write(inp.read())

# Creating your own context manager with a class
class Timer:
    def __enter__(self):
        import time
        self.start = time.time()
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        import time
        self.elapsed = time.time() - self.start
        print(f"Elapsed: {self.elapsed:.2f}s")
        return False   # Don't suppress exceptions

with Timer() as t:
    heavy_computation()

# Creating a context manager with a generator (simpler)
from contextlib import contextmanager

@contextmanager
def timer():
    import time
    start = time.time()
    yield   # Code inside the `with` block runs here
    elapsed = time.time() - start
    print(f"Elapsed: {elapsed:.2f}s")

with timer():
    heavy_computation()

# Common contextlib utilities
from contextlib import suppress, redirect_stdout, closing

with suppress(FileNotFoundError):
    os.remove("maybe_exists.txt")

with redirect_stdout(open("log.txt", "w")):
    print("This goes to the file")

# Async context managers
async with aiohttp.ClientSession() as session:
    async with session.get(url) as response:
        data = await response.json()
```

---

## 14. Type Hints (Python's Type System vs TypeScript)

Python is dynamically typed at runtime, but supports optional static type hints checked by external tools (mypy, pyright). This is similar in spirit to how TypeScript layers types onto JavaScript.

```python
# Basic type annotations
name: str = "Alice"
age: int = 30
active: bool = True
scores: list[int] = [95, 87, 92]      # Python 3.9+
config: dict[str, int] = {"timeout": 30}

# Function signatures
def greet(name: str, times: int = 1) -> str:
    return f"Hello, {name}! " * times

# Optional (≈ TS string | null)
from typing import Optional
def find_user(id: int) -> Optional[str]:    # str | None
    ...

# Python 3.10+: use | directly
def find_user(id: int) -> str | None:
    ...

# Union types
from typing import Union
value: Union[int, str] = 42   # Pre-3.10
value: int | str = 42         # Python 3.10+

# Collections (Python 3.9+ use lowercase)
from typing import List, Dict, Tuple, Set   # Pre-3.9
items: list[str] = []
mapping: dict[str, list[int]] = {}
pair: tuple[str, int] = ("hello", 42)
variadic_tuple: tuple[int, ...] = (1, 2, 3, 4)

# TypedDict (≈ TS interface for objects)
from typing import TypedDict

class UserDict(TypedDict):
    name: str
    age: int
    email: str | None

# With optional keys
class UserDict(TypedDict, total=False):
    name: str          # Required because total=False just sets default
    age: int

# Callable types (≈ TS function types)
from typing import Callable
handler: Callable[[int, str], bool]   # (int, str) => bool

# Generics (≈ TS generics)
from typing import TypeVar, Generic

T = TypeVar("T")

class Stack(Generic[T]):
    def __init__(self) -> None:
        self._items: list[T] = []

    def push(self, item: T) -> None:
        self._items.append(item)

    def pop(self) -> T:
        return self._items.pop()

int_stack: Stack[int] = Stack()

# Python 3.12+: simplified generic syntax
class Stack[T]:
    def __init__(self) -> None:
        self._items: list[T] = []

# TypeVar with constraints
T = TypeVar("T", bound=Comparable)      # Upper bound (≈ TS extends)
T = TypeVar("T", int, float)            # Restricted to specific types

# Literal types
from typing import Literal
def set_mode(mode: Literal["read", "write", "append"]) -> None:
    ...

# Type aliases
UserId = int
UserMap = dict[UserId, "User"]

# Python 3.12+: type statement
type Vector = list[float]
type UserMap = dict[int, User]

# Self type (Python 3.11+)
from typing import Self
class Builder:
    def add(self, item: str) -> Self:
        ...
        return self

# TypeGuard (≈ TS type predicates)
from typing import TypeGuard
def is_string_list(val: list) -> TypeGuard[list[str]]:
    return all(isinstance(x, str) for x in val)

# @overload (≈ TS function overloads)
from typing import overload

@overload
def process(value: int) -> str: ...
@overload
def process(value: str) -> int: ...

def process(value: int | str) -> str | int:
    if isinstance(value, int):
        return str(value)
    return len(value)
```

### Runtime Type Checking

Python type hints are NOT enforced at runtime by default. For runtime validation, use:

```python
# Pydantic — runtime validation with type hints
from pydantic import BaseModel

class User(BaseModel):
    name: str
    age: int
    email: str | None = None

user = User(name="Alice", age="30")  # age auto-coerced to int
user = User(name="Alice", age="not a number")  # ValidationError!

# attrs — similar to dataclasses but more powerful
import attrs

@attrs.define
class Point:
    x: float = attrs.field(validator=attrs.validators.instance_of(float))
    y: float = attrs.field(validator=attrs.validators.instance_of(float))
```

---

## 15. File I/O

```javascript
// JS (Node.js)
const fs = require('fs');
const data = fs.readFileSync('file.txt', 'utf-8');
fs.writeFileSync('out.txt', data);
const stream = fs.createReadStream('big.txt');
```

```python
# Python — ALWAYS use `with` for file operations
# Reading
with open("file.txt", "r", encoding="utf-8") as f:
    content = f.read()           # Read entire file
    # or
    lines = f.readlines()       # List of lines
    # or
    for line in f:              # Iterate line by line (memory efficient)
        process(line.strip())

# Writing
with open("out.txt", "w", encoding="utf-8") as f:
    f.write("Hello\n")
    f.writelines(["line1\n", "line2\n"])

# Appending
with open("log.txt", "a") as f:
    f.write("New entry\n")

# Binary files
with open("image.png", "rb") as f:
    data = f.read()

# pathlib — modern, object-oriented file paths (Python 3.4+)
from pathlib import Path

p = Path("data") / "subdir" / "file.txt"   # OS-independent path joining
p.exists()
p.is_file()
p.is_dir()
p.name           # "file.txt"
p.stem           # "file"
p.suffix         # ".txt"
p.parent         # Path("data/subdir")
p.read_text()    # Read entire file as string
p.write_text("content")  # Write string to file
p.read_bytes()   # Read as bytes
p.mkdir(parents=True, exist_ok=True)  # mkdir -p equivalent
list(p.glob("*.py"))      # Find files matching pattern
list(p.rglob("*.py"))     # Recursive glob

# Temp files
import tempfile
with tempfile.NamedTemporaryFile(mode="w", suffix=".txt", delete=False) as f:
    f.write("temp data")
    print(f.name)   # Path to temp file
```

---

## 16. Concurrency and Parallelism

### Threading vs Multiprocessing vs Async

Python has the GIL (Global Interpreter Lock) which prevents true CPU parallelism with threads. This is a critical difference from Node.js/browsers:

| Approach | Use Case | GIL Impact | JS Equivalent |
|----------|----------|-----------|---------------|
| `asyncio` | I/O-bound concurrent tasks | Not affected (cooperative) | `async/await` + event loop |
| `threading` | I/O-bound parallel tasks | Threads blocked by GIL for CPU | `Worker` (limited) |
| `multiprocessing` | CPU-bound parallel tasks | Separate processes, no GIL | Web Workers / worker_threads |
| `concurrent.futures` | High-level thread/process pools | Depends on executor type | Promise pools (custom) |

```python
# concurrent.futures — highest-level API
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

# Thread pool for I/O-bound tasks
with ThreadPoolExecutor(max_workers=10) as executor:
    futures = [executor.submit(fetch_url, url) for url in urls]
    results = [f.result() for f in futures]

# Process pool for CPU-bound tasks
with ProcessPoolExecutor() as executor:
    results = list(executor.map(heavy_computation, data_chunks))

# Threading
import threading

def worker(name):
    print(f"Worker {name} running")

threads = [threading.Thread(target=worker, args=(i,)) for i in range(5)]
for t in threads:
    t.start()
for t in threads:
    t.join()

# Thread synchronization
lock = threading.Lock()
with lock:
    shared_resource.update()

# Multiprocessing
from multiprocessing import Process, Pool, Queue

with Pool(processes=4) as pool:
    results = pool.map(compute, range(100))
```

### Python 3.13+ Free-Threading (No GIL)

Python 3.13 introduced an experimental free-threaded mode (PEP 703) that removes the GIL. This is opt-in and still experimental as of 2025:

```bash
# Install free-threaded Python
# Build with: ./configure --disable-gil
python3.13t script.py  # 't' suffix for free-threaded build
```

---

## 17. Testing

```javascript
// JS (Jest)
describe('Calculator', () => {
    test('adds numbers', () => {
        expect(add(1, 2)).toBe(3);
    });
    test('throws on string', () => {
        expect(() => add("a", 1)).toThrow(TypeError);
    });
});
```

```python
# Python (pytest — the standard)
import pytest

def test_add():
    assert add(1, 2) == 3

def test_add_throws():
    with pytest.raises(TypeError):
        add("a", 1)

# Parametrize — run same test with different inputs
@pytest.mark.parametrize("a, b, expected", [
    (1, 2, 3),
    (-1, 1, 0),
    (0, 0, 0),
])
def test_add_cases(a, b, expected):
    assert add(a, b) == expected

# Fixtures (setup/teardown, like beforeEach)
@pytest.fixture
def db_connection():
    conn = create_connection()
    yield conn              # Test runs here
    conn.close()            # Teardown

def test_query(db_connection):   # Fixture auto-injected by name
    result = db_connection.execute("SELECT 1")
    assert result == 1

# Fixture scopes
@pytest.fixture(scope="module")   # Once per module (vs "function", "class", "session")
def expensive_resource():
    ...

# Mocking
from unittest.mock import Mock, patch, MagicMock

@patch("mymodule.external_api")
def test_with_mock(mock_api):
    mock_api.return_value = {"status": "ok"}
    result = my_function()
    assert result == "ok"
    mock_api.assert_called_once()

# Standard library unittest (less common, but important to know)
import unittest

class TestCalculator(unittest.TestCase):
    def setUp(self):
        self.calc = Calculator()

    def test_add(self):
        self.assertEqual(self.calc.add(1, 2), 3)

    def tearDown(self):
        pass
```

---

## 18. Standard Library Highlights

Python's standard library is massive ("batteries included"). Here are modules with no direct JS equivalent:

```python
# collections — advanced data structures
from collections import (
    defaultdict,    # Dict with default values
    Counter,        # Count occurrences
    deque,          # Double-ended queue (O(1) append/pop both ends)
    OrderedDict,    # Dict that remembers insertion order
    ChainMap,       # Combine multiple dicts into one view
    namedtuple,     # Tuple with named fields
)

# dataclasses — reduce class boilerplate (covered earlier)
from dataclasses import dataclass

# enum — enumerations
from enum import Enum, auto, IntEnum

class Color(Enum):
    RED = auto()
    GREEN = auto()
    BLUE = auto()

Color.RED.name    # "RED"
Color.RED.value   # 1

class HttpStatus(IntEnum):
    OK = 200
    NOT_FOUND = 404
    # Can be compared with integers: HttpStatus.OK == 200

# functools — function tools
from functools import (
    lru_cache,      # Memoization
    partial,        # Partial application
    reduce,         # Fold/reduce
    wraps,          # Preserve function metadata in decorators
    singledispatch, # Function overloading by type
    cached_property, # Cached property (computed once)
    total_ordering,  # Auto-generate comparison methods
)

@singledispatch
def process(value):
    raise TypeError(f"Unsupported type: {type(value)}")

@process.register(int)
def _(value):
    return value * 2

@process.register(str)
def _(value):
    return value.upper()

# itertools — iteration utilities (covered earlier)
# operator — function versions of operators
from operator import itemgetter, attrgetter, methodcaller
sorted(users, key=attrgetter("age"))
sorted(pairs, key=itemgetter(1))

# contextlib — context manager utilities
from contextlib import contextmanager, suppress, ExitStack

# re — regular expressions
import re
match = re.search(r"\d+", "abc123def")
match.group()  # "123"
re.findall(r"\d+", "abc123def456")  # ["123", "456"]
re.sub(r"\d+", "NUM", "abc123def")  # "abcNUMdef"

# struct — binary data packing (like Buffer operations in Node)
import struct
packed = struct.pack(">Ih", 1, 2)  # Big-endian uint32 + int16

# logging — production-grade logging
import logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)
logger.info("Processing %s items", count)
logger.error("Failed: %s", error, exc_info=True)

# argparse — CLI argument parsing
import argparse
parser = argparse.ArgumentParser(description="My tool")
parser.add_argument("input", help="Input file")
parser.add_argument("-o", "--output", default="out.txt")
parser.add_argument("-v", "--verbose", action="store_true")
args = parser.parse_args()

# json
import json
data = json.loads('{"key": "value"}')
text = json.dumps(data, indent=2)
with open("data.json") as f:
    data = json.load(f)

# datetime
from datetime import datetime, timedelta, timezone
now = datetime.now()
utc_now = datetime.now(timezone.utc)
future = now + timedelta(days=30)
formatted = now.strftime("%Y-%m-%d %H:%M:%S")
parsed = datetime.strptime("2025-01-15", "%Y-%m-%d")

# typing extensions
from typing import (
    Any, NoReturn, Never,
    ClassVar, Final,
    Annotated,
    TypeAlias,
    assert_type,
    reveal_type,
)
```

---

## 19. Descriptors (Python-only, Advanced)

Descriptors are Python's mechanism for customizing attribute access. They power `@property`, `@classmethod`, `@staticmethod`, and ORMs like Django/SQLAlchemy:

```python
class Validated:
    """A descriptor that validates values on assignment."""
    def __init__(self, min_val=None, max_val=None):
        self.min_val = min_val
        self.max_val = max_val

    def __set_name__(self, owner, name):
        self.name = name
        self.private_name = f"_{name}"

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return getattr(obj, self.private_name, None)

    def __set__(self, obj, value):
        if self.min_val is not None and value < self.min_val:
            raise ValueError(f"{self.name} must be >= {self.min_val}")
        if self.max_val is not None and value > self.max_val:
            raise ValueError(f"{self.name} must be <= {self.max_val}")
        setattr(obj, self.private_name, value)

class Product:
    price = Validated(min_val=0)
    quantity = Validated(min_val=0, max_val=10000)

    def __init__(self, name, price, quantity):
        self.name = name
        self.price = price          # Triggers Validated.__set__
        self.quantity = quantity

p = Product("Widget", 9.99, 100)
p.price = -1   # ValueError: price must be >= 0
```

---

## 20. Metaclasses (Python-only, Advanced)

Metaclasses are "classes of classes." They control how classes are created:

```python
# A metaclass is a class whose instances are classes
class SingletonMeta(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class Database(metaclass=SingletonMeta):
    def __init__(self):
        self.connection = "connected"

db1 = Database()
db2 = Database()
assert db1 is db2   # Same instance!

# __init_subclass__ — simpler alternative for many metaclass uses (Python 3.6+)
class Plugin:
    _registry = {}

    def __init_subclass__(cls, plugin_name=None, **kwargs):
        super().__init_subclass__(**kwargs)
        name = plugin_name or cls.__name__.lower()
        Plugin._registry[name] = cls

class EmailPlugin(Plugin, plugin_name="email"):
    pass

class SMSPlugin(Plugin, plugin_name="sms"):
    pass

Plugin._registry  # {"email": EmailPlugin, "sms": SMSPlugin}
```

---

## 21. Walrus Operator (`:=`) — Python 3.8+

The assignment expression lets you assign and use a value in the same expression:

```python
# Without walrus
line = input()
while line != "quit":
    process(line)
    line = input()

# With walrus
while (line := input()) != "quit":
    process(line)

# In comprehensions
results = [y for x in data if (y := expensive(x)) > threshold]

# In if statements
if (match := re.search(pattern, text)) is not None:
    print(match.group())

# Reading file chunks
with open("big_file.bin", "rb") as f:
    while (chunk := f.read(8192)):
        process(chunk)
```

---

## 22. Slots (Python-only, Performance)

By default, Python objects store attributes in a `__dict__` dictionary. Slots restrict this:

```python
class Point:
    __slots__ = ("x", "y")   # Only these attributes allowed

    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(1, 2)
p.z = 3   # AttributeError! Not in __slots__

# Benefits:
# - ~30-40% less memory per instance
# - Slightly faster attribute access
# - Prevents accidental attribute creation

# With dataclasses (Python 3.10+)
@dataclass(slots=True)
class FastPoint:
    x: float
    y: float
```

---

## 23. Global Interpreter Lock (GIL) — Python-only Concept

The GIL is a mutex that protects Python's internal state. Only one thread can execute Python bytecode at a time. This means:

- **I/O-bound code**: Threads work fine. The GIL is released during I/O operations.
- **CPU-bound code**: Threads don't help. Use `multiprocessing` or C extensions.
- **asyncio**: Not affected by the GIL (single-threaded event loop).

This is why Python's concurrency story differs from JavaScript's. Node.js is single-threaded by design; Python is multi-threaded but with the GIL constraint.

---

## 24. Python Packaging and Distribution

### pyproject.toml (≈ package.json)

```toml
[project]
name = "mypackage"
version = "1.0.0"
description = "My awesome package"
requires-python = ">=3.10"
dependencies = [
    "requests>=2.28",
    "pydantic>=2.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
    "ruff>=0.1.0",
    "mypy>=1.0",
]

[project.scripts]
mycommand = "mypackage.cli:main"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.ruff]
line-length = 88

[tool.mypy]
strict = true

[tool.pytest.ini_options]
testpaths = ["tests"]
```

### Dependency Groups

```bash
# Install with uv
uv add requests                # Production dependency
uv add --dev pytest ruff       # Dev dependency

# Install with pip
pip install .                  # Production deps only
pip install ".[dev]"          # Include dev deps
```

---

## 25. Common Patterns and Idioms

### Pythonic vs Non-Pythonic

```python
# ❌ Non-Pythonic (JS habits)
for i in range(len(items)):
    print(items[i])

if len(items) > 0:
    process()

if x == True:
    do_thing()

result = []
for item in items:
    result.append(transform(item))

# ✅ Pythonic
for item in items:
    print(item)

if items:            # Truthy check
    process()

if x:                # Direct boolean check
    do_thing()

result = [transform(item) for item in items]   # Comprehension

# Swapping variables (no temp needed)
a, b = b, a

# Multiple assignment
x = y = z = 0

# Chained comparisons
if 0 < x < 100:     # Instead of: if x > 0 and x < 100
    ...

if a == b == c:      # Check all equal
    ...

# EAFP vs LBYL (Ask Forgiveness vs Look Before You Leap)
# JS habit (LBYL):
if key in dictionary:
    value = dictionary[key]
    process(value)

# Pythonic (EAFP):
try:
    value = dictionary[key]
except KeyError:
    handle_missing()
else:
    process(value)

# Or simply:
value = dictionary.get(key)
if value is not None:
    process(value)

# String joining
words = ["Hello", "World"]
" ".join(words)        # "Hello World"  — NOT " ".concat(words)

# Checking for None
if x is None:          # NOT if x == None
    ...

# Ternary in assignments
value = x if condition else default

# Any / All
if any(score > 90 for score in scores):
    print("Someone aced it")
if all(score >= 60 for score in scores):
    print("Everyone passed")

# zip for parallel iteration
for name, score in zip(names, scores):
    print(f"{name}: {score}")

# dict from pairs
pairs = [("a", 1), ("b", 2)]
d = dict(pairs)

# Reverse a sequence
reversed_list = lst[::-1]
for item in reversed(lst):
    print(item)

# Unpacking in loops
points = [(1, 2), (3, 4), (5, 6)]
for x, y in points:
    print(f"({x}, {y})")

# String multiplication
separator = "-" * 40    # "----------------------------------------"

# Conditional imports
try:
    import rapidjson as json
except ImportError:
    import json
```

---

## 26. Web Frameworks Quick Map

| JS/TS Framework | Python Equivalent | Notes |
|-----------------|-------------------|-------|
| Express.js | Flask | Lightweight, minimal |
| Fastify / Hono | FastAPI | Modern, async, auto-docs via type hints |
| Next.js / Nuxt | Django | Full-featured ("batteries included") |
| Koa | Starlette | Lightweight async (FastAPI is built on it) |
| Socket.io | python-socketio | WebSocket library |
| Prisma / TypeORM | SQLAlchemy / Django ORM | Database ORMs |
| Jest | pytest | Testing |
| Zod | Pydantic | Runtime validation |
| dotenv | python-dotenv | Environment variables |

### FastAPI Example (for Express.js Developers)

```python
# pip install fastapi uvicorn

from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float
    quantity: int = 0

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")
async def get_item(item_id: int, q: str | None = None):
    # item_id is path param (auto-validated as int)
    # q is query param (optional)
    return {"item_id": item_id, "q": q}

@app.post("/items/", status_code=201)
async def create_item(item: Item):
    # Request body auto-validated against Item model
    return item

# Run with: uvicorn main:app --reload
```

---

## 27. Key Libraries for JS/TS Developers

| Task | JS/TS Library | Python Library |
|------|--------------|----------------|
| HTTP requests | axios / fetch | requests / httpx |
| HTTP server | express / fastify | flask / fastapi / django |
| WebSockets | ws / socket.io | websockets / python-socketio |
| Database | prisma / knex / typeorm | sqlalchemy / django-orm / tortoise |
| Validation | zod / joi / yup | pydantic / marshmallow / attrs |
| CLI tools | commander / yargs | click / typer / argparse |
| Task queues | bull / agenda | celery / rq / huey |
| Data science | N/A | pandas / numpy / scipy |
| ML/AI | tensorflow.js | pytorch / tensorflow / scikit-learn |
| Web scraping | puppeteer / cheerio | beautifulsoup4 / scrapy / playwright |
| Image processing | sharp | Pillow (PIL) |
| Template engine | EJS / Handlebars | Jinja2 / Mako |
| Environment | dotenv | python-dotenv |
| Linting | eslint | ruff / flake8 |
| Formatting | prettier | ruff format / black |
| Type checking | tsc | mypy / pyright |
| Testing | jest / vitest | pytest |
| Mocking | jest.mock | unittest.mock / pytest-mock |
| Process manager | pm2 | supervisor / systemd |
| GraphQL | apollo | strawberry / graphene |
| Logging | winston / pino | logging (stdlib) / structlog / loguru |

---

## 28. Common Gotchas for JS/TS Developers

### 1. Mutable Default Arguments

```python
# BUG: Default list is shared across calls
def add_item(item, lst=[]):
    lst.append(item)
    return lst

add_item(1)    # [1]
add_item(2)    # [1, 2]  ← Unexpected!

# FIX:
def add_item(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

### 2. Late Binding Closures

```python
# BUG: All lambdas capture the same variable
funcs = [lambda: i for i in range(5)]
[f() for f in funcs]    # [4, 4, 4, 4, 4]  ← All return 4!

# FIX: Use default argument to capture current value
funcs = [lambda i=i: i for i in range(5)]
[f() for f in funcs]    # [0, 1, 2, 3, 4]
```

### 3. Integer Interning

```python
a = 256
b = 256
a is b     # True (CPython caches small integers -5 to 256)

a = 257
b = 257
a is b     # False (or True, depending on context!)

# RULE: ALWAYS use == for value comparison, `is` ONLY for None/singleton checks
```

### 4. String Immutability Traps

```python
# Strings are immutable — concatenation creates new objects
# This is O(n^2) for building strings in a loop
result = ""
for item in items:
    result += str(item) + ", "    # Bad! Creates new string each time

# Use join instead — O(n)
result = ", ".join(str(item) for item in items)
```

### 5. Shallow Copy Confusion

```python
import copy

original = [[1, 2], [3, 4]]
shallow = original[:]           # or list(original) or original.copy()
deep = copy.deepcopy(original)

shallow[0][0] = 99
original[0][0]  # 99 — shallow copy shares inner objects!

deep[0][0] = 99
original[0][0]  # Still 1 (if deepcopy was made before the change)
```

### 6. Scope Quirks

```python
# Variables in if/for/while are NOT block-scoped (unlike JS let/const)
for i in range(5):
    x = i

print(x)   # 4 — x is still accessible!
print(i)   # 4 — i is still accessible!

# This is very different from JS where let/const are block-scoped
```

### 7. `is` vs `==`

```python
# `is` checks identity (same object in memory)
# `==` checks equality (same value)
a = [1, 2, 3]
b = [1, 2, 3]
a == b     # True (same value)
a is b     # False (different objects)

# ONLY use `is` for: None, True, False, sentinel objects
if x is None:      # Correct
if x == None:      # Wrong (can be overridden by __eq__)
```

### 8. Tuple With One Element

```python
t = (42)     # This is int 42, NOT a tuple!
t = (42,)    # This is a tuple with one element
t = 42,      # Also a tuple (parentheses optional)
```

---

## 29. Performance Tips

```python
# Use built-in functions (implemented in C, much faster)
sum(numbers)                    # Faster than manual loop
max(numbers)
min(numbers)
sorted(items)
any(condition for x in items)
all(condition for x in items)

# Use sets for membership testing
if item in large_list:     # O(n) — slow
    ...
if item in large_set:      # O(1) — fast
    ...

# Use generators for large data
total = sum(x**2 for x in range(10**7))   # Doesn't create a list

# Use collections.deque for queue operations
from collections import deque
q = deque()
q.append(x)     # O(1)
q.popleft()     # O(1), vs list.pop(0) which is O(n)

# Use __slots__ for memory-heavy classes (covered earlier)

# Profile before optimizing
import cProfile
cProfile.run('my_function()')

# Line-level profiling
# pip install line-profiler
@profile
def my_function():
    ...

# timeit for micro-benchmarks
import timeit
timeit.timeit('sum(range(1000))', number=10000)

# For CPU-bound work, consider:
# - numpy/pandas for numerical work
# - multiprocessing for parallelism
# - Cython/mypyc for compilation
# - PyPy as an alternative runtime
```

---

## 30. Cheat Sheet: Quick Translation Table

| JS/TS | Python |
|-------|--------|
| `console.log(x)` | `print(x)` |
| `typeof x` | `type(x)` |
| `x instanceof Y` | `isinstance(x, Y)` |
| `JSON.stringify(x)` | `json.dumps(x)` |
| `JSON.parse(s)` | `json.loads(s)` |
| `Object.keys(o)` | `o.keys()` or `list(o.keys())` |
| `Object.values(o)` | `o.values()` or `list(o.values())` |
| `Object.entries(o)` | `o.items()` or `list(o.items())` |
| `Array.isArray(x)` | `isinstance(x, list)` |
| `x.toString()` | `str(x)` |
| `parseInt(s)` | `int(s)` |
| `parseFloat(s)` | `float(s)` |
| `Math.floor(x)` | `math.floor(x)` or `x // 1` |
| `Math.ceil(x)` | `math.ceil(x)` |
| `Math.round(x)` | `round(x)` |
| `Math.abs(x)` | `abs(x)` |
| `Math.max(a,b)` | `max(a, b)` |
| `Math.min(a,b)` | `min(a, b)` |
| `Math.random()` | `random.random()` |
| `Math.PI` | `math.pi` |
| `x.length` | `len(x)` |
| `arr.push(x)` | `lst.append(x)` |
| `arr.pop()` | `lst.pop()` |
| `arr.shift()` | `lst.pop(0)` |
| `arr.unshift(x)` | `lst.insert(0, x)` |
| `arr.concat(b)` | `lst + b` or `lst.extend(b)` |
| `arr.includes(x)` | `x in lst` |
| `arr.indexOf(x)` | `lst.index(x)` |
| `arr.slice(a,b)` | `lst[a:b]` |
| `arr.splice(i,n)` | `del lst[i:i+n]` |
| `arr.flat()` | `[x for sub in lst for x in sub]` |
| `arr.map(fn)` | `[fn(x) for x in lst]` |
| `arr.filter(fn)` | `[x for x in lst if fn(x)]` |
| `arr.reduce(fn,init)` | `functools.reduce(fn, lst, init)` |
| `arr.some(fn)` | `any(fn(x) for x in lst)` |
| `arr.every(fn)` | `all(fn(x) for x in lst)` |
| `arr.find(fn)` | `next((x for x in lst if fn(x)), None)` |
| `arr.sort((a,b)=>a-b)` | `lst.sort()` or `sorted(lst)` |
| `[...arr]` | `lst[:]` or `list(lst)` |
| `{...obj}` | `{**d}` or `dict(d)` |
| `new Set([1,2,3])` | `{1, 2, 3}` |
| `new Map()` | `{}` (dict) |
| `Promise.all([])` | `asyncio.gather()` |
| `setTimeout(fn, ms)` | `asyncio.sleep(s)` or `time.sleep(s)` |
| `setInterval(fn, ms)` | `while` loop with `sleep` |
| `try/catch/finally` | `try/except/finally` |
| `throw new Error()` | `raise Exception()` |
| `x ?? default` | `x if x is not None else default` |
| `x?.y` | `getattr(x, 'y', None)` |
| `x?.y?.z` | No direct equivalent; use try/except or getattr chains |
| `...rest` (params) | `*args, **kwargs` |
| `export default` | Module-level (everything is accessible) |
| `import x from 'y'` | `from y import x` |
| `require('y')` | `import y` |
| Template literal `` `${}` `` | f-string `f"{}"` |
| `null` / `undefined` | `None` |
| `=== / !==` | `== / !=` (Python has no loose equality) |
| `&&` / `||` / `!` | `and` / `or` / `not` |
| `true` / `false` | `True` / `False` |
| `class { #private }` | `class: _private` or `__mangled` |

---

*This guide covers the conceptual territory needed to become productive in Python as a JS/TS engineer. For continued learning, explore the official Python documentation (docs.python.org), work through real projects, and read well-maintained open-source Python codebases like FastAPI, httpx, or rich.*
