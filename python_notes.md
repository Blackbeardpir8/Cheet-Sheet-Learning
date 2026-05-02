# 🐍 Complete Python Notes — Beginner to Advanced

> **How to use:** Click any topic in the Table of Contents to jump directly to it.

---

## 📚 Table of Contents

1. [Python Fundamentals](#1-python-fundamentals)
2. [Variables and Data Types](#2-variables-and-data-types)
3. [Operators](#3-operators)
4. [Strings](#4-strings)
5. [Lists](#5-lists)
6. [Tuples](#6-tuples)
7. [Sets](#7-sets)
8. [Dictionaries](#8-dictionaries)
9. [Control Flow](#9-control-flow)
10. [Loops](#10-loops)
11. [Functions](#11-functions)
12. [Advanced Function Concepts](#12-advanced-function-concepts)
13. [Comprehensions](#13-comprehensions)
14. [Iterators and Generators](#14-iterators-and-generators)
15. [Exception Handling](#15-exception-handling)
16. [Modules and Packages](#16-modules-and-packages)
17. [File Handling](#17-file-handling)
18. [Object-Oriented Programming (OOP)](#18-object-oriented-programming-oop)
19. [OOP Concepts](#19-oop-concepts)
20. [Dunder (Magic) Methods](#20-dunder-magic-methods)
21. [Advanced OOP](#21-advanced-oop)
22. [Memory Management](#22-memory-management)
23. [Python Internals](#23-python-internals)
24. [Functional Programming](#24-functional-programming)
25. [Decorators Deep Dive](#25-decorators-deep-dive)
26. [Context Managers](#26-context-managers)
27. [Itertools and Collections](#27-itertools-and-collections)
28. [Regular Expressions](#28-regular-expressions)
29. [Date and Time](#29-date-and-time)
30. [Serialization](#30-serialization)
31. [Virtual Environments and Packaging](#31-virtual-environments-and-packaging)
32. [Testing](#32-testing)
33. [Logging and Debugging](#33-logging-and-debugging)
34. [Concurrency and Parallelism](#34-concurrency-and-parallelism)
35. [Async Programming](#35-async-programming)
36. [Advanced Python Features](#36-advanced-python-features)

---

## 1. Python Fundamentals

### What is Python?

- Python is a **general-purpose programming language** — you can use it to build websites, analyze data, create AI, automate tasks, and much more.
- It reads almost like plain English, which makes it beginner-friendly.
- Created by **Guido van Rossum** in 1991.

```python
print("Hello, World!")  # Your first Python program
```

---

### Features of Python

- **Easy to read and write** — clean, English-like syntax
- **Free and open source** — anyone can use and modify it
- **Cross-platform** — runs on Windows, Mac, Linux
- **Large standard library** — comes with many built-in tools
- **Dynamically typed** — no need to declare variable types
- **Supports multiple paradigms** — OOP, functional, procedural

---

### Interpreted vs Compiled

| Feature | Interpreted (Python) | Compiled (C, Java) |
|---|---|---|
| Execution | Line by line | Whole program at once |
| Speed | Slower | Faster |
| Ease | Easier to debug | Harder to debug |
| Output | No separate file needed | Creates executable |

- **Python is interpreted** — each line runs as it is read.

---

### Python Execution Model

```
Your Code (.py file)
       ↓
  Python Interpreter
       ↓
  Bytecode (.pyc file)
       ↓
  Python Virtual Machine (PVM)
       ↓
  Output / Result
```

---

### Python Virtual Machine (PVM)

- The PVM is the **engine** that actually runs your Python code.
- It reads bytecode (a simplified version of your code) and executes it.
- You don't interact with it directly — Python handles this automatically.

---

### Keywords and Identifiers

- **Keywords** are reserved words that Python uses for its own purposes. You cannot use them as variable names.

```python
# Examples of keywords:
# if, else, while, for, def, class, return, import, True, False, None, and, or, not, in, is
```

- **Identifiers** are names you give to variables, functions, classes, etc.

```python
name = "Alice"     # 'name' is an identifier
age = 25           # 'age' is an identifier
```

**Rules for identifiers:**
- Can contain letters, numbers, and underscore `_`
- Cannot start with a number
- Case-sensitive (`Name` and `name` are different)
- Cannot be a keyword

---

### Indentation and Syntax Rules

- Python uses **indentation (spaces/tabs)** to define blocks of code instead of `{}` like other languages.
- Standard: **4 spaces per indentation level**

```python
# Correct indentation
if True:
    print("This is inside the if block")
    print("Still inside")
print("This is outside")

# Wrong — will cause IndentationError
if True:
print("Missing indentation")  # ❌ Error
```

---

### Comments and Docstrings

```python
# This is a single-line comment

"""
This is a multi-line comment
also called a docstring
"""

def greet():
    """This function prints a greeting."""  # Docstring for a function
    print("Hello!")
```

- **Comments** are ignored by Python — they're for humans to read.
- **Docstrings** document what a function/class does and can be accessed with `help()`.

---

### Dynamic Typing

- In Python, you **don't need to declare** the type of a variable.
- Python figures it out automatically.

```python
x = 10        # Python knows this is an int
x = "Hello"   # Now x is a string — that's fine!
x = 3.14      # Now it's a float
```

**Tip:** Dynamic typing is flexible but can cause bugs if not careful. Always know what type your variable holds.

---

**Best Practices:**
- Always use 4 spaces for indentation (never mix tabs and spaces)
- Write comments to explain *why*, not *what* the code does
- Use descriptive variable names: `user_age` instead of `x`

---

## 2. Variables and Data Types

### Variables and Assignment

- A **variable** is a name that stores a value in memory.
- Think of it as a labeled box that holds something.

```python
name = "Alice"   # String
age = 25         # Integer
height = 5.6     # Float
is_student = True  # Boolean
```

---

### Multiple Assignment

```python
# Assign same value to multiple variables
x = y = z = 0

# Assign different values in one line
a, b, c = 1, 2, 3
print(a, b, c)  # 1 2 3

# Swap values easily
a, b = b, a
print(a, b)  # 2 1
```

---

### Naming Conventions

| Style | Used For | Example |
|---|---|---|
| `snake_case` | Variables, functions | `user_name`, `get_age()` |
| `PascalCase` | Classes | `MyClass`, `BankAccount` |
| `UPPER_CASE` | Constants | `MAX_SIZE = 100` |
| `_single_underscore` | Private (by convention) | `_helper()` |
| `__double_underscore` | Name mangling in classes | `__secret` |

---

### Type Checking

```python
x = 42
print(type(x))          # <class 'int'>
print(isinstance(x, int))  # True
print(isinstance(x, str))  # False
```

---

### Built-in Data Types

#### `int` — Whole Numbers

```python
a = 10
b = -5
c = 1_000_000  # Underscores for readability
print(a + b)   # 5
```

#### `float` — Decimal Numbers

```python
pi = 3.14159
temp = -2.5
print(round(pi, 2))  # 3.14
```

#### `complex` — Complex Numbers

```python
z = 3 + 4j
print(z.real)   # 3.0
print(z.imag)   # 4.0
```

#### `bool` — True or False

```python
is_active = True
is_deleted = False
print(int(True))   # 1
print(int(False))  # 0
```

#### `str` — Text

```python
greeting = "Hello"
name = 'Alice'
multi = """This is
a multi-line string"""
```

#### `list` — Ordered, Changeable Collection

```python
fruits = ["apple", "banana", "cherry"]
fruits.append("mango")
print(fruits[0])  # apple
```

#### `tuple` — Ordered, Unchangeable Collection

```python
coordinates = (10, 20)
print(coordinates[0])  # 10
```

#### `set` — Unordered, Unique Items

```python
colors = {"red", "blue", "red"}  # Duplicates removed
print(colors)  # {'red', 'blue'}
```

#### `dict` — Key-Value Pairs

```python
person = {"name": "Alice", "age": 25}
print(person["name"])  # Alice
```

#### `NoneType` — Represents "Nothing"

```python
result = None
print(result is None)  # True
```

---

### Mutable vs Immutable Types

| Type | Mutable? |
|---|---|
| `list` | ✅ Yes |
| `dict` | ✅ Yes |
| `set` | ✅ Yes |
| `int`, `float`, `bool` | ❌ No |
| `str` | ❌ No |
| `tuple` | ❌ No |

```python
# Mutable — can be changed
my_list = [1, 2, 3]
my_list[0] = 99  # Works fine

# Immutable — cannot be changed
my_str = "hello"
my_str[0] = "H"  # ❌ TypeError
```

---

### Type Casting

```python
# Convert between types
int("42")       # 42
float("3.14")   # 3.14
str(100)        # "100"
bool(0)         # False
bool(1)         # True
list("abc")     # ['a', 'b', 'c']
```

**Tip:** `bool()` returns `False` for: `0`, `""`, `[]`, `{}`, `None`. Everything else is `True`.

---

**Best Practices:**
- Use `None` instead of empty strings or `0` to represent "no value"
- Prefer `isinstance()` over `type()` for type checking
- Be careful with floats: `0.1 + 0.2 != 0.3` due to floating-point precision

---

## 3. Operators

### Arithmetic Operators

```python
a, b = 10, 3

print(a + b)   # 13  — Addition
print(a - b)   # 7   — Subtraction
print(a * b)   # 30  — Multiplication
print(a / b)   # 3.333... — Division (always float)
print(a // b)  # 3   — Floor Division (rounds down)
print(a % b)   # 1   — Modulo (remainder)
print(a ** b)  # 1000 — Exponentiation (power)
```

---

### Comparison Operators

```python
print(5 == 5)   # True  — Equal
print(5 != 3)   # True  — Not equal
print(5 > 3)    # True  — Greater than
print(5 < 3)    # False — Less than
print(5 >= 5)   # True  — Greater than or equal
print(5 <= 4)   # False — Less than or equal
```

---

### Logical Operators

```python
print(True and False)  # False — Both must be True
print(True or False)   # True  — At least one must be True
print(not True)        # False — Reverses the value
```

---

### Bitwise Operators

```python
a = 5   # 0101 in binary
b = 3   # 0011 in binary

print(a & b)   # 1  — AND
print(a | b)   # 7  — OR
print(a ^ b)   # 6  — XOR
print(~a)      # -6 — NOT
print(a << 1)  # 10 — Left shift
print(a >> 1)  # 2  — Right shift
```

---

### Assignment Operators

```python
x = 10
x += 5   # x = x + 5  → 15
x -= 3   # x = x - 3  → 12
x *= 2   # x = x * 2  → 24
x //= 5  # x = x // 5 → 4
x **= 2  # x = x ** 2 → 16
x %= 5   # x = x % 5  → 1
```

---

### Membership Operators

```python
fruits = ["apple", "banana"]
print("apple" in fruits)      # True
print("grape" not in fruits)  # True
```

---

### Identity Operators

```python
a = [1, 2, 3]
b = a
c = [1, 2, 3]

print(a is b)   # True  — same object in memory
print(a is c)   # False — same value, different object
print(a == c)   # True  — same value
```

**Tip:** Use `is` to check for `None`: `if x is None:` not `if x == None:`

---

### Operator Precedence (High → Low)

1. `**` (exponentiation)
2. `+x`, `-x`, `~x` (unary)
3. `*`, `/`, `//`, `%`
4. `+`, `-`
5. `<<`, `>>`
6. `&`
7. `^`
8. `|`
9. Comparisons: `==`, `!=`, `<`, `>`, etc.
10. `not`
11. `and`
12. `or`

```python
# Use parentheses for clarity
result = 2 + 3 * 4    # 14 (multiplication first)
result = (2 + 3) * 4  # 20 (parentheses first)
```

---

## 4. Strings

### String Creation

```python
s1 = "Hello"
s2 = 'World'
s3 = """Multi
line"""
s4 = str(42)  # Convert to string
```

---

### Indexing and Slicing

```python
s = "Python"
#    P y t h o n
#    0 1 2 3 4 5   (positive index)
#   -6-5-4-3-2-1   (negative index)

print(s[0])    # P
print(s[-1])   # n
print(s[1:4])  # yth  (start:stop — stop is excluded)
print(s[::2])  # Pto  (every 2nd character)
print(s[::-1]) # nohtyP  (reversed)
```

---

### String Immutability

```python
s = "hello"
s[0] = "H"  # ❌ TypeError — strings cannot be changed

# Instead, create a new string:
s = "H" + s[1:]  # "Hello"
```

---

### String Methods

```python
s = "  Hello, World!  "

print(s.strip())         # "Hello, World!" — removes spaces
print(s.lower())         # "  hello, world!  "
print(s.upper())         # "  HELLO, WORLD!  "
print(s.replace("World", "Python"))  # "  Hello, Python!  "
print(s.split(","))      # ['  Hello', ' World!  ']
print("hello".capitalize())  # "Hello"
print("hello".startswith("he"))  # True
print("hello".endswith("lo"))    # True
print("hello".find("ll"))        # 2 (index of first match)
print("hello".count("l"))        # 2
print(",".join(["a", "b", "c"]))  # "a,b,c"
print("hello".isalpha())  # True (all letters)
print("123".isdigit())    # True (all digits)
```

---

### String Formatting

```python
name = "Alice"
age = 25

# Method 1: % formatting (old style)
print("Name: %s, Age: %d" % (name, age))

# Method 2: str.format()
print("Name: {}, Age: {}".format(name, age))
print("Name: {n}, Age: {a}".format(n=name, a=age))

# Method 3: f-strings (recommended — Python 3.6+)
print(f"Name: {name}, Age: {age}")
print(f"Next year: {age + 1}")
print(f"Pi: {3.14159:.2f}")  # 2 decimal places
```

---

### Escape Characters

```python
print("Hello\nWorld")   # newline
print("Hello\tWorld")   # tab
print("He said \"Hi\"") # double quotes inside string
print("C:\\Users\\")    # backslash
```

---

### Raw Strings

```python
# Raw strings ignore escape characters
path = r"C:\Users\Alice\Documents"
print(path)  # C:\Users\Alice\Documents (not interpreted)
```

---

### Encoding and Decoding

```python
s = "hello"
encoded = s.encode("utf-8")   # b'hello' — bytes
decoded = encoded.decode("utf-8")  # "hello" — back to string
```

---

**Best Practices:**
- Use f-strings for formatting — they're the fastest and most readable
- Use `.strip()` when reading user input to remove accidental spaces
- Use `.lower()` when comparing strings to avoid case-sensitivity issues

---

## 5. Lists

### List Creation

```python
empty = []
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", True, 3.14]
from_range = list(range(5))  # [0, 1, 2, 3, 4]
```

---

### Indexing and Slicing

```python
fruits = ["apple", "banana", "cherry", "mango"]

print(fruits[0])     # apple
print(fruits[-1])    # mango
print(fruits[1:3])   # ['banana', 'cherry']
print(fruits[::-1])  # reversed list
```

---

### List Methods

```python
nums = [3, 1, 4, 1, 5]

nums.append(9)       # Add to end → [3, 1, 4, 1, 5, 9]
nums.insert(0, 0)    # Insert at index 0
nums.remove(1)       # Remove first occurrence of 1
nums.pop()           # Remove and return last item
nums.pop(0)          # Remove and return item at index 0
nums.sort()          # Sort in place
nums.reverse()       # Reverse in place
print(nums.index(4)) # Find index of value 4
print(nums.count(1)) # Count occurrences of 1
nums.extend([7, 8])  # Add multiple items
nums.clear()         # Remove all items
copy = nums.copy()   # Shallow copy
```

---

### Nested Lists

```python
matrix = [[1, 2, 3],
          [4, 5, 6],
          [7, 8, 9]]

print(matrix[1][2])  # 6 (row 1, column 2)
```

---

### List Comprehensions

```python
# Basic: [expression for item in iterable]
squares = [x**2 for x in range(6)]
# [0, 1, 4, 9, 16, 25]

# With condition
evens = [x for x in range(10) if x % 2 == 0]
# [0, 2, 4, 6, 8]
```

---

### Copying Lists

```python
original = [1, 2, [3, 4]]

# Shallow copy — nested objects still shared
shallow = original.copy()
shallow = original[:]
shallow = list(original)

# Deep copy — fully independent copy
import copy
deep = copy.deepcopy(original)
```

**Tip:** If your list has nested lists, always use `deepcopy` to avoid surprise changes.

---

**Best Practices:**
- Use list comprehensions instead of loops for simple transformations
- Use `append()` to add items, not `+` (which creates a new list each time)
- Check membership with `in`: `if "apple" in fruits:`

---

## 6. Tuples

### Tuple Creation

```python
empty = ()
single = (42,)        # Note the comma — required for single item!
coords = (10, 20)
mixed = (1, "hello", True)
packed = 1, 2, 3      # Parentheses are optional
```

---

### Tuple Immutability

```python
t = (1, 2, 3)
t[0] = 99  # ❌ TypeError — tuples cannot be changed
```

---

### Packing and Unpacking

```python
# Packing
point = 3, 5

# Unpacking
x, y = point
print(x, y)  # 3 5

# Extended unpacking
first, *rest = (1, 2, 3, 4, 5)
print(first)  # 1
print(rest)   # [2, 3, 4, 5]

# Swap variables
a, b = 1, 2
a, b = b, a
```

---

### Tuple Methods

```python
t = (1, 2, 2, 3)
print(t.count(2))  # 2
print(t.index(3))  # 3
```

---

**When to use tuples vs lists:**
- Use **tuples** for data that should not change (e.g., coordinates, RGB colors, database records)
- Use **lists** for data that you'll modify (e.g., a shopping cart)
- Tuples are slightly faster than lists

---

## 7. Sets

### Set Creation

```python
empty = set()         # NOT {} — that creates a dict!
colors = {"red", "blue", "green"}
from_list = set([1, 2, 2, 3])  # {1, 2, 3} — duplicates removed
```

---

### Set Properties

- **Unordered** — no indexing (can't do `s[0]`)
- **Unique** — no duplicate values
- **Mutable** — can add/remove items

---

### Set Methods

```python
s = {1, 2, 3}

s.add(4)          # Add one item
s.remove(2)       # Remove item (error if not found)
s.discard(99)     # Remove item (no error if not found)
s.pop()           # Remove and return arbitrary item
s.clear()         # Remove all items
print(len(s))     # Number of items
print(3 in s)     # Membership check — very fast!
```

---

### Set Operations

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

print(a | b)          # {1,2,3,4,5,6} — Union
print(a & b)          # {3, 4} — Intersection
print(a - b)          # {1, 2} — Difference (in a but not b)
print(a ^ b)          # {1,2,5,6} — Symmetric difference

print(a.union(b))
print(a.intersection(b))
print(a.difference(b))

print({1,2}.issubset({1,2,3}))   # True
print({1,2,3}.issuperset({1,2})) # True
```

---

### Frozen Sets

```python
# Immutable version of a set
fs = frozenset([1, 2, 3])
fs.add(4)  # ❌ AttributeError — can't modify
```

---

**Best Practices:**
- Use sets for fast membership checks — `in` on a set is O(1), on a list is O(n)
- Use sets to remove duplicates: `list(set(my_list))`

---

## 8. Dictionaries

### Dictionary Creation

```python
empty = {}
person = {"name": "Alice", "age": 25}
from_keys = dict.fromkeys(["a", "b"], 0)  # {'a': 0, 'b': 0}
constructed = dict(name="Bob", age=30)
```

---

### Keys and Values

```python
d = {"name": "Alice", "age": 25, "city": "Delhi"}

print(d["name"])           # Alice
print(d.get("age"))        # 25
print(d.get("phone", "N/A"))  # N/A (default if key missing)
```

---

### Dictionary Methods

```python
d = {"a": 1, "b": 2, "c": 3}

print(d.keys())    # dict_keys(['a', 'b', 'c'])
print(d.values())  # dict_values([1, 2, 3])
print(d.items())   # dict_items([('a',1), ('b',2), ('c',3)])

d["d"] = 4          # Add new key
d.update({"e": 5})  # Add/update multiple keys
del d["a"]          # Delete a key
popped = d.pop("b") # Remove and return value
d.setdefault("f", 0)  # Set only if key doesn't exist
d.clear()           # Remove all items
```

---

### Iteration Over Dict

```python
person = {"name": "Alice", "age": 25}

for key in person:
    print(key)

for key, value in person.items():
    print(f"{key}: {value}")

for value in person.values():
    print(value)
```

---

### Nested Dictionaries

```python
students = {
    "Alice": {"age": 20, "grade": "A"},
    "Bob":   {"age": 22, "grade": "B"},
}

print(students["Alice"]["grade"])  # A
```

---

### Dictionary Comprehensions

```python
squares = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

filtered = {k: v for k, v in squares.items() if v > 4}
# {3: 9, 4: 16}
```

---

**Best Practices:**
- Always use `.get()` instead of `[]` when a key might not exist
- Use `dict.items()` when you need both key and value in loops
- Dictionaries maintain insertion order (Python 3.7+)

---

## 9. Control Flow

### `if` Statement

```python
x = 10
if x > 5:
    print("x is greater than 5")
```

---

### `if-else`

```python
age = 17
if age >= 18:
    print("Adult")
else:
    print("Minor")
```

---

### `if-elif-else`

```python
score = 75

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"

print(f"Grade: {grade}")  # Grade: C
```

---

### Nested Conditions

```python
x, y = 5, 10

if x > 0:
    if y > 0:
        print("Both positive")
    else:
        print("Only x is positive")
```

---

### Ternary (One-line if-else)

```python
age = 20
status = "Adult" if age >= 18 else "Minor"
print(status)  # Adult
```

---

### `match-case` (Python 3.10+)

- Python's version of `switch-case` from other languages.

```python
day = "Monday"

match day:
    case "Monday" | "Tuesday" | "Wednesday" | "Thursday" | "Friday":
        print("Weekday")
    case "Saturday" | "Sunday":
        print("Weekend")
    case _:
        print("Unknown day")
```

---

**Best Practices:**
- Keep conditions simple — complex conditions should be extracted into variables or functions
- Use ternary for simple one-liners, not complex logic
- Avoid deep nesting (more than 2-3 levels) — refactor with functions

---

## 10. Loops

### `for` Loop

```python
# Loop over a range
for i in range(5):
    print(i)   # 0 1 2 3 4

# Loop over a list
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# With index using enumerate()
for index, fruit in enumerate(fruits):
    print(index, fruit)  # 0 apple, 1 banana, ...

# Loop over a dict
person = {"name": "Alice", "age": 25}
for key, value in person.items():
    print(f"{key}: {value}")
```

---

### `while` Loop

```python
count = 0
while count < 5:
    print(count)
    count += 1
```

---

### Nested Loops

```python
for i in range(3):
    for j in range(3):
        print(f"({i},{j})", end=" ")
    print()  # New line after each row
```

---

### Loop Control Statements

```python
# break — exit the loop immediately
for i in range(10):
    if i == 5:
        break
    print(i)  # 0 1 2 3 4

# continue — skip current iteration, go to next
for i in range(10):
    if i % 2 == 0:
        continue
    print(i)  # 1 3 5 7 9

# pass — do nothing (placeholder)
for i in range(5):
    pass  # Does nothing — used when code is not yet written
```

---

### `else` with Loops

```python
# else runs only if the loop completed WITHOUT a break
for i in range(5):
    if i == 10:
        break
else:
    print("Loop completed without break")  # This prints

# Example: Search
for num in [1, 2, 3, 4, 5]:
    if num == 6:
        print("Found!")
        break
else:
    print("Not found")  # Prints because 6 was not in list
```

---

**Best Practices:**
- Use `enumerate()` instead of `range(len(list))`
- Use `zip()` to iterate two lists together: `for a, b in zip(list1, list2):`
- Avoid modifying a list while iterating over it

---

## 11. Functions

### Function Definition

```python
def greet():
    print("Hello!")

greet()  # Call the function
```

---

### Parameters and Arguments

```python
# Positional arguments
def add(a, b):
    return a + b

print(add(3, 5))  # 8

# Keyword arguments
print(add(b=5, a=3))  # 8

# Default parameters
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Alice")           # Hello, Alice!
greet("Bob", "Hi")       # Hi, Bob!

# Variable-length arguments (*args)
def total(*args):
    return sum(args)

print(total(1, 2, 3, 4))  # 10

# Keyword variable-length (**kwargs)
def profile(**kwargs):
    for k, v in kwargs.items():
        print(f"{k}: {v}")

profile(name="Alice", age=25, city="Delhi")
```

---

### Return Statement

```python
def square(x):
    return x * x

result = square(5)  # 25

# Return multiple values (as a tuple)
def min_max(lst):
    return min(lst), max(lst)

low, high = min_max([3, 1, 4, 1, 5])
print(low, high)  # 1 5
```

---

### Recursion

```python
# A function that calls itself
def factorial(n):
    if n == 0:
        return 1          # Base case — stops the recursion
    return n * factorial(n - 1)  # Recursive case

print(factorial(5))  # 120
```

---

### Lambda Functions

```python
# Anonymous one-line functions
square = lambda x: x ** 2
print(square(4))  # 16

# Often used with sorted, map, filter
nums = [3, 1, 4, 1, 5]
nums.sort(key=lambda x: -x)  # Sort descending
print(nums)  # [5, 4, 3, 1, 1]
```

---

### Type Hints (Annotations)

```python
def add(a: int, b: int) -> int:
    return a + b

def greet(name: str) -> None:
    print(f"Hello, {name}")
```

---

**Best Practices:**
- Keep functions small — one function, one job
- Use type hints for clarity
- Always define default parameters after positional ones
- Never use a mutable default: `def f(lst=[])` — use `def f(lst=None): if lst is None: lst = []`

---

## 12. Advanced Function Concepts

### First-Class Functions

- In Python, functions are objects — you can store them in variables, pass them as arguments, return them.

```python
def greet():
    return "Hello"

say = greet         # Assign function to a variable
print(say())        # Hello

def apply(func):
    return func()   # Pass function as argument

print(apply(greet)) # Hello
```

---

### Higher-Order Functions

```python
# Functions that take or return other functions

def multiply_by(n):
    def multiplier(x):
        return x * n
    return multiplier  # Return a function

double = multiply_by(2)
triple = multiply_by(3)
print(double(5))  # 10
print(triple(5))  # 15
```

---

### Closures

- A closure is a function that **remembers** variables from its enclosing scope even after that scope has finished.

```python
def counter():
    count = 0
    def increment():
        nonlocal count
        count += 1
        return count
    return increment

c = counter()
print(c())  # 1
print(c())  # 2
print(c())  # 3
```

---

### Decorators

- A decorator is a function that **wraps another function** to add behavior.

```python
def my_decorator(func):
    def wrapper():
        print("Before the function")
        func()
        print("After the function")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
# Before the function
# Hello!
# After the function
```

---

### Function Caching (`lru_cache`)

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(50))  # Very fast due to caching
```

---

### Partial Functions

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)

print(square(5))  # 25
print(cube(3))    # 27
```

---

## 13. Comprehensions

### List Comprehensions

```python
# [expression for item in iterable if condition]
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
words = [w.upper() for w in ["hi", "hello"]]
```

---

### Set Comprehensions

```python
unique_squares = {x**2 for x in [-2, -1, 0, 1, 2]}
# {0, 1, 4}
```

---

### Dictionary Comprehensions

```python
word_lengths = {word: len(word) for word in ["apple", "banana"]}
# {'apple': 5, 'banana': 6}
```

---

### Generator Expressions

```python
# Like list comprehensions but lazy — doesn't create the full list in memory
gen = (x**2 for x in range(10))
print(next(gen))  # 0
print(next(gen))  # 1

# Efficient for large data
total = sum(x**2 for x in range(1_000_000))
```

---

### Nested Comprehensions

```python
# Flatten a matrix
matrix = [[1,2,3],[4,5,6],[7,8,9]]
flat = [num for row in matrix for num in row]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

---

**Best Practices:**
- Keep comprehensions simple — if they're hard to read, use a regular loop
- Use generator expressions instead of list comprehensions when you only need to iterate once

---

## 14. Iterators and Generators

### Iterables vs Iterators

- **Iterable**: any object you can loop over (list, string, dict, range)
- **Iterator**: an object that remembers where it is in the loop; has `__next__()`

```python
my_list = [1, 2, 3]
it = iter(my_list)   # Create iterator from iterable
print(next(it))      # 1
print(next(it))      # 2
print(next(it))      # 3
# next(it)           # StopIteration error
```

---

### Custom Iterators

```python
class Countdown:
    def __init__(self, start):
        self.start = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.start <= 0:
            raise StopIteration
        self.start -= 1
        return self.start + 1

for n in Countdown(5):
    print(n)  # 5 4 3 2 1
```

---

### Generators

- **Generators** use `yield` to produce values one at a time without storing all in memory.

```python
def count_up(limit):
    n = 1
    while n <= limit:
        yield n
        n += 1

for num in count_up(5):
    print(num)  # 1 2 3 4 5
```

---

### Generator Chaining

```python
def evens(limit):
    for i in range(limit):
        if i % 2 == 0:
            yield i

def double(gen):
    for x in gen:
        yield x * 2

result = double(evens(10))
print(list(result))  # [0, 4, 8, 12, 16]
```

---

### Lazy Evaluation

- Generators compute values **on demand** — great for large or infinite sequences.

```python
def infinite_counter():
    n = 0
    while True:
        yield n
        n += 1

gen = infinite_counter()
print(next(gen))  # 0
print(next(gen))  # 1
# This runs forever but only computes when you call next()
```

---

## 15. Exception Handling

### `try` and `except`

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero!")
```

---

### `else` and `finally`

```python
try:
    result = 10 / 2
except ZeroDivisionError:
    print("Error!")
else:
    print(f"Result: {result}")  # Runs if no exception
finally:
    print("This ALWAYS runs")    # Cleanup code goes here
```

---

### Multiple Exceptions

```python
try:
    x = int(input("Enter number: "))
    result = 10 / x
except ValueError:
    print("That's not a number!")
except ZeroDivisionError:
    print("Cannot divide by zero!")
except (TypeError, OverflowError) as e:
    print(f"Other error: {e}")
```

---

### Custom Exceptions

```python
class AgeError(Exception):
    def __init__(self, message="Age must be positive"):
        super().__init__(message)

def set_age(age):
    if age < 0:
        raise AgeError("Age cannot be negative")
    return age

try:
    set_age(-5)
except AgeError as e:
    print(e)  # Age cannot be negative
```

---

### Raising Exceptions

```python
def divide(a, b):
    if b == 0:
        raise ValueError("b cannot be zero")
    return a / b
```

---

### Exception Hierarchy

```
BaseException
├── SystemExit
├── KeyboardInterrupt
└── Exception
    ├── ValueError
    ├── TypeError
    ├── NameError
    ├── IndexError
    ├── KeyError
    ├── ZeroDivisionError
    ├── FileNotFoundError
    ├── AttributeError
    └── ... (many more)
```

---

**Best Practices:**
- Always catch specific exceptions, not bare `except:`
- Use `finally` for cleanup (closing files, connections)
- Create custom exceptions for domain-specific errors
- Don't silence exceptions — at least log them

---

## 16. Modules and Packages

### Importing Modules

```python
import math
print(math.pi)       # 3.14159...
print(math.sqrt(16)) # 4.0

from math import pi, sqrt
print(pi)     # 3.14159...
print(sqrt(9)) # 3.0

import math as m
print(m.ceil(4.2))  # 5
```

---

### Built-in Modules

```python
import os           # OS operations
import sys          # System-specific parameters
import math         # Math functions
import random       # Random numbers
import datetime     # Date and time
import json         # JSON handling
import re           # Regular expressions
import collections  # Specialized data structures
import itertools    # Efficient looping tools
```

---

### Creating Your Own Module

```python
# File: mymath.py
def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

PI = 3.14159

# File: main.py
import mymath
print(mymath.add(3, 4))  # 7
print(mymath.PI)          # 3.14159
```

---

### Packages

- A **package** is a folder containing multiple modules and an `__init__.py` file.

```
mypackage/
├── __init__.py     ← Makes it a package
├── math_utils.py
└── string_utils.py
```

```python
from mypackage import math_utils
from mypackage.string_utils import format_name
```

---

### `__name__ == "__main__"`

```python
# mymodule.py
def greet():
    print("Hello!")

if __name__ == "__main__":
    greet()  # Only runs when this file is executed directly
             # NOT when it's imported
```

---

## 17. File Handling

### Opening Files

```python
# Always use 'with' — it automatically closes the file
with open("file.txt", "r") as f:
    content = f.read()
```

---

### File Modes

| Mode | Meaning |
|---|---|
| `"r"` | Read (default) |
| `"w"` | Write (overwrites existing content) |
| `"a"` | Append (adds to end of file) |
| `"x"` | Create (fails if file exists) |
| `"rb"` | Read binary |
| `"wb"` | Write binary |

---

### Reading and Writing

```python
# Writing
with open("notes.txt", "w") as f:
    f.write("Hello, World!\n")
    f.write("Second line\n")

# Reading all at once
with open("notes.txt", "r") as f:
    content = f.read()

# Reading line by line (memory efficient)
with open("notes.txt", "r") as f:
    for line in f:
        print(line.strip())

# Read all lines into a list
with open("notes.txt", "r") as f:
    lines = f.readlines()
```

---

### File Operations

```python
import os

os.rename("old.txt", "new.txt")   # Rename
os.remove("file.txt")             # Delete
os.path.exists("file.txt")        # Check if exists
os.path.getsize("file.txt")       # File size in bytes
os.listdir(".")                   # List directory contents
os.makedirs("my/folder", exist_ok=True)  # Create folders
```

---

**Best Practices:**
- Always use `with` statement — never manually call `.close()`
- Use `encoding="utf-8"` to handle international characters: `open("file.txt", "r", encoding="utf-8")`
- For large files, read line by line instead of `.read()` all at once

---

## 18. Object-Oriented Programming (OOP)

### Classes and Objects

- A **class** is a blueprint. An **object** is an instance of that blueprint.

```python
class Dog:
    # Class variable (shared by all instances)
    species = "Canis familiaris"

    # Constructor — runs when object is created
    def __init__(self, name, age):
        # Instance variables (unique to each object)
        self.name = name
        self.age = age

    # Instance method
    def bark(self):
        return f"{self.name} says: Woof!"

# Create objects (instances)
dog1 = Dog("Rex", 3)
dog2 = Dog("Buddy", 5)

print(dog1.bark())       # Rex says: Woof!
print(dog2.name)         # Buddy
print(Dog.species)       # Canis familiaris
```

---

### Instance vs Class Variables

```python
class Counter:
    count = 0  # Class variable — shared by all

    def __init__(self):
        Counter.count += 1
        self.id = Counter.count  # Instance variable — unique

c1 = Counter()
c2 = Counter()
print(Counter.count)  # 2
print(c1.id)          # 1
print(c2.id)          # 2
```

---

### The `self` Keyword

- `self` refers to the current object (instance).
- It must be the first parameter of every instance method.
- Python passes it automatically — you don't include it when calling.

```python
class Cat:
    def __init__(self, name):
        self.name = name   # self.name stores the name for THIS cat

    def meow(self):
        print(f"{self.name} says meow!")

my_cat = Cat("Whiskers")
my_cat.meow()  # Whiskers says meow!
```

---

## 19. OOP Concepts

### Encapsulation

- **Hide internal data** and only expose what's needed.

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # Private — double underscore

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount

    def get_balance(self):
        return self.__balance

acc = BankAccount(1000)
acc.deposit(500)
print(acc.get_balance())  # 1500
# print(acc.__balance)    # AttributeError — can't access directly
```

---

### Abstraction

- **Show only what's necessary**, hide the complex implementation.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass   # Must be implemented by subclass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius ** 2

c = Circle(5)
print(c.area())  # 78.5
```

---

### Inheritance

```python
# Single Inheritance
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "..."

class Dog(Animal):         # Dog inherits from Animal
    def speak(self):
        return f"{self.name} says Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"

d = Dog("Rex")
print(d.speak())   # Rex says Woof!
```

```python
# Multilevel Inheritance
class Vehicle:
    def move(self): return "Moving"

class Car(Vehicle):
    def honk(self): return "Beep"

class ElectricCar(Car):
    def charge(self): return "Charging"

ec = ElectricCar()
print(ec.move())    # Moving  (from Vehicle)
print(ec.honk())    # Beep    (from Car)
print(ec.charge())  # Charging (own method)
```

```python
# Multiple Inheritance
class Flyable:
    def fly(self): return "Flying"

class Swimmable:
    def swim(self): return "Swimming"

class Duck(Flyable, Swimmable):
    pass

d = Duck()
print(d.fly())   # Flying
print(d.swim())  # Swimming
```

---

### Polymorphism

```python
# Method Overriding — child redefines parent's method
class Animal:
    def speak(self):
        return "Some sound"

class Dog(Animal):
    def speak(self):          # Override
        return "Woof!"

class Cat(Animal):
    def speak(self):          # Override
        return "Meow!"

animals = [Dog(), Cat(), Animal()]
for animal in animals:
    print(animal.speak())     # Works for all types!
```

---

### `super()`

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)  # Call parent's __init__
        self.breed = breed

d = Dog("Rex", "Labrador")
print(d.name, d.breed)  # Rex Labrador
```

---

## 20. Dunder (Magic) Methods

- Dunder = **D**ouble **under**score. These are special methods Python calls automatically.

```python
class Book:
    def __init__(self, title, pages):
        self.title = title
        self.pages = pages

    def __str__(self):            # For print() — user-friendly
        return f"Book: {self.title}"

    def __repr__(self):           # For debugging — detailed
        return f"Book('{self.title}', {self.pages})"

    def __len__(self):            # For len()
        return self.pages

    def __add__(self, other):     # For + operator
        return Book(self.title + " & " + other.title, self.pages + other.pages)

    def __eq__(self, other):      # For == operator
        return self.title == other.title

    def __lt__(self, other):      # For < operator
        return self.pages < other.pages

    def __call__(self):           # Makes object callable
        return f"Reading {self.title}"

    def __getitem__(self, index): # For obj[index]
        return f"Page {index}"

b1 = Book("Python", 300)
b2 = Book("Django", 200)

print(b1)          # Book: Python
print(repr(b1))    # Book('Python', 300)
print(len(b1))     # 300
print(b1 + b2)     # Book: Python & Django
print(b1 == b2)    # False
print(b1 > b2)     # True (more pages)
print(b1())        # Reading Python
print(b1[5])       # Page 5
```

---

### Context Manager Methods

```python
class FileManager:
    def __init__(self, filename):
        self.filename = filename

    def __enter__(self):
        self.file = open(self.filename, "r")
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.file.close()
        return False  # Don't suppress exceptions

with FileManager("notes.txt") as f:
    content = f.read()
```

---

## 21. Advanced OOP

### Abstract Base Classes

```python
from abc import ABC, abstractmethod

class Payment(ABC):
    @abstractmethod
    def pay(self, amount):
        pass

    def receipt(self):        # Concrete method
        return "Payment done"

class CreditCard(Payment):
    def pay(self, amount):
        return f"Paid {amount} via Credit Card"

class UPI(Payment):
    def pay(self, amount):
        return f"Paid {amount} via UPI"

# Payment()  # ❌ Can't instantiate abstract class
cc = CreditCard()
print(cc.pay(500))  # Paid 500 via Credit Card
```

---

### Composition vs Inheritance

```python
# Inheritance = "IS-A" relationship
# Composition = "HAS-A" relationship — usually preferred

class Engine:
    def start(self):
        return "Engine started"

class Car:
    def __init__(self):
        self.engine = Engine()  # Car HAS-A Engine

    def start(self):
        return self.engine.start()

c = Car()
print(c.start())  # Engine started
```

---

### Method Resolution Order (MRO)

```python
class A:
    def who(self): return "A"

class B(A):
    def who(self): return "B"

class C(A):
    def who(self): return "C"

class D(B, C):
    pass

d = D()
print(d.who())       # B — follows MRO
print(D.__mro__)     # D → B → C → A → object
```

---

### `__slots__`

```python
class Point:
    __slots__ = ["x", "y"]  # Restrict attributes, save memory

    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(3, 4)
print(p.x)      # 3
# p.z = 5       # ❌ AttributeError — z not in __slots__
```

---

## 22. Memory Management

### Stack vs Heap

- **Stack**: Stores local variables and function calls — fast, automatically managed
- **Heap**: Stores objects — slower, managed by garbage collector

---

### Reference Counting

```python
import sys

x = [1, 2, 3]
print(sys.getrefcount(x))  # 2 (x + argument to getrefcount)

y = x  # Another reference
print(sys.getrefcount(x))  # 3

del y  # Remove reference
```

---

### Garbage Collection

```python
import gc

gc.collect()         # Force garbage collection
gc.get_count()       # (gen0, gen1, gen2) collection counts
gc.disable()         # Turn off automatic GC
gc.enable()          # Turn it back on
```

---

### Object Identity vs Equality

```python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)    # True  — same value
print(a is b)    # False — different objects in memory
print(a is c)    # True  — same object

print(id(a))     # Memory address of a
print(id(c))     # Same as a
```

---

## 23. Python Internals

### Bytecode

```python
def add(a, b):
    return a + b

import dis
dis.dis(add)
# Shows the bytecode instructions Python executes
```

---

### GIL (Global Interpreter Lock)

- The GIL is a **mutex** that allows only **one thread** to execute Python bytecode at a time.
- **Impact**: CPU-bound threads don't truly run in parallel in CPython.
- **Workaround**: Use `multiprocessing` for CPU-bound tasks; threads work fine for I/O-bound tasks.

---

### Thread Safety

```python
import threading

counter = 0
lock = threading.Lock()

def increment():
    global counter
    with lock:        # Thread-safe operation
        counter += 1

threads = [threading.Thread(target=increment) for _ in range(1000)]
for t in threads: t.start()
for t in threads: t.join()
print(counter)  # 1000
```

---

## 24. Functional Programming

### `map()`

```python
# Apply a function to every item in an iterable
nums = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, nums))
# [1, 4, 9, 16, 25]
```

---

### `filter()`

```python
# Keep only items where function returns True
nums = [1, 2, 3, 4, 5, 6]
evens = list(filter(lambda x: x % 2 == 0, nums))
# [2, 4, 6]
```

---

### `reduce()`

```python
from functools import reduce

# Combine all items into a single value
nums = [1, 2, 3, 4, 5]
total = reduce(lambda acc, x: acc + x, nums)
# 15  (((((1+2)+3)+4)+5))
```

---

### Immutability

```python
# Prefer creating new objects over modifying existing ones
original = (1, 2, 3)     # Tuple — immutable
new = original + (4,)     # New tuple instead of modifying

# Use tuple, frozenset for immutable collections
```

---

**Tip:** List comprehensions and generator expressions are often more Pythonic than `map`/`filter`.

---

## 25. Decorators Deep Dive

### Function Decorators

```python
def timer(func):
    import time
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end-start:.4f} seconds")
        return result
    return wrapper

@timer
def slow_function():
    import time
    time.sleep(1)

slow_function()  # slow_function took 1.00xx seconds
```

---

### Class Decorators

```python
class Repeat:
    def __init__(self, times):
        self.times = times

    def __call__(self, func):
        def wrapper(*args, **kwargs):
            for _ in range(self.times):
                func(*args, **kwargs)
        return wrapper

@Repeat(3)
def greet():
    print("Hello!")

greet()  # Prints Hello! 3 times
```

---

### Decorators with Arguments

```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(times=3)
def say_hi():
    print("Hi!")

say_hi()  # Hi! Hi! Hi!
```

---

### Preserving Function Metadata

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)          # Preserves __name__, __doc__, etc.
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

---

## 26. Context Managers

### `with` Statement

```python
# The 'with' block ensures cleanup code runs even on error
with open("file.txt", "r") as f:
    data = f.read()
# File is automatically closed here
```

---

### Custom Context Managers

```python
class DatabaseConnection:
    def __enter__(self):
        print("Connecting to database...")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Closing database connection...")
        return False

with DatabaseConnection() as db:
    print("Running query...")
# Closing database connection...
```

---

### `contextlib`

```python
from contextlib import contextmanager

@contextmanager
def timer():
    import time
    start = time.time()
    yield
    end = time.time()
    print(f"Elapsed: {end - start:.2f}s")

with timer():
    import time
    time.sleep(1)
# Elapsed: 1.00s
```

---

## 27. Itertools and Collections

### `itertools`

```python
import itertools

# count — infinite counter
for i in itertools.count(10, 2):  # 10, 12, 14, ...
    if i > 20: break
    print(i)

# chain — combine iterables
list(itertools.chain([1,2], [3,4], [5]))  # [1,2,3,4,5]

# combinations
list(itertools.combinations("ABC", 2))
# [('A','B'), ('A','C'), ('B','C')]

# permutations
list(itertools.permutations("AB", 2))
# [('A','B'), ('B','A')]

# groupby — group consecutive items
data = [("A", 1), ("A", 2), ("B", 3)]
for key, group in itertools.groupby(data, key=lambda x: x[0]):
    print(key, list(group))
```

---

### `collections`

```python
from collections import Counter, defaultdict, namedtuple, deque, OrderedDict

# Counter — count occurrences
words = ["apple", "banana", "apple", "cherry"]
c = Counter(words)
print(c)               # Counter({'apple': 2, 'banana': 1, 'cherry': 1})
print(c.most_common(2)) # [('apple', 2), ('banana', 1)]

# defaultdict — dict with default value for missing keys
dd = defaultdict(int)
dd["count"] += 1
print(dd["count"])     # 1
print(dd["missing"])   # 0 (default for int)

# namedtuple — tuple with named fields
Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)
print(p.x, p.y)        # 3 4
print(p[0])            # 3 (still works like a tuple)

# deque — efficient double-ended queue
dq = deque([1, 2, 3])
dq.appendleft(0)       # [0, 1, 2, 3]
dq.popleft()           # Remove from left — O(1), unlike list
dq.append(4)           # Add to right
```

---

## 28. Regular Expressions

### Pattern Matching with `re`

```python
import re

text = "My phone is 123-456-7890 and email is alice@example.com"

# Search for a pattern
match = re.search(r"\d{3}-\d{3}-\d{4}", text)
if match:
    print(match.group())  # 123-456-7890

# Find all matches
numbers = re.findall(r"\d+", text)
print(numbers)  # ['123', '456', '7890']

# Replace
clean = re.sub(r"\d", "X", text)  # Replace digits with X

# Split
parts = re.split(r"\s+", "Hello   World")  # ['Hello', 'World']
```

---

### Common Patterns

| Pattern | Meaning |
|---|---|
| `.` | Any character (except newline) |
| `\d` | Digit (0-9) |
| `\D` | Non-digit |
| `\w` | Word character (letter, digit, `_`) |
| `\W` | Non-word character |
| `\s` | Whitespace |
| `\S` | Non-whitespace |
| `^` | Start of string |
| `$` | End of string |
| `*` | 0 or more |
| `+` | 1 or more |
| `?` | 0 or 1 |
| `{n}` | Exactly n times |
| `{n,m}` | Between n and m times |
| `[abc]` | Any of a, b, c |
| `(abc)` | Group |

---

### Groups and Captures

```python
import re

pattern = r"(\d{3})-(\d{3})-(\d{4})"
match = re.search(pattern, "Call 123-456-7890 now")

if match:
    print(match.group(0))  # 123-456-7890 (full match)
    print(match.group(1))  # 123
    print(match.group(2))  # 456
    print(match.group(3))  # 7890
```

---

## 29. Date and Time

```python
from datetime import datetime, date, timedelta
import time

# Current date and time
now = datetime.now()
print(now)                        # 2024-01-15 10:30:00.123456
print(now.year, now.month, now.day)  # 2024 1 15
print(now.strftime("%d/%m/%Y %H:%M"))  # 15/01/2024 10:30

# Parse a date string
dt = datetime.strptime("15-01-2024", "%d-%m-%Y")
print(dt)

# Date arithmetic
today = date.today()
tomorrow = today + timedelta(days=1)
last_week = today - timedelta(weeks=1)

# Time elapsed
start = time.time()
# ... some code ...
elapsed = time.time() - start
print(f"Elapsed: {elapsed:.2f}s")
```

---

### Format Codes

| Code | Meaning | Example |
|---|---|---|
| `%Y` | 4-digit year | 2024 |
| `%m` | Month (01-12) | 01 |
| `%d` | Day (01-31) | 15 |
| `%H` | Hour (00-23) | 10 |
| `%M` | Minute (00-59) | 30 |
| `%S` | Second | 45 |
| `%A` | Weekday name | Monday |

---

## 30. Serialization

### JSON

```python
import json

# Python dict → JSON string
data = {"name": "Alice", "age": 25, "hobbies": ["reading", "coding"]}
json_str = json.dumps(data, indent=2)
print(json_str)

# JSON string → Python dict
loaded = json.loads(json_str)
print(loaded["name"])  # Alice

# Write to file
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

# Read from file
with open("data.json", "r") as f:
    loaded = json.load(f)
```

---

### Pickle

```python
import pickle

# Serialize Python object (any type — not just dict/list)
data = {"key": [1, 2, 3], "val": (4, 5)}

with open("data.pkl", "wb") as f:
    pickle.dump(data, f)

with open("data.pkl", "rb") as f:
    loaded = pickle.load(f)

print(loaded)  # {'key': [1, 2, 3], 'val': (4, 5)}
```

**Warning:** Never unpickle data from untrusted sources — it can execute arbitrary code.

---

**JSON vs Pickle:**
| | JSON | Pickle |
|---|---|---|
| Human readable | ✅ Yes | ❌ No |
| Cross-language | ✅ Yes | ❌ Python only |
| Supports all types | ❌ Limited | ✅ Yes |
| Safe | ✅ Yes | ⚠️ Caution |

---

## 31. Virtual Environments and Packaging

### Virtual Environment (`venv`)

```bash
# Create a virtual environment
python -m venv myenv

# Activate (Windows)
myenv\Scripts\activate

# Activate (Mac/Linux)
source myenv/bin/activate

# Deactivate
deactivate
```

---

### `pip` — Package Manager

```bash
pip install requests           # Install a package
pip install requests==2.28.0   # Specific version
pip uninstall requests         # Remove a package
pip list                       # List installed packages
pip freeze > requirements.txt  # Save dependencies
pip install -r requirements.txt # Install from file
pip show requests              # Info about a package
```

---

### `requirements.txt`

```
requests==2.28.0
numpy>=1.21.0
flask
```

---

### `pyproject.toml` (Modern Packaging)

```toml
[build-system]
requires = ["setuptools"]
build-backend = "setuptools.backends.legacy:build"

[project]
name = "mypackage"
version = "1.0.0"
dependencies = ["requests", "flask"]
```

---

## 32. Testing

### `unittest`

```python
import unittest

def add(a, b):
    return a + b

class TestAdd(unittest.TestCase):
    def test_positive(self):
        self.assertEqual(add(2, 3), 5)

    def test_negative(self):
        self.assertEqual(add(-1, -2), -3)

    def test_zero(self):
        self.assertEqual(add(0, 0), 0)

if __name__ == "__main__":
    unittest.main()
```

---

### `pytest`

```python
# test_math.py
def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5

def test_add_negative():
    assert add(-1, -2) == -3
```

```bash
pytest test_math.py         # Run tests
pytest -v                   # Verbose output
pytest --cov=mymodule       # Test coverage
```

---

### Mocking

```python
from unittest.mock import MagicMock, patch

def get_user_name(user_id):
    # Pretend this calls an API
    return "Alice"

with patch("__main__.get_user_name") as mock:
    mock.return_value = "Bob"
    print(get_user_name(1))  # Bob — mocked!
```

---

## 33. Logging and Debugging

### Logging

```python
import logging

# Configure logging
logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

logging.debug("Debugging info")    # Lowest level
logging.info("App started")
logging.warning("Low memory!")
logging.error("Something failed")
logging.critical("System crash!")
```

**Log Levels (Low → High):**
`DEBUG → INFO → WARNING → ERROR → CRITICAL`

---

### `pdb` — Python Debugger

```python
import pdb

def faulty_function(x):
    pdb.set_trace()   # Pause here and open debugger
    result = x * 2
    return result

faulty_function(5)
# (Pdb) n      → next line
# (Pdb) p x    → print value of x
# (Pdb) c      → continue
# (Pdb) q      → quit
```

---

## 34. Concurrency and Parallelism

### Threading (I/O-bound tasks)

```python
import threading
import time

def download(url):
    print(f"Downloading {url}")
    time.sleep(2)
    print(f"Done: {url}")

# Run concurrently
t1 = threading.Thread(target=download, args=("site1.com",))
t2 = threading.Thread(target=download, args=("site2.com",))

t1.start()
t2.start()
t1.join()
t2.join()
```

---

### Multiprocessing (CPU-bound tasks)

```python
from multiprocessing import Pool

def square(n):
    return n * n

with Pool(4) as p:
    results = p.map(square, range(10))
print(results)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

---

### Race Conditions

```python
import threading

counter = 0
lock = threading.Lock()

def safe_increment():
    global counter
    with lock:       # Only one thread at a time
        counter += 1
```

---

**When to use what:**
| Task Type | Tool |
|---|---|
| I/O-bound (web, file) | `threading` or `asyncio` |
| CPU-bound (math, ML) | `multiprocessing` |
| Simple async I/O | `asyncio` |

---

## 35. Async Programming

### `async` / `await`

```python
import asyncio

async def fetch_data():
    print("Fetching...")
    await asyncio.sleep(2)  # Non-blocking wait
    print("Done!")
    return "data"

async def main():
    result = await fetch_data()
    print(result)

asyncio.run(main())
```

---

### Running Multiple Coroutines

```python
import asyncio

async def task(name, delay):
    print(f"Starting {name}")
    await asyncio.sleep(delay)
    print(f"Done {name}")

async def main():
    # Run concurrently
    await asyncio.gather(
        task("A", 2),
        task("B", 1),
        task("C", 3),
    )

asyncio.run(main())
# Starts A, B, C — B finishes first, then A, then C
```

---

### Tasks and Futures

```python
import asyncio

async def slow_task():
    await asyncio.sleep(1)
    return 42

async def main():
    task = asyncio.create_task(slow_task())  # Schedule it
    print("Doing other work...")
    result = await task                       # Wait for result
    print(result)  # 42

asyncio.run(main())
```

---

## 36. Advanced Python Features

### Walrus Operator (`:=`)

```python
# Assign and use in the same expression
numbers = [1, 2, 3, 4, 5]

while chunk := numbers[:2]:
    print(chunk)
    numbers = numbers[2:]

# Clean up in comprehensions
data = [1, -2, 3, -4, 5]
results = [y for x in data if (y := x * 2) > 0]
print(results)  # [2, 6, 10]
```

---

### Type Hints and `typing` Module

```python
from typing import List, Dict, Tuple, Optional, Union

def process(names: List[str]) -> Dict[str, int]:
    return {name: len(name) for name in names}

def find(item: str) -> Optional[int]:  # Can return int or None
    return 0 if item else None

def flexible(x: Union[int, str]) -> str:
    return str(x)

# Python 3.10+ simplified syntax
def greet(name: str | None = None) -> str:
    return f"Hello, {name or 'stranger'}"
```

---

### Dataclasses

```python
from dataclasses import dataclass, field

@dataclass
class Student:
    name: str
    age: int
    grades: list = field(default_factory=list)

    def average(self):
        return sum(self.grades) / len(self.grades) if self.grades else 0

s = Student("Alice", 20, [85, 90, 92])
print(s)          # Student(name='Alice', age=20, grades=[85, 90, 92])
print(s.average()) # 89.0
print(s.name)      # Alice
```

---

### Enums

```python
from enum import Enum, auto

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

class Direction(Enum):
    NORTH = auto()   # auto() assigns values automatically
    SOUTH = auto()
    EAST = auto()
    WEST = auto()

print(Color.RED)        # Color.RED
print(Color.RED.value)  # 1
print(Color.RED.name)   # RED

for color in Color:
    print(color)
```

---

### Pattern Matching (Structural — Python 3.10+)

```python
def process_command(command):
    match command:
        case {"action": "buy", "item": item}:
            return f"Buying {item}"
        case {"action": "sell", "item": item, "price": price}:
            return f"Selling {item} for {price}"
        case [first, *rest]:
            return f"List: starts with {first}"
        case str() as s if len(s) > 10:
            return f"Long string: {s}"
        case _:
            return "Unknown command"

print(process_command({"action": "buy", "item": "apple"}))
```

---

## 🏆 Quick Reference Cheat Sheet

### Data Structures at a Glance

| Type | Ordered | Mutable | Duplicates | Syntax |
|---|---|---|---|---|
| `list` | ✅ | ✅ | ✅ | `[1, 2, 3]` |
| `tuple` | ✅ | ❌ | ✅ | `(1, 2, 3)` |
| `set` | ❌ | ✅ | ❌ | `{1, 2, 3}` |
| `dict` | ✅ | ✅ | Keys: ❌ | `{"a": 1}` |

---

### Time Complexities

| Operation | list | dict/set |
|---|---|---|
| Access | O(1) | O(1) |
| Search | O(n) | O(1) |
| Insert | O(1) amortized | O(1) |
| Delete | O(n) | O(1) |

---

### Most Used Built-in Functions

```python
len(x)         # Length
range(n)       # Range of numbers
print(x)       # Output
input()        # User input
type(x)        # Type of x
isinstance(x, T) # Type check
int(), str(), float(), list(), dict(), set(), tuple()  # Type casting
sorted(x)      # Returns sorted copy
enumerate(x)   # Returns (index, value) pairs
zip(a, b)      # Pairs up two iterables
map(f, x)      # Apply function to all items
filter(f, x)   # Keep items where function is True
sum(x)         # Sum of items
min(x), max(x) # Min and max
any(x), all(x) # Boolean checks
reversed(x)    # Reverse iterator
```

---

*End of Python Notes — All 36 Topics Covered ✅*
