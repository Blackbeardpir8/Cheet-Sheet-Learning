# 📘 Complete DSA Notes in Python — Beginner to Advanced

---

## 📋 Table of Contents

1. [Foundations](#1-foundations-must-before-dsa)
   - 1.1 [Programming Basics (Python Focus)](#11-programming-basics-python-focus)
   - 1.2 [Time & Space Complexity](#12-time--space-complexity)
2. [Mathematical & Logic Building](#2-mathematical--logic-building)
3. [Core Data Structures (Linear)](#3-core-data-structures-linear)
   - 3.1 [Arrays](#31-arrays)
   - 3.2 [Strings](#32-strings)
   - 3.3 [Linked List](#33-linked-list)
   - 3.4 [Stack](#34-stack)
   - 3.5 [Queue](#35-queue)
4. [Hashing](#4-hashing)
5. [Recursion & Backtracking](#5-recursion--backtracking)
   - 5.1 [Recursion](#51-recursion)
   - 5.2 [Backtracking](#52-backtracking)
6. [Searching Algorithms](#6-searching-algorithms)
7. [Sorting Algorithms](#7-sorting-algorithms)
   - 7.1 [Basic Sorting](#71-basic-sorting)
   - 7.2 [Efficient Sorting](#72-efficient-sorting)
   - 7.3 [Non-comparison Sorting](#73-non-comparison-sorting)
8. [Trees](#8-trees)
   - 8.1 [Basics](#81-basics)
   - 8.2 [Traversals](#82-traversals)
   - 8.3 [Binary Search Tree (BST)](#83-binary-search-tree-bst)
   - 8.4 [Advanced Trees](#84-advanced-trees)
9. [Heaps (Priority Queue)](#9-heaps-priority-queue)
10. [Graphs](#10-graphs)
    - 10.1 [Basics](#101-basics)
    - 10.2 [Traversals](#102-traversals)
    - 10.3 [Advanced Graph Algorithms](#103-advanced-graph-algorithms)
11. [Greedy Algorithms](#11-greedy-algorithms)
12. [Dynamic Programming](#12-dynamic-programming-very-important)
    - 12.1 [Basics](#121-basics)
    - 12.2 [Techniques](#122-techniques)
    - 12.3 [Classic Problems](#123-classic-problems)
13. [Bit Manipulation (Advanced)](#13-bit-manipulation-advanced)
14. [Advanced Topics](#14-advanced-topics)
    - 14.1 [Sliding Window & Two Pointers](#141-sliding-window--two-pointers-advanced)
    - 14.2 [Divide and Conquer](#142-divide-and-conquer)
    - 14.3 [String Algorithms](#143-string-algorithms)
    - 14.4 [Advanced Data Structures](#144-advanced-data-structures)
15. [Competitive Programming Concepts](#15-competitive-programming-concepts)
16. [System Design Basics](#16-system-design-basics-optional-but-useful)
17. [Practice Strategy](#17-practice-strategy-very-important)

---

# 1. Foundations (Must Before DSA)

## 1.1 Programming Basics (Python Focus)

### Simple Definition
Python basics are the building blocks you need before learning any algorithm or data structure. Think of them like the ABCs before writing sentences.

### Why It Is Used
Without these basics, you cannot write any program. Every DSA solution uses variables, loops, functions, etc.

---

### Variables & Data Types

**Definition:** A variable is a box that stores data. The type of data it stores is called a data type.

**Types:**
- `int` — whole numbers: `5`, `-3`
- `float` — decimal numbers: `3.14`
- `str` — text: `"hello"`
- `bool` — True or False
- `list` — ordered collection: `[1, 2, 3]`
- `tuple` — ordered, unchangeable: `(1, 2)`
- `dict` — key-value pairs: `{"name": "Ali"}`
- `set` — unique items: `{1, 2, 3}`

```python
x = 10           # int
name = "Alice"   # str
pi = 3.14        # float
is_ready = True  # bool
nums = [1, 2, 3] # list
```

**Tips:**
- Python is dynamically typed — no need to declare type
- Use `type(x)` to check the type of any variable

---

### Input / Output

```python
# Output
print("Hello, World!")

# Input
name = input("Enter your name: ")  # Always returns string
age = int(input("Enter age: "))    # Convert to int manually

# Multiple inputs on one line
a, b = map(int, input().split())
```

**Tip:** `input().split()` splits by space — very common in competitive programming.

---

### Operators

```python
# Arithmetic
a + b   # Add
a - b   # Subtract
a * b   # Multiply
a / b   # Divide (returns float)
a // b  # Floor division (returns int)
a % b   # Modulus (remainder)
a ** b  # Exponent (a to the power b)

# Comparison
a == b  # Equal
a != b  # Not equal
a > b   # Greater
a < b   # Less
a >= b  # Greater or equal
a <= b  # Less or equal

# Logical
a and b  # Both must be True
a or b   # At least one True
not a    # Reverses True/False
```

---

### Conditional Statements

```python
x = 10

if x > 0:
    print("Positive")
elif x == 0:
    print("Zero")
else:
    print("Negative")
```

**One-liner (Ternary):**
```python
result = "Even" if x % 2 == 0 else "Odd"
```

---

### Loops

**For loop** — used when you know how many times to loop:
```python
for i in range(5):        # 0, 1, 2, 3, 4
    print(i)

for i in range(1, 6):     # 1, 2, 3, 4, 5
    print(i)

for i in range(0, 10, 2): # 0, 2, 4, 6, 8
    print(i)

# Loop over a list
for item in [10, 20, 30]:
    print(item)
```

**While loop** — used when you loop until a condition is False:
```python
i = 0
while i < 5:
    print(i)
    i += 1
```

**Loop control:**
```python
break     # Stop the loop
continue  # Skip current iteration
pass      # Do nothing (placeholder)
```

---

### Functions

**Definition:** A function is a reusable block of code. Give it a name, and call it whenever needed.

```python
# Basic function
def greet(name):
    return "Hello, " + name

print(greet("Alice"))  # Hello, Alice

# Default arguments
def power(base, exp=2):
    return base ** exp

print(power(3))    # 9
print(power(3, 3)) # 27

# Multiple return values
def min_max(lst):
    return min(lst), max(lst)

lo, hi = min_max([3, 1, 4, 1, 5])
```

**Recursion Basics:**
A function that calls itself. Must have a **base case** to stop.

```python
def factorial(n):
    if n == 0:       # Base case
        return 1
    return n * factorial(n - 1)  # Recursive case

print(factorial(5))  # 120
```

**Tips:**
- Always define the base case first
- Python's default recursion limit is 1000 — use `sys.setrecursionlimit(10000)` if needed

---

### Modules and Packages

**Definition:** A module is a Python file with useful code. A package is a folder of modules.

```python
import math
print(math.sqrt(16))   # 4.0
print(math.ceil(2.3))  # 3
print(math.floor(2.7)) # 2

import sys
sys.setrecursionlimit(10000)

from collections import defaultdict, Counter, deque
from itertools import permutations, combinations
```

**Most used modules in DSA:**
- `math` — math functions
- `sys` — system settings
- `collections` — Counter, deque, defaultdict
- `heapq` — heap (priority queue)
- `itertools` — permutations, combinations

---

## 1.2 Time & Space Complexity

### What is an Algorithm?

**Simple Definition:** An algorithm is a step-by-step solution to a problem. Like a recipe — you follow the steps to get the result.

**Why it matters:** Two algorithms can solve the same problem, but one can be 1000x faster. Complexity analysis helps us pick the best one.

---

### Time Complexity Basics

**Definition:** Time complexity measures how many steps an algorithm takes as input size grows. We don't measure in seconds — we measure in number of operations.

**Example:** If you have a list of `n` items and loop once → you do `n` operations.

---

### Big-O, Big-Theta, Big-Omega

| Notation | Meaning | Used For |
|----------|---------|----------|
| **Big-O (O)** | Upper bound — worst case | Most common in interviews |
| **Big-Theta (Θ)** | Exact bound — average case | Academic analysis |
| **Big-Omega (Ω)** | Lower bound — best case | Rarely used in practice |

**In interviews, always talk about Big-O (worst case).**

---

### Common Complexities

| Complexity | Name | Example |
|------------|------|---------|
| O(1) | Constant | Accessing array index |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Loop through array |
| O(n log n) | Linearithmic | Merge sort |
| O(n²) | Quadratic | Nested loops |
| O(2ⁿ) | Exponential | Recursive subsets |

**From fastest to slowest:**
`O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ)`

```python
# O(1) — constant time
def get_first(arr):
    return arr[0]

# O(n) — linear time
def find_max(arr):
    max_val = arr[0]
    for x in arr:          # loops n times
        if x > max_val:
            max_val = x
    return max_val

# O(n²) — quadratic time
def bubble_sort(arr):
    for i in range(len(arr)):       # n times
        for j in range(len(arr)):   # n times each
            pass  # n * n = n²

# O(log n) — logarithmic time
def binary_search(arr, target):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
```

---

### Space Complexity

**Definition:** How much extra memory (RAM) an algorithm uses as input size grows.

```python
# O(1) space — only uses a few variables
def sum_array(arr):
    total = 0
    for x in arr:
        total += x
    return total

# O(n) space — creates a new list of size n
def double_array(arr):
    return [x * 2 for x in arr]
```

---

### Best, Worst, Average Case

| Case | Definition | Example (Linear Search) |
|------|-----------|------------------------|
| Best | Minimum steps | Target is first element |
| Worst | Maximum steps | Target is last or not found |
| Average | Expected steps | Target is in the middle |

**Tips & Tricks:**
- Ignore constants: `O(2n)` is just `O(n)`
- Ignore lower-order terms: `O(n² + n)` is just `O(n²)`
- Nested loops = multiply complexities: `O(n) × O(n) = O(n²)`
- Sequential steps = add: `O(n) + O(n) = O(2n) = O(n)`

---

# 2. Mathematical & Logic Building

### Simple Definition
Math foundations help you solve many algorithm problems faster and smarter.

---

### Number Systems

```python
# Binary to decimal
print(int("1010", 2))   # 10

# Decimal to binary
print(bin(10))           # 0b1010
print(bin(10)[2:])       # 1010 (without 0b prefix)

# Decimal to hex
print(hex(255))          # 0xff
```

---

### Prime Numbers & Divisibility

```python
# Check if prime — O(sqrt(n))
def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            return False
    return True

# Sieve of Eratosthenes — find all primes up to n
def sieve(n):
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n+1, i):
                is_prime[j] = False
    return [i for i in range(n+1) if is_prime[i]]

print(sieve(30))  # [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
```

**Tip:** Sieve of Eratosthenes is the fastest way to find all primes up to n — O(n log log n).

---

### GCD & LCM

```python
import math

# GCD (Greatest Common Divisor)
print(math.gcd(12, 8))  # 4

# LCM (Lowest Common Multiple)
def lcm(a, b):
    return a * b // math.gcd(a, b)

print(lcm(4, 6))  # 12
```

**Euclidean Algorithm (manual GCD):**
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a
```

---

### Modular Arithmetic

**Definition:** Working with remainders. Very important in competitive programming.

```python
# Basic
print(10 % 3)   # 1

# Modular addition
(a + b) % mod == ((a % mod) + (b % mod)) % mod

# Modular multiplication
(a * b) % mod == ((a % mod) * (b % mod)) % mod

# Modular exponentiation — fast power
print(pow(2, 10, 1000))  # 2^10 mod 1000 = 24

# MOD constant (very common in competitive programming)
MOD = 10**9 + 7
```

---

### Bit Manipulation Basics

```python
a = 5   # binary: 101
b = 3   # binary: 011

print(a & b)   # AND  → 001 = 1
print(a | b)   # OR   → 111 = 7
print(a ^ b)   # XOR  → 110 = 6
print(~a)      # NOT  → -6
print(a << 1)  # Left shift  → 1010 = 10 (multiply by 2)
print(a >> 1)  # Right shift → 10 = 2  (divide by 2)

# Check if bit i is set
def is_bit_set(n, i):
    return (n >> i) & 1 == 1

# Set bit i
def set_bit(n, i):
    return n | (1 << i)

# Clear bit i
def clear_bit(n, i):
    return n & ~(1 << i)
```

---

### Logarithms

**Definition:** `log base 2 of n` answers: "How many times must we halve n to reach 1?"

```python
import math

print(math.log2(8))    # 3.0  → 2^3 = 8
print(math.log10(100)) # 2.0  → 10^2 = 100
print(math.log(8, 2))  # 3.0  → log base 2 of 8
```

**Why important in DSA:** Binary search halves the array each step → O(log n) time.

---

### Recursion Fundamentals

```python
# Power of a number — O(log n) using recursion
def fast_power(base, exp):
    if exp == 0:
        return 1
    if exp % 2 == 0:
        half = fast_power(base, exp // 2)
        return half * half
    return base * fast_power(base, exp - 1)

# Sum of digits
def digit_sum(n):
    if n == 0:
        return 0
    return n % 10 + digit_sum(n // 10)

print(digit_sum(1234))  # 10
```

---

# 3. Core Data Structures (Linear)

## 3.1 Arrays

### Simple Definition
An array (called **list** in Python) is an ordered collection of items stored in sequence. Think of it as a row of boxes, each holding one value.

### Why It Is Used
- Storing and accessing data in order
- Foundation of almost every algorithm
- Fast access by index (O(1))

### Types
- **Static Array** — fixed size (like in C/Java)
- **Dynamic Array** — resizable (Python list is dynamic)

### Advantages
- O(1) access by index
- Simple to use
- Memory efficient

### Disadvantages
- Insertion/deletion in middle is O(n) (have to shift elements)
- Static arrays have fixed size

### Tips & Tricks
- Use `enumerate()` for index + value in loops
- Use slicing `arr[start:end]` to get subarrays
- `arr[::-1]` reverses an array instantly

### Operations

```python
arr = [10, 20, 30, 40, 50]

# Traversal — O(n)
for i, val in enumerate(arr):
    print(f"Index {i}: {val}")

# Insertion — O(n) (shifts elements)
arr.insert(2, 99)   # Insert 99 at index 2
arr.append(60)      # Add to end — O(1)

# Deletion — O(n)
arr.pop(2)          # Remove at index 2
arr.remove(99)      # Remove by value

# Searching — O(n)
print(arr.index(30))    # Returns first index of 30
print(30 in arr)        # True/False

# Sorting
arr.sort()              # Sorts in-place — O(n log n)
sorted_arr = sorted(arr) # Returns new sorted list

# Reversing
arr.reverse()            # In-place
rev = arr[::-1]          # Creates new reversed list
```

---

### Two Pointers Technique

**Definition:** Use two indices (left and right) moving towards each other or in the same direction.

**When to use:** Sorted arrays, pair-sum problems, palindrome checks.

```python
# Check if array has pair with given sum (sorted array)
def has_pair_with_sum(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        curr_sum = arr[left] + arr[right]
        if curr_sum == target:
            return True
        elif curr_sum < target:
            left += 1
        else:
            right -= 1
    return False

arr = [1, 2, 3, 4, 6]
print(has_pair_with_sum(arr, 6))  # True (2 + 4)
```

---

### Sliding Window Technique

**Definition:** Maintain a "window" (subarray) and slide it across the array without recalculating from scratch.

**When to use:** Max/min subarray of size k, longest subarray with condition.

```python
# Maximum sum subarray of size k — O(n)
def max_sum_subarray(arr, k):
    window_sum = sum(arr[:k])   # First window
    max_sum = window_sum

    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]  # Slide window
        max_sum = max(max_sum, window_sum)

    return max_sum

print(max_sum_subarray([2, 1, 5, 1, 3, 2], 3))  # 9
```

---

### Prefix Sum

**Definition:** Store cumulative sums so you can answer range sum queries in O(1).

```python
def build_prefix(arr):
    prefix = [0] * (len(arr) + 1)
    for i in range(len(arr)):
        prefix[i+1] = prefix[i] + arr[i]
    return prefix

def range_sum(prefix, l, r):  # Sum from index l to r (inclusive)
    return prefix[r+1] - prefix[l]

arr = [2, 4, 1, 3, 5]
prefix = build_prefix(arr)
print(range_sum(prefix, 1, 3))  # 4+1+3 = 8
```

---

## 3.2 Strings

### Simple Definition
A string is a sequence of characters (letters, numbers, symbols). In Python, strings are immutable — you can't change them in place.

### Why It Is Used
Text processing, pattern matching, and many interview problems involve strings.

### String Operations

```python
s = "hello world"

# Basic
print(len(s))           # 11
print(s.upper())        # HELLO WORLD
print(s.lower())        # hello world
print(s.strip())        # Remove whitespace from both ends
print(s.split())        # ['hello', 'world']
print(s.replace("l", "L"))  # heLLo worLd
print(s[0])             # h (indexing)
print(s[0:5])           # hello (slicing)
print(s[::-1])          # dlrow olleh (reverse)

# Check
print(s.startswith("hello"))   # True
print(s.endswith("world"))     # True
print("ell" in s)              # True

# Join and split
words = ["hello", "world"]
print(" ".join(words))  # hello world
```

---

### Pattern Matching Basics (Naive)

```python
# Find pattern in text — O(n*m)
def naive_search(text, pattern):
    n, m = len(text), len(pattern)
    positions = []
    for i in range(n - m + 1):
        if text[i:i+m] == pattern:
            positions.append(i)
    return positions

print(naive_search("abcabcabc", "abc"))  # [0, 3, 6]

# Python built-in
s = "abcabcabc"
print(s.find("abc"))     # 0 (first occurrence)
print(s.count("abc"))    # 3 (count occurrences)
```

---

### Anagram Problems

**Definition:** Two strings are anagrams if they have the same characters in any order.

```python
# Method 1: Sort and compare
def is_anagram(s1, s2):
    return sorted(s1) == sorted(s2)

# Method 2: Counter (faster and cleaner)
from collections import Counter

def is_anagram_v2(s1, s2):
    return Counter(s1) == Counter(s2)

print(is_anagram("listen", "silent"))  # True
```

---

### Palindrome Problems

**Definition:** A string that reads the same forward and backward.

```python
# Simple check
def is_palindrome(s):
    return s == s[::-1]

# Two-pointer approach
def is_palindrome_v2(s):
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True

print(is_palindrome("racecar"))  # True
print(is_palindrome("hello"))    # False
```

---

## 3.3 Linked List

### Simple Definition
A linked list is a chain of **nodes**. Each node holds data and a pointer (reference) to the next node. Unlike arrays, nodes are NOT stored in continuous memory.

### Why It Is Used
- Fast insertion/deletion at any position — O(1) if you have the node
- No need to know size in advance
- Used in stacks, queues, and many OS implementations

### Types
- **Singly Linked List** — each node points to the next
- **Doubly Linked List** — each node points to next AND previous
- **Circular Linked List** — last node points back to the first

### Advantages
- Dynamic size
- O(1) insert/delete at head

### Disadvantages
- No random access — O(n) to reach middle
- Extra memory for pointers

---

### Singly Linked List

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    # Insert at beginning — O(1)
    def insert_front(self, data):
        new_node = Node(data)
        new_node.next = self.head
        self.head = new_node

    # Insert at end — O(n)
    def insert_end(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            return
        curr = self.head
        while curr.next:
            curr = curr.next
        curr.next = new_node

    # Delete a node by value — O(n)
    def delete(self, data):
        if not self.head:
            return
        if self.head.data == data:
            self.head = self.head.next
            return
        curr = self.head
        while curr.next:
            if curr.next.data == data:
                curr.next = curr.next.next
                return
            curr = curr.next

    # Reverse linked list — O(n)
    def reverse(self):
        prev = None
        curr = self.head
        while curr:
            next_node = curr.next
            curr.next = prev
            prev = curr
            curr = next_node
        self.head = prev

    # Print list
    def display(self):
        curr = self.head
        while curr:
            print(curr.data, end=" -> ")
            curr = curr.next
        print("None")

# Usage
ll = LinkedList()
ll.insert_end(1)
ll.insert_end(2)
ll.insert_end(3)
ll.display()   # 1 -> 2 -> 3 -> None
ll.reverse()
ll.display()   # 3 -> 2 -> 1 -> None
```

---

### Doubly Linked List

```python
class DNode:
    def __init__(self, data):
        self.data = data
        self.next = None
        self.prev = None

class DoublyLinkedList:
    def __init__(self):
        self.head = None

    def insert_front(self, data):
        new_node = DNode(data)
        if self.head:
            self.head.prev = new_node
        new_node.next = self.head
        self.head = new_node

    def display(self):
        curr = self.head
        while curr:
            print(curr.data, end=" <-> ")
            curr = curr.next
        print("None")
```

---

### Fast & Slow Pointers (Floyd's Cycle Detection)

**Use case:** Detect cycle in linked list, find middle of list.

```python
# Find middle of linked list
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow.data

# Detect cycle
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

---

## 3.4 Stack

### Simple Definition
A stack is like a pile of plates. You can only add to the top or remove from the top. **Last In, First Out (LIFO)**.

### Why It Is Used
- Undo/Redo functionality
- Browser back button
- Expression evaluation
- Function call management

### Types
- Array-based stack
- Linked list-based stack

### Advantages
- O(1) push and pop
- Simple and fast

### Disadvantages
- Only access top element
- Limited access to middle elements

### Implementation

```python
# Using Python list (recommended)
stack = []

stack.append(10)   # push
stack.append(20)
stack.append(30)

print(stack[-1])   # peek (top element) = 30
stack.pop()        # pop removes 30

print(stack)       # [10, 20]

# Check if empty
print(len(stack) == 0)
```

---

### Applications of Stack

**Balanced Parentheses:**
```python
def is_balanced(s):
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}
    for char in s:
        if char in '({[':
            stack.append(char)
        elif char in ')}]':
            if not stack or stack[-1] != mapping[char]:
                return False
            stack.pop()
    return len(stack) == 0

print(is_balanced("({[]})"))  # True
print(is_balanced("({[})"))   # False
```

**Monotonic Stack** — used to find next greater/smaller element:
```python
# Next Greater Element
def next_greater(arr):
    n = len(arr)
    result = [-1] * n
    stack = []  # stores indices

    for i in range(n):
        while stack and arr[stack[-1]] < arr[i]:
            idx = stack.pop()
            result[idx] = arr[i]
        stack.append(i)

    return result

print(next_greater([4, 5, 2, 25]))  # [5, 25, 25, -1]
```

---

## 3.5 Queue

### Simple Definition
A queue is like a line at a ticket counter. First person to join is first to leave. **First In, First Out (FIFO)**.

### Why It Is Used
- Task scheduling
- BFS (Breadth First Search)
- Print spooling
- Cache management

### Types
- **Simple Queue** — basic FIFO
- **Circular Queue** — end connects back to front
- **Deque** — double-ended (add/remove from both ends)
- **Priority Queue** — highest priority served first

### Implementation

```python
from collections import deque

# Simple Queue
q = deque()
q.append(10)    # enqueue (add to right)
q.append(20)
q.append(30)
print(q.popleft())  # dequeue (remove from left) = 10

# Deque (add/remove from both ends)
dq = deque()
dq.append(1)        # add right
dq.appendleft(0)    # add left
dq.pop()            # remove right
dq.popleft()        # remove left

# Priority Queue (min-heap)
import heapq
pq = []
heapq.heappush(pq, 5)
heapq.heappush(pq, 1)
heapq.heappush(pq, 3)
print(heapq.heappop(pq))  # 1 (smallest first)
```

---

# 4. Hashing

### Simple Definition
Hashing converts data into a fixed-size number (hash code) using a hash function. This lets us store and find data in O(1) average time.

### Why It Is Used
- Super fast lookup, insert, and delete
- Frequency counting
- Used in dictionaries and sets

### Hash Tables in Python

Python's `dict` and `set` are built-in hash tables.

```python
# Dictionary — key:value pairs
d = {}
d["apple"] = 5
d["banana"] = 3
print(d["apple"])      # 5
print("apple" in d)    # True
print(d.get("mango", 0))  # 0 (default if not found)

# Loop over dict
for key, value in d.items():
    print(key, value)

# Set — unique values only
s = set()
s.add(1)
s.add(2)
s.add(1)   # duplicate ignored
print(s)   # {1, 2}
print(1 in s)  # True — O(1) lookup
```

---

### Collision Handling

**Definition:** Two keys may produce the same hash. We handle this via:
- **Chaining** — store a list at each bucket
- **Open Addressing** — find the next empty slot

Python handles this automatically — you don't need to worry about it.

---

### Frequency Counting (Very Common Pattern)

```python
from collections import Counter, defaultdict

# Count character frequencies
s = "aabbbcccc"
freq = Counter(s)
print(freq)  # Counter({'c': 4, 'b': 3, 'a': 2})

# Most common
print(freq.most_common(2))  # [('c', 4), ('b', 3)]

# defaultdict — never raises KeyError
from collections import defaultdict
dd = defaultdict(int)
for c in "hello":
    dd[c] += 1
print(dict(dd))  # {'h': 1, 'e': 1, 'l': 2, 'o': 1}
```

---

### Applications

```python
# Two Sum Problem — O(n)
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []

print(two_sum([2, 7, 11, 15], 9))  # [0, 1]

# Find duplicate
def has_duplicate(arr):
    return len(arr) != len(set(arr))

# Count words
from collections import Counter
words = ["apple", "banana", "apple", "cherry", "banana", "apple"]
print(Counter(words))  # Counter({'apple': 3, 'banana': 2, 'cherry': 1})
```

---

# 5. Recursion & Backtracking

## 5.1 Recursion

### Simple Definition
Recursion is when a function calls itself. Every recursive function has two parts:
1. **Base case** — when to stop
2. **Recursive case** — how to break the problem smaller

### Why It Is Used
- Solve problems that have repetitive substructure
- Tree/graph traversal
- Divide and conquer algorithms

### Stack Behavior

Each recursive call is added to the **call stack**. When base case is reached, calls "unwind" back up.

```python
# Factorial
def factorial(n):
    if n <= 1:       # Base case
        return 1
    return n * factorial(n - 1)  # Calls itself with smaller input

# Fibonacci
def fib(n):
    if n <= 1:       # Base cases
        return n
    return fib(n-1) + fib(n-2)

# Sum of list
def list_sum(arr):
    if not arr:
        return 0
    return arr[0] + list_sum(arr[1:])

# Power
def power(base, exp):
    if exp == 0:
        return 1
    return base * power(base, exp - 1)
```

---

### Tail Recursion

**Definition:** The recursive call is the LAST operation in the function. Python doesn't optimize tail recursion (unlike some other languages), but it's still a useful pattern.

```python
# Tail recursive factorial
def factorial_tail(n, accumulator=1):
    if n <= 1:
        return accumulator
    return factorial_tail(n - 1, n * accumulator)  # call is last operation

print(factorial_tail(5))  # 120
```

**Tips:**
- Always define base case first
- Draw the recursion tree to understand flow
- Use `sys.setrecursionlimit()` for deep recursion

---

## 5.2 Backtracking

### Simple Definition
Backtracking is trying all possibilities and **undoing** the choice if it doesn't work. It's like solving a maze — try a path, hit a wall, go back and try another.

### Why It Is Used
- Generate all subsets, permutations, combinations
- Solve constraint problems (N-Queens, Sudoku)

---

### Subsets

```python
def subsets(nums):
    result = []
    def backtrack(start, current):
        result.append(current[:])  # Add current subset
        for i in range(start, len(nums)):
            current.append(nums[i])
            backtrack(i + 1, current)
            current.pop()  # Undo the choice (backtrack)

    backtrack(0, [])
    return result

print(subsets([1, 2, 3]))
# [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

---

### Permutations

```python
def permutations(nums):
    result = []
    def backtrack(current, remaining):
        if not remaining:
            result.append(current[:])
            return
        for i in range(len(remaining)):
            current.append(remaining[i])
            backtrack(current, remaining[:i] + remaining[i+1:])
            current.pop()

    backtrack([], nums)
    return result

print(permutations([1, 2, 3]))
```

---

### Combinations

```python
def combinations(nums, k):
    result = []
    def backtrack(start, current):
        if len(current) == k:
            result.append(current[:])
            return
        for i in range(start, len(nums)):
            current.append(nums[i])
            backtrack(i + 1, current)
            current.pop()

    backtrack(0, [])
    return result

print(combinations([1, 2, 3, 4], 2))
# [[1,2], [1,3], [1,4], [2,3], [2,4], [3,4]]
```

---

### N-Queens Problem

```python
def solve_n_queens(n):
    result = []
    board = [["." for _ in range(n)] for _ in range(n)]

    def is_safe(row, col):
        # Check column
        for r in range(row):
            if board[r][col] == "Q":
                return False
        # Check upper-left diagonal
        r, c = row - 1, col - 1
        while r >= 0 and c >= 0:
            if board[r][c] == "Q":
                return False
            r -= 1; c -= 1
        # Check upper-right diagonal
        r, c = row - 1, col + 1
        while r >= 0 and c < n:
            if board[r][c] == "Q":
                return False
            r -= 1; c += 1
        return True

    def backtrack(row):
        if row == n:
            result.append(["".join(r) for r in board])
            return
        for col in range(n):
            if is_safe(row, col):
                board[row][col] = "Q"
                backtrack(row + 1)
                board[row][col] = "."   # Backtrack

    backtrack(0)
    return result

solutions = solve_n_queens(4)
print(f"Found {len(solutions)} solutions")
```

---

### Sudoku Solver

```python
def solve_sudoku(board):
    def is_valid(board, row, col, num):
        num = str(num)
        # Check row
        if num in board[row]:
            return False
        # Check column
        if num in [board[r][col] for r in range(9)]:
            return False
        # Check 3x3 box
        box_r, box_c = 3 * (row // 3), 3 * (col // 3)
        for r in range(box_r, box_r + 3):
            for c in range(box_c, box_c + 3):
                if board[r][c] == num:
                    return False
        return True

    def backtrack():
        for r in range(9):
            for c in range(9):
                if board[r][c] == ".":
                    for num in range(1, 10):
                        if is_valid(board, r, c, num):
                            board[r][c] = str(num)
                            if backtrack():
                                return True
                            board[r][c] = "."   # Backtrack
                    return False
        return True

    backtrack()
```

---

# 6. Searching Algorithms

### Simple Definition
Searching means finding a target value in a collection.

---

### Linear Search

```python
# O(n) — check every element
def linear_search(arr, target):
    for i in range(len(arr)):
        if arr[i] == target:
            return i
    return -1

print(linear_search([10, 20, 30, 40], 30))  # 2
```

**Best case:** O(1) (target is first) | **Worst case:** O(n) | **Works on unsorted arrays**

---

### Binary Search

**Condition:** Array must be sorted!

```python
# Iterative — O(log n)
def binary_search(arr, target):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1

# Recursive — O(log n)
def binary_search_rec(arr, target, lo, hi):
    if lo > hi:
        return -1
    mid = (lo + hi) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search_rec(arr, target, mid + 1, hi)
    else:
        return binary_search_rec(arr, target, lo, mid - 1)

arr = [1, 3, 5, 7, 9, 11, 13]
print(binary_search(arr, 7))   # 3
```

---

### Variants

```python
import bisect

arr = [1, 3, 5, 7, 9]

# Lower bound — first position where we can insert target (leftmost)
print(bisect.bisect_left(arr, 5))   # 2 (first index >= 5)

# Upper bound — position after last occurrence of target
print(bisect.bisect_right(arr, 5))  # 3 (first index > 5)

# Manual lower bound
def lower_bound(arr, target):
    lo, hi = 0, len(arr)
    while lo < hi:
        mid = (lo + hi) // 2
        if arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid
    return lo
```

---

### Binary Search on Answer

**Concept:** Instead of searching in an array, you binary search on the answer range. Very powerful technique.

```python
# Find minimum eating speed (Koko Eating Bananas)
import math

def min_eating_speed(piles, h):
    def can_finish(speed):
        return sum(math.ceil(p / speed) for p in piles) <= h

    lo, hi = 1, max(piles)
    while lo < hi:
        mid = (lo + hi) // 2
        if can_finish(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
```

---

# 7. Sorting Algorithms

## 7.1 Basic Sorting

### Bubble Sort

**Idea:** Repeatedly swap adjacent elements if they're in the wrong order. Biggest "bubbles" to the top.

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        swapped = False
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        if not swapped:  # Optimization: already sorted
            break
    return arr

# Time: O(n²) | Space: O(1) | Stable: Yes
```

---

### Selection Sort

**Idea:** Find the minimum element and put it at the front. Repeat for the rest.

```python
def selection_sort(arr):
    n = len(arr)
    for i in range(n):
        min_idx = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    return arr

# Time: O(n²) | Space: O(1) | Stable: No
```

---

### Insertion Sort

**Idea:** Build sorted portion one element at a time by inserting each into correct position. Like sorting playing cards.

```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key
    return arr

# Time: O(n²) worst, O(n) best | Space: O(1) | Stable: Yes
# Best for nearly sorted arrays
```

---

## 7.2 Efficient Sorting

### Merge Sort

**Idea:** Divide array in half, sort each half, merge them. Classic **divide and conquer**.

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result

# Time: O(n log n) | Space: O(n) | Stable: Yes
print(merge_sort([38, 27, 43, 3, 9, 82, 10]))
```

---

### Quick Sort

**Idea:** Pick a pivot, partition array so left has smaller and right has larger elements, then recurse.

```python
def quick_sort(arr, lo=0, hi=None):
    if hi is None:
        hi = len(arr) - 1
    if lo < hi:
        pivot_idx = partition(arr, lo, hi)
        quick_sort(arr, lo, pivot_idx - 1)
        quick_sort(arr, pivot_idx + 1, hi)

def partition(arr, lo, hi):
    pivot = arr[hi]
    i = lo - 1
    for j in range(lo, hi):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i+1], arr[hi] = arr[hi], arr[i+1]
    return i + 1

# Time: O(n log n) average, O(n²) worst | Space: O(log n) | Stable: No
```

---

### Heap Sort

**Idea:** Build a max-heap, then repeatedly extract the max to get sorted order.

```python
def heap_sort(arr):
    n = len(arr)

    # Build max heap
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)

    # Extract elements one by one
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)

def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2

    if left < n and arr[left] > arr[largest]:
        largest = left
    if right < n and arr[right] > arr[largest]:
        largest = right

    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)

# Time: O(n log n) | Space: O(1) | Stable: No
```

---

## 7.3 Non-comparison Sorting

### Counting Sort

**Idea:** Count occurrences of each value, then rebuild sorted array.

```python
def counting_sort(arr):
    if not arr:
        return arr
    max_val = max(arr)
    count = [0] * (max_val + 1)

    for num in arr:
        count[num] += 1

    result = []
    for i, c in enumerate(count):
        result.extend([i] * c)
    return result

# Time: O(n + k) where k is range | Space: O(k)
# Best for small integer ranges
print(counting_sort([4, 2, 2, 8, 3, 3, 1]))
```

---

### Radix Sort

**Idea:** Sort digit by digit, from least significant to most significant.

```python
def radix_sort(arr):
    max_val = max(arr)
    exp = 1
    while max_val // exp > 0:
        counting_sort_by_digit(arr, exp)
        exp *= 10
    return arr

def counting_sort_by_digit(arr, exp):
    n = len(arr)
    output = [0] * n
    count = [0] * 10

    for i in range(n):
        index = (arr[i] // exp) % 10
        count[index] += 1

    for i in range(1, 10):
        count[i] += count[i - 1]

    for i in range(n - 1, -1, -1):
        index = (arr[i] // exp) % 10
        output[count[index] - 1] = arr[i]
        count[index] -= 1

    for i in range(n):
        arr[i] = output[i]

# Time: O(d*(n+k)) | Space: O(n+k) | d=digits, k=base
```

---

### Bucket Sort

**Idea:** Distribute elements into buckets, sort each bucket, concatenate.

```python
def bucket_sort(arr):
    if not arr:
        return arr
    min_val, max_val = min(arr), max(arr)
    bucket_range = (max_val - min_val) / len(arr) + 1
    buckets = [[] for _ in range(len(arr))]

    for num in arr:
        idx = int((num - min_val) / bucket_range)
        buckets[idx].append(num)

    result = []
    for bucket in buckets:
        result.extend(sorted(bucket))
    return result

# Time: O(n + k) average | Space: O(n)
```

**Sorting Comparison Table:**

| Algorithm | Best | Average | Worst | Space | Stable |
|-----------|------|---------|-------|-------|--------|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes |

---

# 8. Trees

## 8.1 Basics

### Simple Definition
A tree is a hierarchical data structure with a root node at the top and child nodes branching downward. Like a family tree.

### Tree Terminology
- **Root** — topmost node (no parent)
- **Leaf** — node with no children
- **Parent / Child** — nodes connected directly
- **Height** — longest path from root to leaf
- **Depth** — distance from root to a node
- **Subtree** — any node and all its descendants

### Binary Tree

**Definition:** Each node has at most 2 children (left and right).

```python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None

# Build a tree
root = TreeNode(1)
root.left = TreeNode(2)
root.right = TreeNode(3)
root.left.left = TreeNode(4)
root.left.right = TreeNode(5)
#       1
#      / \
#     2   3
#    / \
#   4   5
```

---

## 8.2 Traversals

### Inorder (Left → Root → Right)

```python
def inorder(root):
    if not root:
        return []
    return inorder(root.left) + [root.val] + inorder(root.right)

# Iterative inorder
def inorder_iter(root):
    result, stack = [], []
    curr = root
    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        result.append(curr.val)
        curr = curr.right
    return result
```

---

### Preorder (Root → Left → Right)

```python
def preorder(root):
    if not root:
        return []
    return [root.val] + preorder(root.left) + preorder(root.right)
```

---

### Postorder (Left → Right → Root)

```python
def postorder(root):
    if not root:
        return []
    return postorder(root.left) + postorder(root.right) + [root.val]
```

---

### Level Order (BFS)

```python
from collections import deque

def level_order(root):
    if not root:
        return []
    result = []
    q = deque([root])
    while q:
        level_size = len(q)
        level = []
        for _ in range(level_size):
            node = q.popleft()
            level.append(node.val)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        result.append(level)
    return result
```

---

## 8.3 Binary Search Tree (BST)

### Simple Definition
A BST is a binary tree where every left child is SMALLER than the parent, and every right child is LARGER. This property allows binary search in the tree.

### Properties
- Inorder traversal of BST gives sorted order
- Search, Insert, Delete: O(log n) average, O(n) worst (unbalanced)

```python
class BST:
    def __init__(self):
        self.root = None

    def insert(self, val):
        self.root = self._insert(self.root, val)

    def _insert(self, node, val):
        if not node:
            return TreeNode(val)
        if val < node.val:
            node.left = self._insert(node.left, val)
        elif val > node.val:
            node.right = self._insert(node.right, val)
        return node

    def search(self, val):
        return self._search(self.root, val)

    def _search(self, node, val):
        if not node:
            return False
        if node.val == val:
            return True
        elif val < node.val:
            return self._search(node.left, val)
        else:
            return self._search(node.right, val)

    def delete(self, val):
        self.root = self._delete(self.root, val)

    def _delete(self, node, val):
        if not node:
            return None
        if val < node.val:
            node.left = self._delete(node.left, val)
        elif val > node.val:
            node.right = self._delete(node.right, val)
        else:
            # Node found
            if not node.left:
                return node.right
            if not node.right:
                return node.left
            # Node has two children — find inorder successor
            successor = node.right
            while successor.left:
                successor = successor.left
            node.val = successor.val
            node.right = self._delete(node.right, successor.val)
        return node
```

---

## 8.4 Advanced Trees

### AVL Tree

**Definition:** Self-balancing BST. After each insert/delete, rotations are performed to keep height balanced. Guarantees O(log n) operations.

```python
class AVLNode:
    def __init__(self, val):
        self.val = val
        self.left = self.right = None
        self.height = 1

def get_height(node):
    return node.height if node else 0

def get_balance(node):
    return get_height(node.left) - get_height(node.right) if node else 0

def rotate_right(y):
    x = y.left
    T2 = x.right
    x.right = y
    y.left = T2
    y.height = 1 + max(get_height(y.left), get_height(y.right))
    x.height = 1 + max(get_height(x.left), get_height(x.right))
    return x

def rotate_left(x):
    y = x.right
    T2 = y.left
    y.left = x
    x.right = T2
    x.height = 1 + max(get_height(x.left), get_height(x.right))
    y.height = 1 + max(get_height(y.left), get_height(y.right))
    return y

def avl_insert(root, val):
    if not root:
        return AVLNode(val)
    if val < root.val:
        root.left = avl_insert(root.left, val)
    elif val > root.val:
        root.right = avl_insert(root.right, val)
    else:
        return root

    root.height = 1 + max(get_height(root.left), get_height(root.right))
    balance = get_balance(root)

    # LL case
    if balance > 1 and val < root.left.val:
        return rotate_right(root)
    # RR case
    if balance < -1 and val > root.right.val:
        return rotate_left(root)
    # LR case
    if balance > 1 and val > root.left.val:
        root.left = rotate_left(root.left)
        return rotate_right(root)
    # RL case
    if balance < -1 and val < root.right.val:
        root.right = rotate_right(root.right)
        return rotate_left(root)

    return root
```

---

### Segment Tree

**Definition:** A tree for range queries (sum, min, max) and point updates in O(log n).

```python
class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)
        self.build(arr, 0, 0, self.n - 1)

    def build(self, arr, node, start, end):
        if start == end:
            self.tree[node] = arr[start]
        else:
            mid = (start + end) // 2
            self.build(arr, 2*node+1, start, mid)
            self.build(arr, 2*node+2, mid+1, end)
            self.tree[node] = self.tree[2*node+1] + self.tree[2*node+2]

    def query(self, node, start, end, l, r):  # Range sum query
        if r < start or end < l:
            return 0
        if l <= start and end <= r:
            return self.tree[node]
        mid = (start + end) // 2
        return (self.query(2*node+1, start, mid, l, r) +
                self.query(2*node+2, mid+1, end, l, r))

    def update(self, node, start, end, idx, val):
        if start == end:
            self.tree[node] = val
        else:
            mid = (start + end) // 2
            if idx <= mid:
                self.update(2*node+1, start, mid, idx, val)
            else:
                self.update(2*node+2, mid+1, end, idx, val)
            self.tree[node] = self.tree[2*node+1] + self.tree[2*node+2]

    def range_sum(self, l, r):
        return self.query(0, 0, self.n - 1, l, r)

    def point_update(self, idx, val):
        self.update(0, 0, self.n - 1, idx, val)

arr = [1, 3, 5, 7, 9, 11]
st = SegmentTree(arr)
print(st.range_sum(1, 3))   # 3+5+7 = 15
```

---

### Fenwick Tree (Binary Indexed Tree)

**Definition:** Compact data structure for prefix sums and point updates in O(log n). More memory efficient than segment tree.

```python
class FenwickTree:
    def __init__(self, n):
        self.n = n
        self.tree = [0] * (n + 1)

    def update(self, i, delta):  # 1-indexed
        while i <= self.n:
            self.tree[i] += delta
            i += i & (-i)   # Add least significant bit

    def prefix_sum(self, i):  # Sum from 1 to i
        total = 0
        while i > 0:
            total += self.tree[i]
            i -= i & (-i)   # Remove least significant bit
        return total

    def range_sum(self, l, r):  # Sum from l to r
        return self.prefix_sum(r) - self.prefix_sum(l - 1)

ft = FenwickTree(6)
arr = [1, 3, 5, 7, 9, 11]
for i, val in enumerate(arr, 1):
    ft.update(i, val)
print(ft.range_sum(2, 4))  # 3+5+7 = 15
```

---

### Trie (Prefix Tree)

**Definition:** A tree where each node represents a character. Used for efficient prefix/word search.

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end

    def starts_with(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True

trie = Trie()
trie.insert("apple")
trie.insert("app")
print(trie.search("app"))       # True
print(trie.search("ap"))        # False
print(trie.starts_with("app"))  # True
```

---

# 9. Heaps (Priority Queue)

### Simple Definition
A heap is a complete binary tree where each parent is either smaller (min-heap) or larger (max-heap) than its children.

### Why It Is Used
- Get min/max in O(1)
- Insert and delete in O(log n)
- Used for priority queues, scheduling, k-th element problems

### Types
- **Min Heap** — smallest at top (Python's `heapq`)
- **Max Heap** — largest at top (negate values in Python)

### Advantages
- O(1) access to min/max
- O(log n) insert/delete

### Disadvantages
- Not sorted — can only access top element easily

```python
import heapq

# MIN HEAP
min_heap = []
heapq.heappush(min_heap, 5)
heapq.heappush(min_heap, 1)
heapq.heappush(min_heap, 3)

print(min_heap[0])             # 1 (smallest)
print(heapq.heappop(min_heap)) # 1 (removes and returns smallest)

# MAX HEAP (negate values)
max_heap = []
heapq.heappush(max_heap, -5)
heapq.heappush(max_heap, -1)
heapq.heappush(max_heap, -3)

print(-heapq.heappop(max_heap)) # 5 (largest)

# Convert list to heap — O(n)
arr = [3, 1, 4, 1, 5, 9]
heapq.heapify(arr)

# Nlargest and Nsmallest — O(n log k)
print(heapq.nlargest(3, [3,1,4,1,5,9]))   # [9, 5, 4]
print(heapq.nsmallest(3, [3,1,4,1,5,9]))  # [1, 1, 3]
```

---

### Applications

```python
# K largest elements
def k_largest(arr, k):
    return heapq.nlargest(k, arr)

# K smallest elements
def k_smallest(arr, k):
    return heapq.nsmallest(k, arr)

# Median of data stream
class MedianFinder:
    def __init__(self):
        self.lo = []  # Max heap (lower half)
        self.hi = []  # Min heap (upper half)

    def add_num(self, num):
        heapq.heappush(self.lo, -num)
        heapq.heappush(self.hi, -heapq.heappop(self.lo))
        if len(self.lo) < len(self.hi):
            heapq.heappush(self.lo, -heapq.heappop(self.hi))

    def find_median(self):
        if len(self.lo) > len(self.hi):
            return -self.lo[0]
        return (-self.lo[0] + self.hi[0]) / 2
```

---

# 10. Graphs

## 10.1 Basics

### Simple Definition
A graph is a collection of **nodes (vertices)** connected by **edges**. Think of cities (nodes) connected by roads (edges).

### Graph Representation

```python
# Adjacency List (most common, memory efficient)
graph = {
    0: [1, 2],
    1: [0, 3],
    2: [0, 3],
    3: [1, 2]
}

# For weighted graphs
weighted_graph = {
    0: [(1, 4), (2, 1)],   # (neighbor, weight)
    1: [(0, 4), (3, 1)],
    2: [(0, 1), (3, 5)],
    3: [(1, 1), (2, 5)]
}

# Adjacency Matrix
n = 4
adj_matrix = [[0] * n for _ in range(n)]
adj_matrix[0][1] = 1
adj_matrix[1][0] = 1  # Undirected

# Build adjacency list from edges
def build_graph(n, edges, directed=False):
    graph = {i: [] for i in range(n)}
    for u, v in edges:
        graph[u].append(v)
        if not directed:
            graph[v].append(u)
    return graph
```

### Types
- **Directed** — edges have direction (one-way)
- **Undirected** — edges go both ways
- **Weighted** — edges have weights (distances)
- **Unweighted** — all edges equal

---

## 10.2 Traversals

### BFS (Breadth First Search)

**Idea:** Explore level by level using a queue. Like spreading ripples in water.

```python
from collections import deque

def bfs(graph, start):
    visited = set([start])
    queue = deque([start])
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return order

# Shortest path (unweighted graph)
def bfs_shortest_path(graph, start, end):
    visited = {start}
    queue = deque([(start, [start])])
    while queue:
        node, path = queue.popleft()
        if node == end:
            return path
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, path + [neighbor]))
    return []
```

---

### DFS (Depth First Search)

**Idea:** Go as deep as possible along each path before backtracking. Like exploring a maze.

```python
# Recursive DFS
def dfs(graph, node, visited=None):
    if visited is None:
        visited = set()
    visited.add(node)
    print(node, end=" ")
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)

# Iterative DFS
def dfs_iter(graph, start):
    visited = set()
    stack = [start]
    order = []

    while stack:
        node = stack.pop()
        if node not in visited:
            visited.add(node)
            order.append(node)
            for neighbor in graph[node]:
                if neighbor not in visited:
                    stack.append(neighbor)
    return order
```

---

## 10.3 Advanced Graph Algorithms

### Dijkstra's Algorithm

**Purpose:** Shortest path from one source to all nodes in a weighted graph (no negative weights).

```python
import heapq

def dijkstra(graph, start):
    dist = {node: float('inf') for node in graph}
    dist[start] = 0
    pq = [(0, start)]  # (distance, node)

    while pq:
        curr_dist, node = heapq.heappop(pq)
        if curr_dist > dist[node]:
            continue
        for neighbor, weight in graph[node]:
            new_dist = curr_dist + weight
            if new_dist < dist[neighbor]:
                dist[neighbor] = new_dist
                heapq.heappush(pq, (new_dist, neighbor))
    return dist

# Time: O((V + E) log V)
graph = {
    'A': [('B', 1), ('C', 4)],
    'B': [('A', 1), ('C', 2), ('D', 5)],
    'C': [('A', 4), ('B', 2), ('D', 1)],
    'D': [('B', 5), ('C', 1)]
}
print(dijkstra(graph, 'A'))
```

---

### Bellman-Ford Algorithm

**Purpose:** Shortest path that handles negative weights. Also detects negative cycles.

```python
def bellman_ford(vertices, edges, source):
    dist = [float('inf')] * vertices
    dist[source] = 0

    # Relax all edges (V-1) times
    for _ in range(vertices - 1):
        for u, v, w in edges:
            if dist[u] != float('inf') and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w

    # Check for negative cycle
    for u, v, w in edges:
        if dist[u] != float('inf') and dist[u] + w < dist[v]:
            print("Negative cycle detected!")
            return None
    return dist

# Time: O(V * E)
```

---

### Floyd-Warshall Algorithm

**Purpose:** Shortest paths between ALL pairs of nodes.

```python
def floyd_warshall(dist):
    n = len(dist)
    for k in range(n):
        for i in range(n):
            for j in range(n):
                dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
    return dist

# Time: O(V³) | Space: O(V²)
INF = float('inf')
graph = [
    [0, 3, INF, 5],
    [2, 0, INF, 4],
    [INF, 1, 0, INF],
    [INF, INF, 2, 0]
]
result = floyd_warshall(graph)
```

---

### Topological Sorting

**Purpose:** Order nodes in a Directed Acyclic Graph (DAG) so that for every edge u→v, u comes before v. Used in task scheduling.

```python
from collections import deque

def topological_sort_bfs(graph, in_degree, n):
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    result = []

    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)

    if len(result) != n:
        return []  # Cycle detected
    return result

# DFS-based Topological Sort
def topological_sort_dfs(graph, n):
    visited = [False] * n
    stack = []

    def dfs(v):
        visited[v] = True
        for u in graph[v]:
            if not visited[u]:
                dfs(u)
        stack.append(v)

    for i in range(n):
        if not visited[i]:
            dfs(i)

    return stack[::-1]
```

---

### Cycle Detection

```python
# Undirected graph — using DFS
def has_cycle_undirected(graph, n):
    visited = [False] * n

    def dfs(node, parent):
        visited[node] = True
        for neighbor in graph[node]:
            if not visited[neighbor]:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True
        return False

    for i in range(n):
        if not visited[i]:
            if dfs(i, -1):
                return True
    return False

# Directed graph — using color marking
def has_cycle_directed(graph, n):
    color = [0] * n  # 0=white, 1=gray(in stack), 2=black(done)

    def dfs(node):
        color[node] = 1
        for neighbor in graph[node]:
            if color[neighbor] == 1:
                return True
            if color[neighbor] == 0 and dfs(neighbor):
                return True
        color[node] = 2
        return False

    for i in range(n):
        if color[i] == 0:
            if dfs(i):
                return True
    return False
```

---

### Minimum Spanning Tree

#### Kruskal's Algorithm

**Idea:** Sort all edges by weight, add edge if it doesn't form a cycle (using Union-Find).

```python
def kruskal(n, edges):
    edges.sort(key=lambda x: x[2])  # Sort by weight
    parent = list(range(n))
    rank = [0] * n

    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])  # Path compression
        return parent[x]

    def union(x, y):
        px, py = find(x), find(y)
        if px == py:
            return False
        if rank[px] < rank[py]:
            px, py = py, px
        parent[py] = px
        if rank[px] == rank[py]:
            rank[px] += 1
        return True

    mst = []
    for u, v, w in edges:
        if union(u, v):
            mst.append((u, v, w))
    return mst
```

---

#### Prim's Algorithm

**Idea:** Start from any node, greedily add the cheapest edge connecting the MST to a new node.

```python
def prim(graph, n):
    visited = [False] * n
    min_heap = [(0, 0)]  # (weight, node)
    mst_cost = 0

    while min_heap:
        weight, node = heapq.heappop(min_heap)
        if visited[node]:
            continue
        visited[node] = True
        mst_cost += weight
        for neighbor, w in graph[node]:
            if not visited[neighbor]:
                heapq.heappush(min_heap, (w, neighbor))
    return mst_cost
```

---

### Union-Find (Disjoint Set)

**Definition:** Efficiently tracks which elements are in the same connected component.

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.components = n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        self.components -= 1
        return True

    def connected(self, x, y):
        return self.find(x) == self.find(y)
```

---

# 11. Greedy Algorithms

### Simple Definition
Greedy algorithms make the locally best choice at each step, hoping it leads to the globally best solution. Like always picking the closest restaurant — it may not always be best, but often works.

### Why It Is Used
- Simpler and faster than DP for certain problems
- Works when greedy choice property holds

---

### Activity Selection

```python
def activity_selection(activities):
    # Sort by end time
    activities.sort(key=lambda x: x[1])
    selected = [activities[0]]
    last_end = activities[0][1]

    for start, end in activities[1:]:
        if start >= last_end:
            selected.append((start, end))
            last_end = end
    return selected

activities = [(1,3), (2,5), (4,7), (6,9), (8,10)]
print(activity_selection(activities))  # [(1,3), (4,7), (8,10)]
```

---

### Fractional Knapsack

```python
def fractional_knapsack(items, capacity):
    # items = [(value, weight), ...]
    items.sort(key=lambda x: x[0]/x[1], reverse=True)  # Sort by value/weight
    total_value = 0.0

    for value, weight in items:
        if capacity >= weight:
            total_value += value
            capacity -= weight
        else:
            total_value += value * (capacity / weight)
            break
    return total_value

items = [(60, 10), (100, 20), (120, 30)]
print(fractional_knapsack(items, 50))  # 240.0
```

---

### Huffman Coding

```python
import heapq

def huffman_coding(freq):
    heap = [[f, [char, ""]] for char, f in freq.items()]
    heapq.heapify(heap)

    while len(heap) > 1:
        lo = heapq.heappop(heap)
        hi = heapq.heappop(heap)
        for pair in lo[1:]:
            pair[1] = '0' + pair[1]
        for pair in hi[1:]:
            pair[1] = '1' + pair[1]
        heapq.heappush(heap, [lo[0] + hi[0]] + lo[1:] + hi[1:])

    return sorted(heapq.heappop(heap)[1:], key=lambda p: len(p[1]))

freq = {'a': 5, 'b': 2, 'c': 1, 'd': 3}
print(huffman_coding(freq))
```

---

### Interval Problems

```python
# Merge overlapping intervals
def merge_intervals(intervals):
    intervals.sort()
    merged = [intervals[0]]

    for start, end in intervals[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged

print(merge_intervals([[1,3],[2,6],[8,10],[15,18]]))
# [[1,6], [8,10], [15,18]]
```

---

# 12. Dynamic Programming (Very Important)

## 12.1 Basics

### Simple Definition
Dynamic Programming (DP) solves problems by breaking them into smaller subproblems and **storing results** so you don't compute the same thing twice. It's "smart recursion."

### Two Properties Required:
1. **Overlapping Subproblems** — same subproblem solved multiple times
2. **Optimal Substructure** — optimal solution built from optimal subproblems

---

## 12.2 Techniques

### Memoization (Top-Down)

```python
# Fibonacci with memoization
from functools import lru_cache

@lru_cache(maxsize=None)
def fib_memo(n):
    if n <= 1:
        return n
    return fib_memo(n-1) + fib_memo(n-2)

# Manual memo with dict
def fib(n, memo={}):
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]
```

---

### Tabulation (Bottom-Up)

```python
# Fibonacci with tabulation
def fib_tab(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# Space optimized
def fib_opt(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
    return a
```

---

## 12.3 Classic Problems

### 0/1 Knapsack

```python
def knapsack_01(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        for w in range(capacity + 1):
            dp[i][w] = dp[i-1][w]  # Don't include item i
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i][w],
                               dp[i-1][w - weights[i-1]] + values[i-1])
    return dp[n][capacity]

weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
print(knapsack_01(weights, values, 7))  # 9
```

---

### Unbounded Knapsack

```python
def knapsack_unbounded(weights, values, capacity):
    dp = [0] * (capacity + 1)
    for w in range(1, capacity + 1):
        for i in range(len(weights)):
            if weights[i] <= w:
                dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    return dp[capacity]
```

---

### Longest Common Subsequence (LCS)

```python
def lcs(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[m][n]

print(lcs("abcde", "ace"))  # 3
```

---

### Longest Increasing Subsequence (LIS)

```python
# O(n²) DP
def lis_dp(arr):
    n = len(arr)
    dp = [1] * n
    for i in range(1, n):
        for j in range(i):
            if arr[j] < arr[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)

# O(n log n) using binary search
import bisect

def lis_fast(arr):
    tails = []
    for x in arr:
        pos = bisect.bisect_left(tails, x)
        if pos == len(tails):
            tails.append(x)
        else:
            tails[pos] = x
    return len(tails)

print(lis_fast([10, 9, 2, 5, 3, 7, 101, 18]))  # 4
```

---

### Matrix Chain Multiplication

```python
def matrix_chain(p):
    n = len(p) - 1
    dp = [[0] * n for _ in range(n)]

    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            dp[i][j] = float('inf')
            for k in range(i, j):
                cost = dp[i][k] + dp[k+1][j] + p[i]*p[k+1]*p[j+1]
                dp[i][j] = min(dp[i][j], cost)
    return dp[0][n-1]

print(matrix_chain([40, 20, 30, 10, 30]))  # 26000
```

---

### DP on Grids

```python
# Minimum path sum in grid
def min_path_sum(grid):
    m, n = len(grid), len(grid[0])
    dp = [[0]*n for _ in range(m)]
    dp[0][0] = grid[0][0]

    for i in range(1, m):
        dp[i][0] = dp[i-1][0] + grid[i][0]
    for j in range(1, n):
        dp[0][j] = dp[0][j-1] + grid[0][j]

    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])
    return dp[m-1][n-1]

# Unique paths
def unique_paths(m, n):
    dp = [[1]*n for _ in range(m)]
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    return dp[m-1][n-1]
```

---

### DP on Trees

```python
# Maximum path sum in binary tree
def max_path_sum(root):
    max_sum = [float('-inf')]

    def dfs(node):
        if not node:
            return 0
        left = max(dfs(node.left), 0)
        right = max(dfs(node.right), 0)
        max_sum[0] = max(max_sum[0], node.val + left + right)
        return node.val + max(left, right)

    dfs(root)
    return max_sum[0]
```

---

# 13. Bit Manipulation (Advanced)

### Simple Definition
Bit manipulation uses binary operations directly on bits of numbers. Very fast and useful for competitive programming.

### Bitwise Operators

```python
a, b = 12, 10   # 12 = 1100, 10 = 1010

print(a & b)    # AND  = 1000 = 8
print(a | b)    # OR   = 1110 = 14
print(a ^ b)    # XOR  = 0110 = 6
print(~a)       # NOT  = -13 (two's complement)
print(a << 1)   # Left shift  = 11000 = 24
print(a >> 1)   # Right shift = 110 = 6
```

### XOR Tricks

```python
# XOR of a number with itself = 0
# XOR of a number with 0 = number itself

# Find single number (all others appear twice)
def single_number(nums):
    result = 0
    for n in nums:
        result ^= n
    return result

print(single_number([4, 1, 2, 1, 2]))  # 4

# Swap without temp
a, b = 5, 7
a ^= b
b ^= a
a ^= b
print(a, b)  # 7 5
```

### Subsets Using Bits

```python
# Generate all subsets using bitmask
def subsets_bitmask(nums):
    n = len(nums)
    result = []
    for mask in range(1 << n):  # 0 to 2^n - 1
        subset = []
        for i in range(n):
            if mask & (1 << i):
                subset.append(nums[i])
        result.append(subset)
    return result

print(subsets_bitmask([1, 2, 3]))
```

### Bit Masking Tricks

```python
# Check if n is power of 2
def is_power_of_2(n):
    return n > 0 and (n & (n - 1)) == 0

# Count set bits (Brian Kernighan's algorithm)
def count_bits(n):
    count = 0
    while n:
        n &= (n - 1)  # Remove last set bit
        count += 1
    return count

# Get lowest set bit
def lowest_set_bit(n):
    return n & (-n)

print(is_power_of_2(16))  # True
print(count_bits(13))     # 3 (1101 has 3 ones)
```

---

# 14. Advanced Topics

## 14.1 Sliding Window & Two Pointers (Advanced)

### Fixed Window

```python
# Max sum of subarray of size k
def max_sum_fixed(arr, k):
    window = sum(arr[:k])
    result = window
    for i in range(k, len(arr)):
        window += arr[i] - arr[i-k]
        result = max(result, window)
    return result
```

### Variable Window

```python
# Longest substring without repeating characters
def length_of_longest_substring(s):
    char_set = set()
    left = 0
    max_len = 0

    for right in range(len(s)):
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        char_set.add(s[right])
        max_len = max(max_len, right - left + 1)
    return max_len

print(length_of_longest_substring("abcabcbb"))  # 3

# Minimum window substring
def min_window(s, t):
    from collections import Counter
    need = Counter(t)
    missing = len(t)
    best = float('inf'), 0, 0
    left = 0

    for right, char in enumerate(s, 1):
        if need[char] > 0:
            missing -= 1
        need[char] -= 1

        if missing == 0:
            while need[s[left]] < 0:
                need[s[left]] += 1
                left += 1
            if right - left < best[0]:
                best = right - left, left, right
            need[s[left]] += 1
            missing += 1
            left += 1

    return s[best[1]:best[2]] if best[0] != float('inf') else ""
```

---

## 14.2 Divide and Conquer

### Merge Sort Concept

```python
# Divide: split array into halves
# Conquer: sort each half recursively
# Combine: merge sorted halves
# Time: O(n log n) | Space: O(n)

# Count inversions using merge sort
def count_inversions(arr):
    if len(arr) <= 1:
        return arr, 0
    mid = len(arr) // 2
    left, left_inv = count_inversions(arr[:mid])
    right, right_inv = count_inversions(arr[mid:])
    merged, split_inv = merge_count(left, right)
    return merged, left_inv + right_inv + split_inv

def merge_count(left, right):
    result, inversions = [], 0
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i]); i += 1
        else:
            result.append(right[j]); j += 1
            inversions += len(left) - i
    result.extend(left[i:]); result.extend(right[j:])
    return result, inversions
```

---

## 14.3 String Algorithms

### KMP Algorithm (Knuth-Morris-Pratt)

**Purpose:** Find pattern in text in O(n+m) instead of O(n*m).

```python
def kmp_search(text, pattern):
    n, m = len(text), len(pattern)
    lps = compute_lps(pattern)
    i = j = 0
    positions = []

    while i < n:
        if text[i] == pattern[j]:
            i += 1; j += 1
        if j == m:
            positions.append(i - j)
            j = lps[j - 1]
        elif i < n and text[i] != pattern[j]:
            if j != 0:
                j = lps[j - 1]
            else:
                i += 1
    return positions

def compute_lps(pattern):
    m = len(pattern)
    lps = [0] * m
    length = 0
    i = 1
    while i < m:
        if pattern[i] == pattern[length]:
            length += 1
            lps[i] = length
            i += 1
        else:
            if length != 0:
                length = lps[length - 1]
            else:
                lps[i] = 0
                i += 1
    return lps

print(kmp_search("aabaacaabaa", "aab"))  # [0, 6]
```

---

### Rabin-Karp Algorithm

**Purpose:** Rolling hash for pattern matching — O(n+m) average.

```python
def rabin_karp(text, pattern):
    n, m = len(text), len(pattern)
    base, mod = 256, 10**9 + 7
    h_pat = 0; h_txt = 0; power = 1
    positions = []

    for i in range(m - 1):
        power = (power * base) % mod

    for i in range(m):
        h_pat = (base * h_pat + ord(pattern[i])) % mod
        h_txt = (base * h_txt + ord(text[i])) % mod

    for i in range(n - m + 1):
        if h_pat == h_txt:
            if text[i:i+m] == pattern:
                positions.append(i)
        if i < n - m:
            h_txt = (base * (h_txt - ord(text[i]) * power) + ord(text[i + m])) % mod
            h_txt = (h_txt + mod) % mod
    return positions
```

---

### Z Algorithm

**Purpose:** Find all positions where pattern occurs in text using Z-array.

```python
def z_algorithm(s):
    n = len(s)
    z = [0] * n
    z[0] = n
    l, r = 0, 0
    for i in range(1, n):
        if i < r:
            z[i] = min(r - i, z[i - l])
        while i + z[i] < n and s[z[i]] == s[i + z[i]]:
            z[i] += 1
        if i + z[i] > r:
            l, r = i, i + z[i]
    return z

def z_search(text, pattern):
    s = pattern + "$" + text
    z = z_algorithm(s)
    m = len(pattern)
    return [i - m - 1 for i in range(m + 1, len(s)) if z[i] == m]
```

---

## 14.4 Advanced Data Structures

### Sparse Table

**Purpose:** Range Minimum/Maximum Queries in O(1) after O(n log n) preprocessing.

```python
import math

class SparseTable:
    def __init__(self, arr):
        n = len(arr)
        k = int(math.log2(n)) + 1
        self.st = [[float('inf')] * n for _ in range(k)]
        self.st[0] = arr[:]

        for j in range(1, k):
            for i in range(n - (1 << j) + 1):
                self.st[j][i] = min(self.st[j-1][i],
                                    self.st[j-1][i + (1 << (j-1))])

    def query(self, l, r):  # Range minimum query O(1)
        k = int(math.log2(r - l + 1))
        return min(self.st[k][l], self.st[k][r - (1 << k) + 1])

arr = [2, 4, 3, 1, 6, 7, 8, 9]
st = SparseTable(arr)
print(st.query(0, 4))   # 1 (minimum in range [0,4])
```

---

### Segment Tree with Lazy Propagation

**Purpose:** Range updates + range queries in O(log n).

```python
class LazySegTree:
    def __init__(self, n):
        self.n = n
        self.tree = [0] * (4 * n)
        self.lazy = [0] * (4 * n)

    def push_down(self, node, start, end):
        if self.lazy[node] != 0:
            mid = (start + end) // 2
            self.tree[2*node] += self.lazy[node] * (mid - start + 1)
            self.tree[2*node+1] += self.lazy[node] * (end - mid)
            self.lazy[2*node] += self.lazy[node]
            self.lazy[2*node+1] += self.lazy[node]
            self.lazy[node] = 0

    def range_update(self, node, start, end, l, r, val):
        if r < start or end < l:
            return
        if l <= start and end <= r:
            self.tree[node] += val * (end - start + 1)
            self.lazy[node] += val
            return
        self.push_down(node, start, end)
        mid = (start + end) // 2
        self.range_update(2*node, start, mid, l, r, val)
        self.range_update(2*node+1, mid+1, end, l, r, val)
        self.tree[node] = self.tree[2*node] + self.tree[2*node+1]

    def range_query(self, node, start, end, l, r):
        if r < start or end < l:
            return 0
        if l <= start and end <= r:
            return self.tree[node]
        self.push_down(node, start, end)
        mid = (start + end) // 2
        return (self.range_query(2*node, start, mid, l, r) +
                self.range_query(2*node+1, mid+1, end, l, r))
```

---

### Advanced Tries

```python
# Trie with wildcards (. matches any character)
class AdvancedTrie:
    def __init__(self):
        self.root = {}

    def insert(self, word):
        node = self.root
        for c in word:
            node = node.setdefault(c, {})
        node['$'] = True

    def search(self, word):
        return self._search(word, 0, self.root)

    def _search(self, word, idx, node):
        if idx == len(word):
            return '$' in node
        c = word[idx]
        if c == '.':
            return any(self._search(word, idx+1, child)
                       for k, child in node.items() if k != '$')
        if c not in node:
            return False
        return self._search(word, idx+1, node[c])
```

---

# 15. Competitive Programming Concepts

### Fast I/O

```python
import sys
input = sys.stdin.readline  # Much faster than input()

# Read all input at once
data = sys.stdin.read().split()
idx = 0

def read_int():
    global idx
    idx += 1
    return int(data[idx-1])

# Output buffering
import sys
output = []
output.append("Hello World")
print('\n'.join(output))
```

### Complexity Optimization

- O(n²) → O(n log n) by using sorting or heaps
- O(n²) → O(n) by using hash maps for lookups
- Memoization converts exponential to polynomial
- Two pointers convert O(n²) search to O(n)
- Prefix sums convert O(n) range queries to O(1)

### Pattern Recognition

| Pattern | Technique |
|---------|-----------|
| "find max/min k elements" | Heap |
| "sorted array + target" | Binary Search |
| "subarray sum" | Prefix Sum or Sliding Window |
| "all permutations/subsets" | Backtracking |
| "shortest path" | BFS (unweighted) / Dijkstra (weighted) |
| "repeated subproblems" | Dynamic Programming |
| "overlapping intervals" | Greedy + Sorting |
| "count connected components" | Union-Find or DFS |

### Problem-Solving Strategy

1. Read problem carefully — understand constraints
2. Identify the pattern (see table above)
3. Start with brute force — write it out
4. Optimize step by step
5. Test with edge cases (empty input, single element, max size)
6. Check time complexity vs constraints:
   - n ≤ 10^6 → O(n) or O(n log n) needed
   - n ≤ 10^3 → O(n²) is fine
   - n ≤ 20 → O(2^n) or O(n!) may be acceptable

---

# 16. System Design Basics (Optional but Useful)

### Scalability Basics

```
Vertical Scaling   → Add more power to single machine (RAM, CPU)
Horizontal Scaling → Add more machines (distribute load)
Load Balancer      → Distributes incoming requests across servers
```

### Caching

```python
# Simple LRU Cache using OrderedDict
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity

    def get(self, key):
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)  # Remove LRU (first item)

cache = LRUCache(2)
cache.put(1, 1)
cache.put(2, 2)
print(cache.get(1))   # 1
cache.put(3, 3)       # Evicts key 2
print(cache.get(2))   # -1 (evicted)
```

### Data Handling

```
Relational DB   → Structured data with relationships (SQL)
NoSQL           → Flexible schema (MongoDB, Redis)
Sharding        → Split data across multiple databases
Replication     → Copy data across multiple servers for reliability
```

---

# 17. Practice Strategy (Very Important)

### Learning Path

1. **Easy Problems First** — build confidence and learn patterns
2. **Topic-Wise Practice** — master one topic before moving to next
3. **Medium Problems** — once comfortable with topic
4. **Hard Problems** — after solving 50+ mediums
5. **Mock Interviews** — simulate real interview pressure

### Recommended Order

1. Arrays + Two Pointers + Sliding Window
2. Strings + Hashing
3. Recursion + Backtracking
4. Sorting + Searching (Binary Search)
5. Linked Lists + Stack + Queue
6. Trees + BST + Heaps
7. Graphs (BFS/DFS)
8. Dynamic Programming
9. Greedy Algorithms
10. Bit Manipulation
11. Advanced Topics

### Weekly Plan (Beginner)

| Week | Focus |
|------|-------|
| 1-2 | Python basics + Complexity |
| 3-4 | Arrays, Strings, Hashing |
| 5-6 | Recursion, Backtracking |
| 7-8 | Sorting, Searching |
| 9-10 | Linked Lists, Stack, Queue |
| 11-12 | Trees, BST |
| 13-14 | Heaps, Graphs |
| 15-16 | DP (very important — spend extra time) |
| 17-18 | Greedy, Bit Manipulation |
| 19-20 | Advanced Topics + Mock Interviews |

### Daily Routine

```
Day 1: Learn concept + theory
Day 2: Solve 2-3 easy problems on topic
Day 3: Solve 2-3 medium problems
Day 4: Solve 1-2 hard problems + review
Day 5: Mixed review of all previous topics
Day 6-7: Mock interview + code review
```

### Platforms to Practice

| Platform | Best For |
|----------|---------|
| LeetCode | Interview prep (most popular) |
| Codeforces | Competitive programming |
| HackerRank | Beginners |
| GeeksforGeeks | Theory + practice |
| AtCoder | Clean problems |

### Best Practices for Coding

1. Always analyze time and space complexity before coding
2. Write brute force first, then optimize
3. Use meaningful variable names
4. Test with edge cases: empty input, size 1, max size
5. Know Python built-ins: `sorted()`, `heapq`, `Counter`, `deque`
6. Practice until you can solve easy problems in under 10 minutes
7. Review others' solutions after solving — learn better approaches
8. Don't memorize — understand the pattern

---

*📌 End of Complete DSA Notes — Python Edition*
*Created for beginners learning Data Structures & Algorithms step by step.*
