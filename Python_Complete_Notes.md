# Python Complete Notes — Beginner to Advanced

## Table of Contents

1. [Python Fundamentals](#1-python-fundamentals)
   - [1.1 Introduction](#11-introduction)
   - [1.2 Setup & Environment](#12-setup--environment)
   - [1.3 Syntax & Structure](#13-syntax--structure)
2. [Variables and Data Types](#2-variables-and-data-types)
   - [2.1 Variables](#21-variables)
   - [2.2 Data Types](#22-data-types)
   - [2.3 Type Conversion](#23-type-conversion)
3. [Operators](#3-operators)
4. [Input and Output](#4-input-and-output)
5. [Control Flow](#5-control-flow)
   - [5.1 Conditional Statements](#51-conditional-statements)
   - [5.2 Loops](#52-loops)
6. [Data Structures](#6-data-structures)
   - [6.1 Strings](#61-strings)
   - [6.2 Lists](#62-lists)
   - [6.3 Tuples](#63-tuples)
   - [6.4 Sets](#64-sets)
   - [6.5 Dictionaries](#65-dictionaries)
7. [Functions](#7-functions)
8. [Modules and Packages](#8-modules-and-packages)
9. [Exception Handling](#9-exception-handling)
10. [File Handling](#10-file-handling)
11. [Object-Oriented Programming (OOP)](#11-object-oriented-programming-oop)
    - [11.1 Basics](#111-basics)
    - [11.2 Advanced Concepts](#112-advanced-concepts)
    - [11.3 Special Methods](#113-special-methods)
12. [Advanced Python Concepts](#12-advanced-python-concepts)
13. [Functional Programming](#13-functional-programming)
14. [Built-in Functions](#14-built-in-functions)
15. [Working with Libraries](#15-working-with-libraries)
16. [Standard Library Deep Dive](#16-standard-library-deep-dive)
17. [Testing and Debugging](#17-testing-and-debugging)
18. [Python for Data Handling](#18-python-for-data-handling)
19. [Regular Expressions](#19-regular-expressions)
20. [Multithreading and Multiprocessing](#20-multithreading-and-multiprocessing)
21. [Networking](#21-networking)
22. [Database Interaction](#22-database-interaction)
23. [Web Development](#23-web-development)
24. [Data Science and Machine Learning](#24-data-science-and-machine-learning)
25. [Automation and Scripting](#25-automation-and-scripting)
26. [Performance Optimization](#26-performance-optimization)
27. [Security Basics](#27-security-basics)
28. [Packaging and Deployment](#28-packaging-and-deployment)
29. [Version Control](#29-version-control)
30. [Best Practices](#30-best-practices)

---

## 1. Python Fundamentals

### 1.1 Introduction

#### What is Python?
- Python is a **high-level, easy-to-read programming language** created by Guido van Rossum in 1991.
- It reads almost like English, making it great for beginners.

#### Features of Python
- Simple and readable syntax
- Interpreted (runs line by line)
- Dynamically typed (no need to declare variable types)
- Cross-platform (runs on Windows, Mac, Linux)
- Huge standard library
- Open source and free

#### Use Cases of Python
- **Web Development** — Django, Flask
- **Artificial Intelligence / ML** — TensorFlow, PyTorch, Scikit-learn
- **Data Science** — Pandas, NumPy, Matplotlib
- **Automation / Scripting** — automate repetitive tasks
- **Cybersecurity** — penetration testing tools
- **Game Development** — Pygame
- **APIs and Backends** — REST APIs

#### Python 2 vs Python 3

| Feature | Python 2 | Python 3 |
|---|---|---|
| `print` | `print "hello"` | `print("hello")` |
| Division | `5/2 = 2` (integer) | `5/2 = 2.5` (float) |
| Unicode | ASCII by default | Unicode by default |
| Support | End of life (2020) | Actively maintained |

> Always use **Python 3**.

---

### 1.2 Setup & Environment

#### Installing Python
1. Go to [https://python.org](https://python.org)
2. Download the latest Python 3.x
3. During install, check **"Add Python to PATH"**
4. Verify: open terminal and type `python --version`

#### Python REPL (Interactive Interpreter)
- Type `python` or `python3` in terminal
- A `>>>` prompt appears — type code and see results instantly
```python
>>> 2 + 3
5
>>> print("Hello")
Hello
```

#### Running Python Scripts
- Save file as `hello.py`
- Run: `python hello.py`

#### IDEs
- **VS Code** — lightweight, free, great extensions
- **PyCharm** — full-featured Python IDE
- **Jupyter Notebook** — for data science

#### Virtual Environments (venv)
- A virtual environment is an **isolated Python environment** for each project.
- Prevents package conflicts between projects.

```bash
# Create virtual environment
python -m venv myenv

# Activate (Windows)
myenv\Scripts\activate

# Activate (Mac/Linux)
source myenv/bin/activate

# Deactivate
deactivate
```

---

### 1.3 Syntax & Structure

#### Indentation
- Python uses **indentation (spaces/tabs)** to define code blocks — not `{}` like other languages.
- Standard: **4 spaces** per level.
```python
if True:
    print("Indented block")  # This belongs to the if block
print("Outside block")
```

#### Statements and Expressions
- **Statement** — a complete instruction (e.g., `x = 5`)
- **Expression** — produces a value (e.g., `2 + 3`)

#### Keywords
- Reserved words Python uses: `if`, `else`, `for`, `while`, `def`, `class`, `return`, `import`, `True`, `False`, `None`, etc.
- You cannot use these as variable names.

#### Comments
```python
# This is a single-line comment

"""
This is a
multi-line comment (docstring)
"""
```

---

## 2. Variables and Data Types

### 2.1 Variables

#### Variable Declaration
- No need to declare type — just assign a value.
```python
name = "Alice"
age = 25
price = 9.99
```

#### Naming Rules and Conventions
- Must start with a letter or `_`
- Cannot start with a number
- Case-sensitive (`Name` ≠ `name`)
- Use `snake_case` for variables: `my_variable`
- Use `UPPER_CASE` for constants: `MAX_SIZE = 100`

#### Dynamic Typing
- Python automatically detects the type.
```python
x = 10       # int
x = "hello"  # now it's a str — no error!
```

---

### 2.2 Data Types

#### Numeric Types
```python
a = 10        # int (whole number)
b = 3.14      # float (decimal)
c = 2 + 3j    # complex (real + imaginary)

print(type(a))  # <class 'int'>
print(type(b))  # <class 'float'>
print(type(c))  # <class 'complex'>
```

#### Boolean
```python
is_active = True
is_done = False
print(type(is_active))  # <class 'bool'>
```
- Internally, `True = 1` and `False = 0`

#### String
```python
name = "Alice"
greeting = 'Hello'
multi = """This is
a multi-line string"""
```

#### NoneType
- Represents the absence of a value (like `null` in other languages).
```python
result = None
print(type(result))  # <class 'NoneType'>
```

---

### 2.3 Type Conversion

#### Implicit Casting (automatic)
```python
x = 5      # int
y = 2.0    # float
z = x + y  # Python converts x to float automatically
print(z)   # 7.0
```

#### Explicit Casting (manual)
```python
int("10")      # 10
float("3.14")  # 3.14
str(100)       # "100"
bool(0)        # False
bool(1)        # True
list("abc")    # ['a', 'b', 'c']
```

---

## 3. Operators

### Arithmetic Operators
```python
a, b = 10, 3
print(a + b)   # 13 — addition
print(a - b)   # 7  — subtraction
print(a * b)   # 30 — multiplication
print(a / b)   # 3.333 — division (always float)
print(a // b)  # 3  — floor division
print(a % b)   # 1  — modulus (remainder)
print(a ** b)  # 1000 — exponentiation
```

### Comparison Operators
```python
print(5 == 5)   # True
print(5 != 3)   # True
print(5 > 3)    # True
print(5 < 3)    # False
print(5 >= 5)   # True
print(5 <= 4)   # False
```

### Logical Operators
```python
print(True and False)  # False
print(True or False)   # True
print(not True)        # False
```

### Assignment Operators
```python
x = 10
x += 5   # x = x + 5 → 15
x -= 3   # x = x - 3 → 12
x *= 2   # x = x * 2 → 24
x /= 4   # x = x / 4 → 6.0
x //= 2  # x = x // 2 → 3.0
x **= 2  # x = x ** 2 → 9.0
x %= 4   # x = x % 4 → 1.0
```

### Bitwise Operators
```python
a, b = 6, 3   # 6=110, 3=011 in binary
print(a & b)  # 2 — AND
print(a | b)  # 7 — OR
print(a ^ b)  # 5 — XOR
print(~a)     # -7 — NOT
print(a << 1) # 12 — left shift
print(a >> 1) # 3  — right shift
```

### Membership Operators
```python
fruits = ["apple", "banana"]
print("apple" in fruits)     # True
print("grape" not in fruits) # True
```

### Identity Operators
```python
a = [1, 2]
b = a
c = [1, 2]
print(a is b)   # True — same object in memory
print(a is c)   # False — different objects (same value)
print(a is not c)  # True
```

---

## 4. Input and Output

### input()
- Always returns a **string**.
```python
name = input("Enter your name: ")
age = int(input("Enter your age: "))  # convert to int
```

### print()
```python
print("Hello")
print("Name:", name, "Age:", age)
print("Hello", end=" ")  # don't add newline
print("World")  # prints on same line: Hello World
print(1, 2, 3, sep="-")  # 1-2-3
```

### Formatting Output

#### f-strings (recommended)
```python
name = "Alice"
age = 25
print(f"My name is {name} and I am {age} years old.")
print(f"Pi is approximately {3.14159:.2f}")  # 2 decimal places
```

#### format()
```python
print("My name is {} and I am {} years old.".format(name, age))
print("{0} is {1} years old".format("Bob", 30))
```

#### % formatting (old style)
```python
print("Hello %s, you are %d years old" % (name, age))
```

---

## 5. Control Flow

### 5.1 Conditional Statements

#### if
```python
x = 10
if x > 5:
    print("x is greater than 5")
```

#### if-else
```python
x = 3
if x > 5:
    print("Greater")
else:
    print("Smaller or equal")
```

#### if-elif-else
```python
score = 75
if score >= 90:
    print("A")
elif score >= 80:
    print("B")
elif score >= 70:
    print("C")
else:
    print("F")
```

#### Nested Conditions
```python
x = 10
if x > 0:
    if x < 100:
        print("x is between 0 and 100")
```

#### Ternary (One-line if-else)
```python
result = "Even" if x % 2 == 0 else "Odd"
```

---

### 5.2 Loops

#### for loop
```python
# Loop over a list
for fruit in ["apple", "banana", "cherry"]:
    print(fruit)

# Loop with range
for i in range(5):       # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 8):    # 2, 3, 4, 5, 6, 7
    print(i)

for i in range(0, 10, 2):  # 0, 2, 4, 6, 8
    print(i)
```

#### while loop
```python
count = 0
while count < 5:
    print(count)
    count += 1
```

#### break, continue, pass
```python
# break — exit the loop early
for i in range(10):
    if i == 5:
        break
    print(i)  # prints 0–4

# continue — skip current iteration
for i in range(10):
    if i % 2 == 0:
        continue
    print(i)  # prints odd numbers only

# pass — placeholder, does nothing
for i in range(5):
    pass  # empty loop body is valid with pass
```

---

## 6. Data Structures

### 6.1 Strings

#### Indexing and Slicing
```python
s = "Hello, World!"
print(s[0])      # H
print(s[-1])     # !
print(s[0:5])    # Hello
print(s[7:])     # World!
print(s[:5])     # Hello
print(s[::2])    # Hlo ol!  (every 2nd character)
print(s[::-1])   # !dlroW ,olleH  (reverse)
```

#### String Methods
```python
s = "  Hello World  "
s.upper()          # "  HELLO WORLD  "
s.lower()          # "  hello world  "
s.strip()          # "Hello World"
s.lstrip()         # "Hello World  "
s.rstrip()         # "  Hello World"
s.replace("World", "Python")  # "  Hello Python  "
s.split()          # ['Hello', 'World']
",".join(["a","b","c"])  # "a,b,c"
s.find("World")    # index of first occurrence
s.startswith("  Hello")  # True
s.endswith("  ")   # True
s.count("l")       # 3
s.isdigit()        # False
s.isalpha()        # False
```

---

### 6.2 Lists

#### Creation
```python
fruits = ["apple", "banana", "cherry"]
nums = [1, 2, 3, 4, 5]
mixed = [1, "hello", True, 3.14]
empty = []
```

#### Indexing and Slicing
```python
print(fruits[0])    # apple
print(fruits[-1])   # cherry
print(fruits[1:3])  # ['banana', 'cherry']
```

#### List Methods
```python
fruits.append("mango")       # add to end
fruits.insert(1, "grape")    # insert at index
fruits.remove("banana")      # remove by value
fruits.pop()                 # remove last
fruits.pop(0)                # remove at index
fruits.sort()                # sort in place
fruits.reverse()             # reverse in place
fruits.index("apple")        # get index of value
fruits.count("apple")        # count occurrences
fruits.extend(["kiwi","lime"])  # add multiple
len(fruits)                  # length
```

#### List Comprehension
```python
# [expression for item in iterable if condition]
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
```

---

### 6.3 Tuples

- **Immutable** (cannot be changed after creation).
- Faster than lists.
- Used for fixed data (e.g., coordinates, RGB values).

```python
point = (3, 5)
colors = ("red", "green", "blue")
single = (42,)  # single element needs trailing comma

# Indexing
print(point[0])  # 3

# Packing and Unpacking
coords = (10, 20, 30)
x, y, z = coords  # unpack
print(x, y, z)    # 10 20 30

# Swap variables
a, b = 5, 10
a, b = b, a
```

---

### 6.4 Sets

- **Unordered**, **no duplicates**, mutable.

```python
s = {1, 2, 3, 3, 2}
print(s)  # {1, 2, 3}

s.add(4)
s.remove(2)
s.discard(10)  # no error if not found

# Set operations
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}
print(a | b)  # union: {1,2,3,4,5,6}
print(a & b)  # intersection: {3,4}
print(a - b)  # difference: {1,2}
print(a ^ b)  # symmetric difference: {1,2,5,6}
```

---

### 6.5 Dictionaries

- **Key-value pairs**. Keys must be unique and immutable.

```python
person = {"name": "Alice", "age": 25, "city": "Delhi"}

# Access
print(person["name"])         # Alice
print(person.get("age"))      # 25
print(person.get("phone", "N/A"))  # N/A (default)

# Modify
person["age"] = 26
person["email"] = "alice@mail.com"  # add new key

# Delete
del person["city"]
person.pop("email")

# Methods
person.keys()    # dict_keys(['name', 'age'])
person.values()  # dict_values(['Alice', 26])
person.items()   # dict_items([('name','Alice'), ('age',26)])

# Loop
for key, value in person.items():
    print(f"{key}: {value}")
```

#### Nested Dictionaries
```python
students = {
    "alice": {"age": 20, "grade": "A"},
    "bob":   {"age": 22, "grade": "B"},
}
print(students["alice"]["grade"])  # A
```

---

## 7. Functions

### Defining Functions
```python
def greet(name):
    """This function greets a person."""
    return f"Hello, {name}!"

print(greet("Alice"))  # Hello, Alice!
```

### Parameters and Arguments

#### Positional
```python
def add(a, b):
    return a + b

add(3, 5)  # a=3, b=5
```

#### Keyword
```python
add(b=5, a=3)  # order doesn't matter
```

#### Default
```python
def greet(name, message="Hello"):
    return f"{message}, {name}!"

greet("Alice")           # Hello, Alice!
greet("Bob", "Welcome")  # Welcome, Bob!
```

#### Variable-length (*args, **kwargs)
```python
# *args — collect extra positional args as tuple
def total(*args):
    return sum(args)

total(1, 2, 3, 4)  # 10

# **kwargs — collect extra keyword args as dict
def info(**kwargs):
    for k, v in kwargs.items():
        print(f"{k}: {v}")

info(name="Alice", age=25)
```

### Return Values
```python
def min_max(lst):
    return min(lst), max(lst)  # returns a tuple

low, high = min_max([3, 1, 7, 2])
```

### Lambda Functions
- Anonymous (nameless) one-line functions.
```python
square = lambda x: x ** 2
print(square(5))  # 25

add = lambda a, b: a + b
print(add(3, 4))  # 7
```

### Recursion
- A function that calls itself.
```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)

print(factorial(5))  # 120
```

### Docstrings
```python
def add(a, b):
    """
    Add two numbers.

    Args:
        a: first number
        b: second number
    Returns:
        Sum of a and b
    """
    return a + b

print(add.__doc__)
```

---

## 8. Modules and Packages

### Importing Modules
```python
import math
print(math.sqrt(16))   # 4.0

from math import pi, sqrt
print(pi)              # 3.14159...

import numpy as np     # alias
```

### Creating Custom Modules
- Create `myutils.py`:
```python
def greet(name):
    return f"Hello, {name}!"
```
- Use it:
```python
import myutils
print(myutils.greet("Alice"))
```

### `__name__ == "__main__"`
```python
def main():
    print("Running directly")

if __name__ == "__main__":
    main()
# Only runs when this file is executed directly, not when imported
```

### Packages and `__init__.py`
```
mypackage/
    __init__.py   ← makes it a package
    module1.py
    module2.py
```
```python
from mypackage import module1
```

### Standard Library Overview
- `math` — mathematical functions
- `os` — operating system interaction
- `sys` — system parameters
- `datetime` — date and time
- `random` — random numbers
- `json` — JSON handling
- `re` — regular expressions
- `collections` — specialized data structures
- `itertools` — iterator tools
- `pathlib` — file paths

---

## 9. Exception Handling

### try / except
```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero!")
```

### else / finally
```python
try:
    num = int(input("Enter a number: "))
except ValueError:
    print("That's not a number!")
else:
    print(f"You entered: {num}")  # runs if no exception
finally:
    print("This always runs")  # cleanup code
```

### Multiple Exceptions
```python
try:
    x = int("abc")
    y = 10 / 0
except ValueError:
    print("Value error")
except ZeroDivisionError:
    print("Division error")
except Exception as e:
    print(f"Unexpected error: {e}")
```

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
    print(e)
```

---

## 10. File Handling

### Opening Files (Modes)
| Mode | Meaning |
|---|---|
| `r` | Read (default) |
| `w` | Write (creates/overwrites) |
| `a` | Append |
| `x` | Create (fails if exists) |
| `b` | Binary mode (e.g. `rb`, `wb`) |

### Reading Files
```python
# Read entire file
with open("file.txt", "r") as f:
    content = f.read()

# Read line by line
with open("file.txt", "r") as f:
    for line in f:
        print(line.strip())

# Read all lines as list
with open("file.txt", "r") as f:
    lines = f.readlines()
```

### Writing Files
```python
with open("output.txt", "w") as f:
    f.write("Hello, World!\n")
    f.write("Second line\n")

# Append
with open("output.txt", "a") as f:
    f.write("Appended line\n")
```

### Binary Files
```python
# Copy a binary file (e.g., image)
with open("image.png", "rb") as src:
    with open("copy.png", "wb") as dst:
        dst.write(src.read())
```

### with Statement (Context Manager)
- Automatically closes the file even if an error occurs.
- **Always use `with` for file handling.**

---

## 11. Object-Oriented Programming (OOP)

### 11.1 Basics

#### Classes and Objects
```python
class Dog:
    # Class attribute (shared by all instances)
    species = "Canis lupus"

    # Constructor
    def __init__(self, name, age):
        self.name = name  # instance attribute
        self.age = age

    # Method
    def bark(self):
        return f"{self.name} says Woof!"

# Create objects (instances)
dog1 = Dog("Rex", 3)
dog2 = Dog("Buddy", 5)

print(dog1.bark())       # Rex says Woof!
print(dog2.name)         # Buddy
print(Dog.species)       # Canis lupus
```

---

### 11.2 Advanced Concepts

#### Encapsulation
- Hide internal data using private attributes (`_` or `__`).
```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # private

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance

acc = BankAccount(1000)
acc.deposit(500)
print(acc.get_balance())  # 1500
# print(acc.__balance)   # AttributeError — private!
```

#### Inheritance
```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "Some sound"

class Cat(Animal):  # Cat inherits from Animal
    def speak(self):  # override parent method
        return f"{self.name} says Meow!"

class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

c = Cat("Whiskers")
print(c.speak())  # Whiskers says Meow!
```

#### Polymorphism
- Same method name, different behavior.
```python
animals = [Cat("Whiskers"), Dog("Rex")]
for animal in animals:
    print(animal.speak())  # each calls its own speak()
```

#### Abstraction
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius ** 2

c = Circle(5)
print(c.area())  # 78.5
```

---

### 11.3 Special Methods

#### `__str__` and `__repr__`
```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"Point({self.x}, {self.y})"  # for print()

    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"  # for debugging
```

#### Operator Overloading
```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __str__(self):
        return f"Vector({self.x}, {self.y})"

v1 = Vector(1, 2)
v2 = Vector(3, 4)
print(v1 + v2)  # Vector(4, 6)
```

---

## 12. Advanced Python Concepts

### Iterators
```python
class Counter:
    def __init__(self, max):
        self.max = max
        self.current = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.current >= self.max:
            raise StopIteration
        self.current += 1
        return self.current

for num in Counter(3):
    print(num)  # 1, 2, 3
```

### Generators (yield)
```python
def count_up(n):
    for i in range(1, n+1):
        yield i  # pauses and returns value

gen = count_up(5)
print(next(gen))  # 1
print(next(gen))  # 2

# Use in a for loop
for val in count_up(3):
    print(val)  # 1, 2, 3

# Generator expression
squares = (x**2 for x in range(5))
```

### Decorators
- A function that wraps another function to add functionality.
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before function")
        result = func(*args, **kwargs)
        print("After function")
        return result
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

say_hello()
# Before function
# Hello!
# After function
```

### Closures
```python
def multiplier(factor):
    def multiply(number):
        return number * factor
    return multiply

double = multiplier(2)
triple = multiplier(3)
print(double(5))  # 10
print(triple(5))  # 15
```

### Comprehensions

#### List Comprehension
```python
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
```

#### Dict Comprehension
```python
squares_dict = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

#### Set Comprehension
```python
unique_squares = {x**2 for x in [-2, -1, 0, 1, 2]}
# {0, 1, 4}
```

### Context Managers
```python
class Timer:
    import time
    def __enter__(self):
        import time
        self.start = time.time()
        return self

    def __exit__(self, *args):
        import time
        print(f"Elapsed: {time.time() - self.start:.2f}s")

with Timer():
    for _ in range(1000000):
        pass
```

---

## 13. Functional Programming

### map()
- Apply a function to every element in an iterable.
```python
nums = [1, 2, 3, 4, 5]
squares = list(map(lambda x: x**2, nums))
# [1, 4, 9, 16, 25]
```

### filter()
- Keep only elements where function returns True.
```python
evens = list(filter(lambda x: x % 2 == 0, nums))
# [2, 4]
```

### reduce()
- Combine all elements into a single value.
```python
from functools import reduce
total = reduce(lambda a, b: a + b, nums)
# 15
```

### lambda functions
```python
add = lambda a, b: a + b
result = add(3, 4)  # 7
```

---

## 14. Built-in Functions

```python
len([1, 2, 3])          # 3
range(5)                 # 0,1,2,3,4
type(3.14)               # <class 'float'>

# zip — combine iterables
names = ["Alice", "Bob"]
ages = [25, 30]
for name, age in zip(names, ages):
    print(name, age)

# enumerate — index + value
for i, val in enumerate(["a", "b", "c"]):
    print(i, val)  # 0 a, 1 b, 2 c

# sorted — returns new sorted list
sorted([3, 1, 4, 1, 5])         # [1, 1, 3, 4, 5]
sorted([3, 1, 5], reverse=True) # [5, 3, 1]

# any / all
any([False, True, False])  # True
all([True, True, True])    # True

# min, max, sum, abs, round
min([3, 1, 4])    # 1
max([3, 1, 4])    # 4
sum([1, 2, 3])    # 6
abs(-5)           # 5
round(3.14159, 2) # 3.14
```

---

## 15. Working with Libraries

### pip (Package Manager)
```bash
pip install requests          # install
pip uninstall requests        # uninstall
pip list                      # show installed
pip show requests             # info about a package
pip install requests==2.28.0  # specific version
pip freeze > requirements.txt # export dependencies
pip install -r requirements.txt  # install from file
```

### Virtual Environments (recap)
```bash
python -m venv venv
source venv/bin/activate   # Mac/Linux
venv\Scripts\activate      # Windows
pip install <package>
deactivate
```

---

## 16. Standard Library Deep Dive

### datetime
```python
from datetime import datetime, date, timedelta

now = datetime.now()
print(now)                        # current datetime
print(now.strftime("%Y-%m-%d"))  # formatted string
d = date(2024, 1, 15)
delta = timedelta(days=10)
print(d + delta)                  # 2024-01-25
```

### math
```python
import math
math.sqrt(16)     # 4.0
math.ceil(4.2)    # 5
math.floor(4.9)   # 4
math.pi           # 3.14159...
math.log(100, 10) # 2.0
```

### random
```python
import random
random.random()           # float 0.0–1.0
random.randint(1, 10)     # random int between 1 and 10
random.choice(["a","b","c"])  # random element
random.shuffle([1,2,3,4]) # shuffle list in place
random.sample(range(100), 5)  # 5 unique random numbers
```

### os
```python
import os
os.getcwd()               # current directory
os.listdir(".")           # list directory contents
os.mkdir("new_folder")    # create directory
os.remove("file.txt")     # delete file
os.path.exists("file.txt") # check if exists
os.path.join("folder", "file.txt")  # safe path joining
os.environ.get("PATH")    # environment variable
```

### sys
```python
import sys
sys.argv             # command-line arguments
sys.exit(0)          # exit program
sys.version          # Python version
sys.path             # module search path
```

### collections
```python
from collections import Counter, defaultdict, deque, OrderedDict, namedtuple

# Counter — count elements
c = Counter("hello world")
print(c.most_common(3))

# defaultdict — dict with default value
dd = defaultdict(list)
dd["key"].append(1)  # no KeyError

# deque — fast append/pop from both ends
dq = deque([1, 2, 3])
dq.appendleft(0)
dq.popleft()

# namedtuple — tuple with field names
Point = namedtuple("Point", ["x", "y"])
p = Point(3, 5)
print(p.x, p.y)
```

### itertools
```python
import itertools
list(itertools.chain([1,2],[3,4]))       # [1,2,3,4]
list(itertools.combinations("ABC", 2))  # [('A','B'),('A','C'),('B','C')]
list(itertools.permutations("AB", 2))   # [('A','B'),('B','A')]
list(itertools.product([1,2],[3,4]))    # all pairs
```

---

## 17. Testing and Debugging

### Debugging Techniques
```python
print(x)               # print debugging
import pdb; pdb.set_trace()  # interactive debugger
# press 'n' (next), 's' (step), 'c' (continue), 'q' (quit)
```

### Logging
```python
import logging

logging.basicConfig(level=logging.DEBUG,
                    format="%(asctime)s - %(levelname)s - %(message)s")

logging.debug("Debug message")
logging.info("Info message")
logging.warning("Warning message")
logging.error("Error message")
logging.critical("Critical message")
```

### Unit Testing (unittest)
```python
import unittest

def add(a, b):
    return a + b

class TestAdd(unittest.TestCase):
    def test_positive(self):
        self.assertEqual(add(2, 3), 5)

    def test_negative(self):
        self.assertEqual(add(-1, -1), -2)

    def test_zero(self):
        self.assertEqual(add(0, 0), 0)

if __name__ == "__main__":
    unittest.main()
```

### pytest
```bash
pip install pytest
```
```python
# test_add.py
def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5

def test_add_negative():
    assert add(-1, -1) == -2
```
```bash
pytest test_add.py
pytest -v  # verbose output
```

---

## 18. Python for Data Handling

### JSON Handling
```python
import json

# Python dict → JSON string
data = {"name": "Alice", "age": 25}
json_str = json.dumps(data)
json_str_pretty = json.dumps(data, indent=4)

# JSON string → Python dict
parsed = json.loads(json_str)
print(parsed["name"])

# Read/Write JSON files
with open("data.json", "w") as f:
    json.dump(data, f, indent=4)

with open("data.json", "r") as f:
    loaded = json.load(f)
```

### CSV Files
```python
import csv

# Write CSV
with open("data.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["Name", "Age"])
    writer.writerow(["Alice", 25])
    writer.writerow(["Bob", 30])

# Read CSV
with open("data.csv", "r") as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)

# DictReader / DictWriter
with open("data.csv", "r") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["Name"], row["Age"])
```

### Pickle
```python
import pickle

data = {"key": "value", "nums": [1, 2, 3]}

# Serialize
with open("data.pkl", "wb") as f:
    pickle.dump(data, f)

# Deserialize
with open("data.pkl", "rb") as f:
    loaded = pickle.load(f)
```

---

## 19. Regular Expressions

### re Module
```python
import re

text = "My phone is 123-456-7890 and email is alice@mail.com"

# search — find first match
match = re.search(r"\d{3}-\d{3}-\d{4}", text)
if match:
    print(match.group())  # 123-456-7890

# findall — find all matches
emails = re.findall(r"\w+@\w+\.\w+", text)

# sub — replace
result = re.sub(r"\d", "*", "Phone: 12345")
# Phone: *****

# match — match at start of string
m = re.match(r"My", text)

# compile — reuse patterns
pattern = re.compile(r"\d+")
pattern.findall("I have 3 cats and 12 dogs")
```

### Common Regex Patterns
| Pattern | Meaning |
|---|---|
| `\d` | Any digit (0-9) |
| `\w` | Any word character (a-z, A-Z, 0-9, _) |
| `\s` | Any whitespace |
| `.` | Any character except newline |
| `^` | Start of string |
| `$` | End of string |
| `*` | 0 or more |
| `+` | 1 or more |
| `?` | 0 or 1 |
| `{n}` | Exactly n times |
| `[abc]` | Any of a, b, c |
| `(abc)` | Group |

---

## 20. Multithreading and Multiprocessing

### Threads
```python
import threading

def task(name):
    print(f"Task {name} running")

t1 = threading.Thread(target=task, args=("A",))
t2 = threading.Thread(target=task, args=("B",))

t1.start()
t2.start()
t1.join()  # wait for t1 to finish
t2.join()
```

### GIL (Global Interpreter Lock)
- Python has a GIL — only **one thread** runs Python code at a time.
- Threads are good for **I/O-bound** tasks (network, file ops).
- For **CPU-bound** tasks (computation), use `multiprocessing`.

### multiprocessing
```python
from multiprocessing import Process

def worker(name):
    print(f"Worker {name}")

p1 = Process(target=worker, args=("A",))
p2 = Process(target=worker, args=("B",))
p1.start()
p2.start()
p1.join()
p2.join()
```

### Concurrency Basics
```python
# ThreadPoolExecutor for concurrent I/O tasks
from concurrent.futures import ThreadPoolExecutor

def fetch(url):
    return f"Fetched {url}"

with ThreadPoolExecutor(max_workers=5) as executor:
    results = executor.map(fetch, ["url1", "url2", "url3"])
```

---

## 21. Networking

### Sockets
```python
import socket

# Server
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(("localhost", 8080))
server.listen(1)
conn, addr = server.accept()
data = conn.recv(1024)
conn.send(b"Hello from server")
conn.close()

# Client
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(("localhost", 8080))
client.send(b"Hello from client")
response = client.recv(1024)
client.close()
```

### HTTP Requests (requests library)
```bash
pip install requests
```
```python
import requests

# GET request
response = requests.get("https://api.github.com")
print(response.status_code)  # 200
print(response.json())

# POST request
payload = {"username": "alice", "password": "secret"}
response = requests.post("https://httpbin.org/post", json=payload)
print(response.json())

# Headers
headers = {"Authorization": "Bearer mytoken"}
response = requests.get("https://api.example.com/data", headers=headers)
```

### APIs
```python
import requests

url = "https://api.open-meteo.com/v1/forecast"
params = {"latitude": 52.52, "longitude": 13.41, "current_weather": True}
response = requests.get(url, params=params)
data = response.json()
print(data["current_weather"])
```

---

## 22. Database Interaction

### SQLite
```python
import sqlite3

conn = sqlite3.connect("mydb.db")  # creates file if not exists
cursor = conn.cursor()

# Create table
cursor.execute("""
    CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT,
        age INTEGER
    )
""")

# Insert
cursor.execute("INSERT INTO users (name, age) VALUES (?, ?)", ("Alice", 25))
conn.commit()

# Read
cursor.execute("SELECT * FROM users")
rows = cursor.fetchall()
for row in rows:
    print(row)

conn.close()
```

### CRUD Operations
```python
# Create
cursor.execute("INSERT INTO users (name, age) VALUES (?, ?)", ("Bob", 30))

# Read
cursor.execute("SELECT * FROM users WHERE age > ?", (20,))

# Update
cursor.execute("UPDATE users SET age = ? WHERE name = ?", (26, "Alice"))

# Delete
cursor.execute("DELETE FROM users WHERE name = ?", ("Bob",))

conn.commit()
```

### ORM Basics (SQLAlchemy)
```bash
pip install sqlalchemy
```
```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import DeclarativeBase, Session

engine = create_engine("sqlite:///mydb.db")

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)

Base.metadata.create_all(engine)

with Session(engine) as session:
    user = User(name="Alice", age=25)
    session.add(user)
    session.commit()

    users = session.query(User).all()
    for u in users:
        print(u.name, u.age)
```

---

## 23. Web Development

### Basics of HTTP
- **HTTP** — protocol for sending data between client and server
- **Methods**: `GET` (read), `POST` (create), `PUT` (update), `DELETE` (remove)
- **Status Codes**: `200` OK, `201` Created, `400` Bad Request, `404` Not Found, `500` Server Error

### Flask (Micro Framework)
```bash
pip install flask
```
```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Flask!"

@app.route("/user/<name>")
def user(name):
    return f"Hello, {name}!"

@app.route("/api/users", methods=["GET", "POST"])
def users():
    if request.method == "POST":
        data = request.get_json()
        return jsonify({"received": data}), 201
    return jsonify({"users": ["Alice", "Bob"]})

if __name__ == "__main__":
    app.run(debug=True)
```

### Django (Full Framework)
```bash
pip install django
django-admin startproject myproject
cd myproject
python manage.py startapp myapp
python manage.py runserver
```

```python
# models.py
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title

# views.py
from django.http import JsonResponse
from .models import Post

def post_list(request):
    posts = list(Post.objects.values())
    return JsonResponse(posts, safe=False)

# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path("posts/", views.post_list),
]
```

### REST APIs
```python
# Flask REST API example
from flask import Flask, request, jsonify

app = Flask(__name__)
items = []

@app.route("/items", methods=["GET"])
def get_items():
    return jsonify(items)

@app.route("/items", methods=["POST"])
def add_item():
    item = request.get_json()
    items.append(item)
    return jsonify(item), 201

@app.route("/items/<int:idx>", methods=["DELETE"])
def delete_item(idx):
    items.pop(idx)
    return jsonify({"deleted": True})

if __name__ == "__main__":
    app.run(debug=True)
```

---

## 24. Data Science and Machine Learning

### NumPy
```bash
pip install numpy
```
```python
import numpy as np

a = np.array([1, 2, 3, 4, 5])
b = np.array([[1, 2], [3, 4]])

print(a.shape)        # (5,)
print(b.shape)        # (2, 2)
print(a * 2)          # [2, 4, 6, 8, 10]
print(np.mean(a))     # 3.0
print(np.zeros((3,3)))
print(np.arange(0, 10, 2))  # [0, 2, 4, 6, 8]
```

### Pandas
```bash
pip install pandas
```
```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
    "score": [90, 85, 92]
})

print(df.head())
print(df.describe())
print(df[df["age"] > 28])       # filter rows
print(df.groupby("age").mean()) # groupby

df.to_csv("data.csv", index=False)
df = pd.read_csv("data.csv")
```

### Matplotlib / Seaborn
```bash
pip install matplotlib seaborn
```
```python
import matplotlib.pyplot as plt
import seaborn as sns

# Line plot
plt.plot([1,2,3,4], [1,4,9,16])
plt.xlabel("x")
plt.ylabel("y")
plt.title("Square Numbers")
plt.show()

# Seaborn
import pandas as pd
df = pd.DataFrame({"x": range(10), "y": range(10)})
sns.lineplot(data=df, x="x", y="y")
plt.show()
```

### Scikit-learn
```bash
pip install scikit-learn
```
```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error
import numpy as np

X = np.array([[1],[2],[3],[4],[5]])
y = np.array([2, 4, 6, 8, 10])

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

model = LinearRegression()
model.fit(X_train, y_train)
predictions = model.predict(X_test)
print(mean_squared_error(y_test, predictions))
```

---

## 25. Automation and Scripting

### File Automation
```python
import os
import shutil
from pathlib import Path

# Rename all .txt files
for f in Path(".").glob("*.txt"):
    f.rename(f.stem + "_backup.txt")

# Copy files
shutil.copy("source.txt", "backup/source.txt")

# Move files
shutil.move("file.txt", "archive/file.txt")

# Delete files/folders
os.remove("file.txt")
shutil.rmtree("old_folder")  # delete folder + contents
```

### Web Scraping (BeautifulSoup)
```bash
pip install requests beautifulsoup4
```
```python
import requests
from bs4 import BeautifulSoup

url = "https://example.com"
response = requests.get(url)
soup = BeautifulSoup(response.text, "html.parser")

# Find elements
title = soup.find("h1").text
links = [a["href"] for a in soup.find_all("a", href=True)]
print(title)
print(links)
```

### Selenium (Browser Automation)
```bash
pip install selenium
```
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://google.com")
search = driver.find_element(By.NAME, "q")
search.send_keys("Python tutorial")
search.submit()
driver.quit()
```

### Task Automation (Schedule)
```bash
pip install schedule
```
```python
import schedule
import time

def job():
    print("Task running...")

schedule.every(10).seconds.do(job)
schedule.every().day.at("09:00").do(job)

while True:
    schedule.run_pending()
    time.sleep(1)
```

---

## 26. Performance Optimization

### Time Complexity Basics

| Operation | Complexity |
|---|---|
| List access by index | O(1) |
| List search | O(n) |
| Dict lookup | O(1) |
| Sort | O(n log n) |

### Profiling
```python
# cProfile
import cProfile
cProfile.run("your_function()")

# timeit
import timeit
timeit.timeit("sum(range(1000))", number=10000)

# line_profiler (pip install line_profiler)
# @profile decorator on function, run: kernprof -l script.py
```

### Optimization Techniques
```python
# Use list comprehensions instead of loops
squares = [x**2 for x in range(1000)]  # faster than loop

# Use generators for large data
gen = (x**2 for x in range(10**6))  # memory efficient

# Use sets for membership testing
items_set = set(items)
if "apple" in items_set:  # O(1) vs O(n) for list

# Use local variables in loops
local_append = list_obj.append  # avoid repeated attribute lookup
for x in range(1000):
    local_append(x)

# Use built-in functions (written in C, very fast)
total = sum(range(1000))  # faster than manual loop
```

---

## 27. Security Basics

### Input Validation
```python
def safe_age(value):
    try:
        age = int(value)
        if age < 0 or age > 150:
            raise ValueError("Age out of range")
        return age
    except ValueError as e:
        print(f"Invalid input: {e}")
        return None
```

### Hashing
```python
import hashlib

password = "my_secret_password"
hashed = hashlib.sha256(password.encode()).hexdigest()
print(hashed)

# For passwords, use bcrypt or argon2
# pip install bcrypt
import bcrypt
hashed_pw = bcrypt.hashpw(password.encode(), bcrypt.gensalt())
bcrypt.checkpw(password.encode(), hashed_pw)  # True
```

### Secure Coding Practices
- Never store passwords in plain text — always hash them.
- Use environment variables for secrets (never hardcode API keys).
- Validate all user inputs to prevent injection attacks.
- Use `https` for all network communication.
- Keep dependencies updated.
```python
# Use environment variables
import os
api_key = os.environ.get("API_KEY")

# Use secrets module for random tokens
import secrets
token = secrets.token_hex(32)
```

---

## 28. Packaging and Deployment

### Creating Packages
```
mypackage/
├── mypackage/
│   ├── __init__.py
│   └── utils.py
├── setup.py
├── README.md
└── requirements.txt
```

### setup.py
```python
from setuptools import setup, find_packages

setup(
    name="mypackage",
    version="1.0.0",
    packages=find_packages(),
    install_requires=["requests>=2.28.0"],
    author="Your Name",
    description="A short description",
)
```
```bash
pip install .         # install locally
python setup.py sdist # create distribution
```

### Docker Basics
```dockerfile
# Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```
```bash
docker build -t myapp .
docker run -p 5000:5000 myapp
```

### Deployment Concepts
- **Heroku** — simple platform deployment (`git push heroku main`)
- **AWS / GCP / Azure** — cloud VMs or containers
- **Railway / Render** — simple web app hosting
- **Gunicorn** — production WSGI server for Flask/Django
```bash
pip install gunicorn
gunicorn app:app --workers 4
```

---

## 29. Version Control

### Git Basics
```bash
git init                        # initialize repo
git status                      # check changes
git add .                       # stage all changes
git add file.py                 # stage specific file
git commit -m "Initial commit"  # commit with message
git log                         # view history
git diff                        # see unstaged changes

git branch                      # list branches
git branch feature-x            # create branch
git checkout feature-x          # switch to branch
git checkout -b feature-x       # create and switch
git merge feature-x             # merge into current branch

git stash                       # save uncommitted changes
git stash pop                   # restore stashed changes
```

### GitHub Workflow
```bash
# Clone a repository
git clone https://github.com/user/repo.git

# Push to remote
git remote add origin https://github.com/user/repo.git
git push -u origin main

# Pull latest changes
git pull origin main

# Fork → Clone → Create branch → Push → Pull Request
```

---

## 30. Best Practices

### Code Readability (PEP 8)
- Use 4 spaces for indentation (no tabs)
- Max 79 characters per line
- Two blank lines between top-level functions/classes
- One blank line between methods
- Use lowercase with underscores for variables/functions: `my_function`
- Use CamelCase for class names: `MyClass`
- Use UPPER_SNAKE_CASE for constants: `MAX_SIZE`

```python
# Bad
def f(x,y):return x+y

# Good
def add_numbers(x, y):
    """Add two numbers and return result."""
    return x + y
```

### Clean Code Principles
- **DRY** (Don't Repeat Yourself) — avoid code duplication, use functions
- **KISS** (Keep It Simple, Stupid) — simple solutions over complex ones
- **YAGNI** (You Aren't Gonna Need It) — don't add features until needed
- **Single Responsibility** — each function does one thing
- Meaningful names for variables, functions, and classes

```python
# Bad
def d(a, b):
    return a * b * 0.15

# Good
def calculate_tax(price, quantity):
    TAX_RATE = 0.15
    return price * quantity * TAX_RATE
```

### Documentation
```python
def fetch_user(user_id: int) -> dict:
    """
    Fetch user data from database.

    Args:
        user_id (int): The unique user identifier.

    Returns:
        dict: A dictionary containing user information.

    Raises:
        ValueError: If user_id is not a positive integer.
    """
    if user_id <= 0:
        raise ValueError("user_id must be a positive integer")
    # ... implementation
```

#### Type Hints
```python
def greet(name: str, age: int) -> str:
    return f"Hello {name}, you are {age} years old."

from typing import List, Dict, Optional, Tuple

def process(items: List[int]) -> Dict[str, int]:
    return {"sum": sum(items), "count": len(items)}

def find(name: str) -> Optional[str]:
    # returns str or None
    pass
```

---

*End of Python Complete Notes*
