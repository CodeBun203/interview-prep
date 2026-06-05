# Python 3: Complete Guide
## From Basic to Advanced

---

## Table of Contents
1. How Python Works
2. Basic Syntax
3. Data Types
4. Variables and Naming
5. Operators
6. Control Flow
7. Functions
8. Collections: List, Tuple, Dict, Set
9. String Methods
10. Comprehensions
11. Classes and Object-Oriented Programming
12. Inheritance and Polymorphism
13. Special (Dunder) Methods
14. Exception Handling
15. File I/O
16. Modules and Imports
17. Generators and Iterators
18. Decorators
19. Context Managers
20. Type Hints
21. Async and Await
22. Common Standard Library Modules
23. Advanced Collections
24. Common Interview Patterns in Python

---

## 1. How Python Works

Python is an **interpreted, dynamically typed, garbage-collected** language.

- **Interpreted:** Python code is not compiled directly to machine code. The CPython interpreter reads your `.py` file, compiles it to bytecode (`.pyc`), and executes that bytecode on the Python Virtual Machine.
- **Dynamically typed:** Variables do not have fixed types. A variable can hold an integer, then later hold a string. Types are checked at runtime, not at compile time.
- **Garbage collected:** Python automatically manages memory. When an object has no more references pointing to it, Python's garbage collector reclaims its memory.

```python
x = 10       # x is an int
x = "hello"  # now x is a str — this is valid in Python
```

---

## 2. Basic Syntax

### Indentation
Python uses indentation (whitespace) to define code blocks. There are no curly braces. The convention is 4 spaces per level.

```python
if True:
    print("inside the block")   # 4 spaces
    print("still inside")       # 4 spaces
print("outside the block")      # no indentation
```

### Comments
```python
# Single line comment

"""
Multi-line string often used as a docstring.
Technically not a comment but treated as one
when placed at the top of a function or class.
"""
```

### Printing
```python
print("Hello, World!")
print("Value:", 42)
print(f"Formatted: {42 * 2}")   # f-string (Python 3.6+)
```

### Input
```python
name = input("Enter your name: ")  # always returns a string
age = int(input("Enter your age: "))
```

---

## 3. Data Types

Python has several built-in data types. Every value in Python is an object.

### Integers (int)
Whole numbers with no size limit in Python 3.

```python
x = 42
y = -7
z = 1_000_000   # underscores for readability
big = 2 ** 100  # Python handles arbitrarily large integers
```

### Floats (float)
Decimal numbers. Stored as 64-bit IEEE 754 floating point.

```python
x = 3.14
y = -0.001
z = 1.5e10      # scientific notation: 1.5 x 10^10

# Floating point is not exact
0.1 + 0.2       # returns 0.30000000000000004, not 0.3
```

For precise decimal math, use the `decimal` module:
```python
from decimal import Decimal
Decimal("0.1") + Decimal("0.2")  # returns Decimal('0.3')
```

### Strings (str)
Immutable sequences of Unicode characters.

```python
s1 = "hello"
s2 = 'world'
s3 = """multi
line"""

# f-strings (fastest and most readable way to format)
name = "Hayden"
greeting = f"Hello, {name}!"
result = f"2 + 2 = {2 + 2}"

# Common operations
len("hello")         # 5
"hello"[0]          # 'h'
"hello"[-1]         # 'o' (negative indexing from the end)
"hello"[1:3]        # 'el' (slicing)
"hello" + " world"  # 'hello world' (concatenation)
"ha" * 3            # 'hahaha' (repetition)
"el" in "hello"     # True (membership test)
```

### Booleans (bool)
True or False. Booleans are a subclass of int in Python (True == 1, False == 0).

```python
x = True
y = False
print(True + True)   # 2 (because True is 1)
```

**Truthy and Falsy values:**
Everything in Python is truthy or falsy. Falsy values:
- `False`
- `0`, `0.0`
- `None`
- Empty sequences: `""`, `[]`, `()`, `{}`
- Empty dict: `{}`

Everything else is truthy.

```python
if []:
    print("never runs")    # empty list is falsy

if [0]:
    print("this runs")     # list with one element is truthy, even if that element is 0
```

### NoneType (None)
Represents the absence of a value. Similar to null in other languages.

```python
x = None
print(x is None)   # True — use 'is' not '==' to check for None
```

### Type Checking and Conversion

```python
type(42)          # <class 'int'>
type("hello")     # <class 'str'>
isinstance(42, int)       # True
isinstance(42, (int, float))  # True — checks against multiple types

int("42")         # 42
float("3.14")     # 3.14
str(42)           # "42"
bool(0)           # False
bool(1)           # True
```

---

## 4. Variables and Naming

### Assignment
```python
x = 10
x, y = 10, 20          # multiple assignment
a = b = c = 0          # chain assignment
first, *rest = [1, 2, 3, 4]   # unpacking: first=1, rest=[2,3,4]
```

### Naming Conventions
| Style | Use case | Example |
|---|---|---|
| `snake_case` | Variables and functions | `user_name`, `get_data()` |
| `PascalCase` | Classes | `UserProfile`, `DatabaseConnection` |
| `UPPER_SNAKE_CASE` | Constants | `MAX_RETRIES`, `API_URL` |
| `_single_leading` | "Private" by convention | `_internal_value` |
| `__double_leading` | Name mangling in classes | `__private_attr` |

---

## 5. Operators

### Arithmetic
```python
5 + 2    # 7   addition
5 - 2    # 3   subtraction
5 * 2    # 10  multiplication
5 / 2    # 2.5 true division (always returns float)
5 // 2   # 2   floor division (rounds down)
5 % 2    # 1   modulo (remainder)
5 ** 2   # 25  exponentiation
```

### Comparison
```python
5 == 5   # True   equal
5 != 4   # True   not equal
5 > 4    # True   greater than
5 >= 5   # True   greater than or equal
5 < 6    # True   less than
5 <= 5   # True   less than or equal
```

### Logical
```python
True and False   # False
True or False    # True
not True         # False

# Short-circuit evaluation
x = None
y = x or "default"   # y = "default" because x is falsy
z = x and x.value    # z = None — never evaluates x.value because x is falsy
```

### Identity and Membership
```python
x is None       # True if x is the None object (use for None checks)
x is not None   # True if x is not None
"a" in "abc"    # True
3 in [1, 2, 3]  # True
"z" not in "abc"  # True
```

### Augmented Assignment
```python
x = 10
x += 5    # x = 15
x -= 3    # x = 12
x *= 2    # x = 24
x //= 4   # x = 6
x **= 2   # x = 36
```

---

## 6. Control Flow

### if / elif / else
```python
score = 85

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"
```

### Ternary (conditional expression)
```python
status = "adult" if age >= 18 else "minor"
```

### for loops
Python's for loop is always a for-each over an iterable.

```python
# Iterate over a list
for item in [1, 2, 3]:
    print(item)

# Iterate with index
for i, item in enumerate(["a", "b", "c"]):
    print(i, item)   # 0 a, 1 b, 2 c

# Iterate over range
for i in range(5):       # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 8, 2): # 2, 4, 6
    print(i)

# Iterate over dict
d = {"a": 1, "b": 2}
for key in d:               # iterates keys
    print(key)
for key, value in d.items():  # iterates key-value pairs
    print(key, value)
```

### while loops
```python
count = 0
while count < 5:
    print(count)
    count += 1
```

### break and continue
```python
for i in range(10):
    if i == 5:
        break       # exit the loop entirely
    if i % 2 == 0:
        continue    # skip to the next iteration
    print(i)        # prints 1, 3
```

### pass
A no-op placeholder. Used when syntax requires a block but you have nothing to put there yet.

```python
def not_implemented_yet():
    pass

class EmptyClass:
    pass
```

---

## 7. Functions

### Defining and Calling
```python
def greet(name):
    return f"Hello, {name}!"

result = greet("Hayden")
```

### Default Parameters
```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

greet("Hayden")              # "Hello, Hayden!"
greet("Hayden", "Hi")        # "Hi, Hayden!"
```

Default values are evaluated once at function definition, not each call. Never use a mutable default:
```python
# WRONG: the list is shared across all calls
def append_item(item, lst=[]):
    lst.append(item)
    return lst

# RIGHT: use None and create inside the function
def append_item(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

### *args and **kwargs
`*args` collects extra positional arguments into a tuple.
`**kwargs` collects extra keyword arguments into a dict.

```python
def add(*args):
    return sum(args)

add(1, 2, 3)       # 6
add(1, 2, 3, 4, 5) # 15

def describe(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

describe(name="Hayden", role="SWE", city="Seattle")
```

### Keyword-Only Arguments
Arguments after `*` must be passed by keyword, not position.

```python
def connect(host, *, port=80, timeout=30):
    pass

connect("localhost", port=8080)       # works
connect("localhost", 8080)            # TypeError
```

### Returning Multiple Values
Python functions can return multiple values as a tuple.

```python
def min_max(lst):
    return min(lst), max(lst)

lo, hi = min_max([3, 1, 4, 1, 5])   # lo=1, hi=5
```

### Lambda Functions
Anonymous single-expression functions.

```python
square = lambda x: x ** 2
square(5)    # 25

# Common use: sort key
people = [("Alice", 30), ("Bob", 25), ("Carol", 35)]
people.sort(key=lambda person: person[1])   # sort by age
```

### Scope: LEGB Rule
Python looks up names in this order:
1. **L**ocal: inside the current function
2. **E**nclosing: in enclosing function scopes (for nested functions)
3. **G**lobal: at the module level
4. **B**uilt-in: Python's built-in names (len, print, etc.)

```python
x = "global"

def outer():
    x = "enclosing"
    def inner():
        x = "local"
        print(x)    # "local"
    inner()
    print(x)        # "enclosing"

outer()
print(x)            # "global"
```

---

## 8. Collections

### List
Ordered, mutable, allows duplicates. The most versatile collection.

```python
lst = [1, 2, 3, 4, 5]

# Accessing
lst[0]       # 1
lst[-1]      # 5 (last element)
lst[1:3]     # [2, 3] (slice: start inclusive, end exclusive)
lst[::-1]    # [5, 4, 3, 2, 1] (reversed)

# Modifying
lst.append(6)           # add to end: [1,2,3,4,5,6]
lst.insert(0, 0)        # insert at index: [0,1,2,3,4,5,6]
lst.extend([7, 8])      # add multiple: [0,1,2,3,4,5,6,7,8]
lst.pop()               # remove and return last element
lst.pop(0)              # remove and return element at index 0
lst.remove(3)           # remove first occurrence of value 3
del lst[0]              # delete by index

# Searching
3 in lst                # True
lst.index(3)            # index of first occurrence
lst.count(3)            # number of occurrences

# Sorting
lst.sort()              # sort in-place (modifies lst)
sorted(lst)             # returns new sorted list (lst unchanged)
lst.sort(reverse=True)  # sort descending
lst.sort(key=lambda x: -x)  # sort by custom key

# Other
len(lst)                # length
lst.copy()              # shallow copy
lst.clear()             # remove all elements
lst.reverse()           # reverse in-place
```

**Time Complexities:**
| Operation | Average Case |
|---|---|
| Access by index | O(1) |
| Append | O(1) amortized |
| Insert at arbitrary index | O(n) |
| Search (in operator) | O(n) |
| Sort | O(n log n) |

---

### Tuple
Ordered, **immutable**, allows duplicates. Use when data should not change.

```python
t = (1, 2, 3)
t2 = 1, 2, 3      # parentheses optional
single = (1,)     # single-element tuple needs trailing comma

# Accessing (same as list)
t[0]         # 1
t[1:3]       # (2, 3)

# Cannot modify
t[0] = 99    # TypeError: tuple is immutable

# Unpacking
x, y, z = (1, 2, 3)
first, *rest = (1, 2, 3, 4)   # first=1, rest=[2,3,4]

# Uses
# - Function return values
# - Dictionary keys (lists cannot be dict keys, tuples can)
# - Data that should not change (coordinates, RGB values, etc.)
```

**Why tuples over lists:**
- Tuples are slightly faster for iteration
- Tuples can be used as dict keys or set members (because they are hashable)
- Tuples signal to the reader that this data is fixed

---

### Dictionary (dict)
Unordered (insertion-ordered since Python 3.7) collection of key-value pairs. Keys must be hashable (immutable). O(1) average lookup.

```python
d = {"name": "Hayden", "role": "SWE", "city": "Seattle"}

# Accessing
d["name"]               # "Hayden"
d.get("name")           # "Hayden" (returns None if missing, not KeyError)
d.get("age", 0)         # 0 (default value if key missing)

# Modifying
d["age"] = 28           # add or update
d.update({"age": 29, "lang": "Python"})  # merge another dict

# Removing
del d["city"]           # remove key, raises KeyError if missing
d.pop("city")           # remove and return value, raises KeyError if missing
d.pop("city", None)     # remove and return, return None if missing
d.popitem()             # remove and return last inserted (key, value) pair

# Iterating
for key in d:                    # iterate keys
for key in d.keys():             # explicitly iterate keys
for value in d.values():         # iterate values
for key, value in d.items():     # iterate key-value pairs

# Checking
"name" in d             # True (checks keys)
"Hayden" in d.values()  # True (checks values)

# Other
len(d)                  # number of keys
d.copy()                # shallow copy
d.clear()               # remove all keys

# Merging dicts (Python 3.9+)
merged = d1 | d2        # new dict with d1 and d2 merged
d1 |= d2                # update d1 in-place with d2
```

**Common patterns:**
```python
# Counting occurrences
word_count = {}
for word in words:
    word_count[word] = word_count.get(word, 0) + 1

# Grouping
from collections import defaultdict
groups = defaultdict(list)
for item in items:
    groups[item.category].append(item)
```

---

### Set
Unordered collection of **unique** values. Elements must be hashable. O(1) average add, remove, and membership test.

```python
s = {1, 2, 3, 3, 2}    # {1, 2, 3} — duplicates removed
s = set([1, 2, 3])      # from list

# Modifying
s.add(4)                # add element
s.remove(3)             # remove, raises KeyError if missing
s.discard(3)            # remove, no error if missing
s.pop()                 # remove and return arbitrary element

# Set operations
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b                   # union: {1,2,3,4,5,6}
a & b                   # intersection: {3,4}
a - b                   # difference: {1,2} (in a but not b)
a ^ b                   # symmetric difference: {1,2,5,6} (in either but not both)

a.issubset(b)           # True if all of a is in b
a.issuperset(b)         # True if all of b is in a
a.isdisjoint(b)         # True if a and b share no elements

# Common use: deduplication
unique = list(set([1, 1, 2, 2, 3]))    # [1, 2, 3]

# Common use: fast membership testing
valid_ids = {1001, 1002, 1003}
if user_id in valid_ids:               # O(1), much faster than a list
    pass
```

---

## 9. String Methods

Strings are immutable. All methods return new strings.

```python
s = "  Hello, World!  "

# Case
s.lower()               # "  hello, world!  "
s.upper()               # "  HELLO, WORLD!  "
s.title()               # "  Hello, World!  "
s.capitalize()          # first char upper, rest lower
s.swapcase()            # swap upper and lower

# Stripping whitespace
s.strip()               # "Hello, World!"
s.lstrip()              # "Hello, World!  "
s.rstrip()              # "  Hello, World!"

# Searching
s.find("World")         # index of first occurrence, -1 if not found
s.index("World")        # same but raises ValueError if not found
s.count("l")            # number of occurrences
s.startswith("  Hello") # True
s.endswith("!  ")       # True

# Splitting and joining
"a,b,c".split(",")      # ["a", "b", "c"]
"hello world".split()   # ["hello", "world"] (splits on whitespace)
",".join(["a", "b", "c"])  # "a,b,c"

# Replacing
"hello".replace("l", "r")   # "herro"

# Checking
"hello123".isalnum()    # True (letters and digits only)
"hello".isalpha()       # True (letters only)
"123".isdigit()         # True (digits only)
"  ".isspace()          # True

# Padding
"hi".ljust(10)          # "hi        "
"hi".rjust(10)          # "        hi"
"hi".center(10)         # "    hi    "
"42".zfill(5)           # "00042"

# Formatting
"{} {}".format("hello", "world")     # "hello world"
"{name}".format(name="Hayden")       # "Hayden"
f"{'hello':>10}"                     # "     hello" (right-align in f-string)
```

---

## 10. Comprehensions

Comprehensions are concise, readable ways to build collections.

### List Comprehension
```python
# [expression for item in iterable if condition]

squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
words = [word.upper() for word in ["hello", "world"]]

# Nested
matrix = [[1,2,3],[4,5,6],[7,8,9]]
flat = [x for row in matrix for x in row]  # [1,2,3,4,5,6,7,8,9]
```

### Dict Comprehension
```python
# {key_expr: value_expr for item in iterable if condition}

squares = {x: x**2 for x in range(5)}
# {0:0, 1:1, 2:4, 3:9, 4:16}

inverted = {v: k for k, v in original.items()}  # swap keys and values
```

### Set Comprehension
```python
unique_squares = {x**2 for x in [-2, -1, 0, 1, 2]}
# {0, 1, 4}
```

### Generator Expression
Like a list comprehension but lazy: values are produced one at a time instead of all at once. Uses parentheses instead of brackets. Memory-efficient for large sequences.

```python
# Wrap in () to create a generator
gen = (x**2 for x in range(1000000))   # uses almost no memory
next(gen)    # 0
next(gen)    # 1

# Can pass directly to functions
total = sum(x**2 for x in range(100))
largest = max(len(word) for word in words)
```

---

## 11. Classes and Object-Oriented Programming

A class is a blueprint for creating objects. Objects bundle data (attributes) and behavior (methods) together.

```python
class BankAccount:
    # Class variable: shared across all instances
    interest_rate = 0.02

    # __init__ is the constructor: runs when an object is created
    def __init__(self, owner, balance=0):
        # Instance variables: unique to each object
        self.owner = owner
        self.balance = balance

    # Instance method: first param is always 'self'
    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be positive")
        self.balance += amount

    def withdraw(self, amount):
        if amount > self.balance:
            raise ValueError("Insufficient funds")
        self.balance -= amount

    def apply_interest(self):
        self.balance += self.balance * BankAccount.interest_rate

    # __repr__: unambiguous string representation (for debugging)
    def __repr__(self):
        return f"BankAccount(owner={self.owner!r}, balance={self.balance})"

    # __str__: readable string representation (for print())
    def __str__(self):
        return f"{self.owner}'s account: ${self.balance:.2f}"


# Creating instances
account1 = BankAccount("Hayden", 1000)
account2 = BankAccount("Alice")

account1.deposit(500)
account1.withdraw(200)
print(account1)             # "Hayden's account: $1300.00"
print(repr(account1))       # "BankAccount(owner='Hayden', balance=1300)"
```

### Class Methods and Static Methods

```python
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def from_string(cls, date_string):
        # cls refers to the class itself, not an instance
        # Used as alternative constructors
        year, month, day = map(int, date_string.split("-"))
        return cls(year, month, day)

    @staticmethod
    def is_valid_year(year):
        # No access to class or instance
        # Used for utility functions related to the class
        return 1900 <= year <= 2100

d = Date.from_string("2025-06-01")
Date.is_valid_year(2025)   # True
```

### Properties
Control attribute access with getter, setter, and deleter logic.

```python
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius   # underscore = "private by convention"

    @property
    def celsius(self):
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Temperature below absolute zero")
        self._celsius = value

    @property
    def fahrenheit(self):
        return self._celsius * 9/5 + 32


t = Temperature(100)
t.celsius       # 100
t.fahrenheit    # 212.0
t.celsius = 200 # calls the setter
t.celsius = -300  # raises ValueError
```

---

## 12. Inheritance and Polymorphism

### Inheritance
A child class inherits attributes and methods from a parent class.

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        raise NotImplementedError("Subclasses must implement speak()")

    def __str__(self):
        return f"{self.__class__.__name__}(name={self.name})"


class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

    def fetch(self):
        return f"{self.name} fetches the ball!"


class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"


dog = Dog("Rex")
cat = Cat("Whiskers")

dog.speak()     # "Rex says Woof!"
cat.speak()     # "Whiskers says Meow!"
```

### super()
Call the parent class's methods from the child class.

```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

class Manager(Employee):
    def __init__(self, name, salary, department):
        super().__init__(name, salary)   # call Employee.__init__
        self.department = department
```

### Polymorphism
The same interface works on different types.

```python
animals = [Dog("Rex"), Cat("Whiskers"), Dog("Buddy")]
for animal in animals:
    print(animal.speak())   # each calls its own speak() implementation
```

### Abstract Base Classes
Force subclasses to implement specific methods.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        import math
        return math.pi * self.radius ** 2

    def perimeter(self):
        import math
        return 2 * math.pi * self.radius

# Shape()       # TypeError: cannot instantiate abstract class
Circle(5).area()  # works
```

---

## 13. Special (Dunder) Methods

Dunder (double underscore) methods let your classes work with Python's built-in operations.

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    # String representations
    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

    def __str__(self):
        return f"({self.x}, {self.y})"

    # Arithmetic operators
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other):
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)

    # Comparison
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

    def __lt__(self, other):
        return abs(self) < abs(other)

    # Length / magnitude
    def __abs__(self):
        return (self.x**2 + self.y**2) ** 0.5

    def __len__(self):
        return 2   # a 2D vector has 2 components

    # Make it iterable
    def __iter__(self):
        yield self.x
        yield self.y

    # Container behavior
    def __getitem__(self, index):
        return (self.x, self.y)[index]

    # Boolean value
    def __bool__(self):
        return self.x != 0 or self.y != 0


v1 = Vector(1, 2)
v2 = Vector(3, 4)
v1 + v2             # Vector(4, 6)
v1 == Vector(1, 2)  # True
abs(v1)             # 2.23...
list(v1)            # [1, 2]
```

### Common Dunder Methods Reference
| Method | Called by |
|---|---|
| `__init__` | `ClassName()` constructor |
| `__repr__` | `repr(obj)`, REPL display |
| `__str__` | `str(obj)`, `print(obj)` |
| `__len__` | `len(obj)` |
| `__getitem__` | `obj[key]` |
| `__setitem__` | `obj[key] = value` |
| `__contains__` | `x in obj` |
| `__iter__` | `for x in obj` |
| `__next__` | `next(obj)` |
| `__add__` | `obj + other` |
| `__eq__` | `obj == other` |
| `__lt__` | `obj < other` |
| `__hash__` | `hash(obj)`, used as dict key |
| `__bool__` | `bool(obj)`, truthiness |
| `__call__` | `obj()` (makes instance callable) |
| `__enter__` | `with obj:` (context manager) |
| `__exit__` | exit of `with` block |

---

## 14. Exception Handling

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
except (TypeError, ValueError) as e:
    print(f"Type or value error: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
    raise   # re-raise the exception
else:
    # runs only if no exception was raised
    print(f"Result: {result}")
finally:
    # always runs, even if there was an exception
    print("Cleanup code here")
```

### Raising Exceptions
```python
def divide(a, b):
    if b == 0:
        raise ValueError("Denominator cannot be zero")
    return a / b
```

### Custom Exceptions
```python
class InsufficientFundsError(Exception):
    def __init__(self, amount, balance):
        self.amount = amount
        self.balance = balance
        super().__init__(f"Cannot withdraw {amount}, balance is {balance}")


try:
    raise InsufficientFundsError(500, 100)
except InsufficientFundsError as e:
    print(e.amount)     # 500
    print(e.balance)    # 100
```

### Common Built-in Exceptions
| Exception | When it occurs |
|---|---|
| `ValueError` | Right type, wrong value (int("abc")) |
| `TypeError` | Wrong type for operation |
| `KeyError` | Missing dict key |
| `IndexError` | List index out of range |
| `AttributeError` | Object has no such attribute |
| `FileNotFoundError` | File does not exist |
| `ZeroDivisionError` | Division by zero |
| `StopIteration` | Iterator is exhausted |
| `RuntimeError` | General runtime error |

---

## 15. File I/O

```python
# Writing a file
with open("output.txt", "w") as f:
    f.write("Hello, World!\n")
    f.write("Second line\n")

# Reading entire file
with open("output.txt", "r") as f:
    content = f.read()

# Reading line by line (memory efficient for large files)
with open("output.txt", "r") as f:
    for line in f:
        print(line.strip())

# Reading all lines into a list
with open("output.txt", "r") as f:
    lines = f.readlines()

# Appending to existing file
with open("output.txt", "a") as f:
    f.write("Appended line\n")

# Working with JSON
import json

data = {"name": "Hayden", "skills": ["Python", "Java"]}

with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

with open("data.json", "r") as f:
    loaded = json.load(f)
```

File modes: `"r"` read, `"w"` write (overwrites), `"a"` append, `"rb"` read binary, `"wb"` write binary.

The `with` statement is a context manager. It automatically closes the file even if an exception is raised.

---

## 16. Modules and Imports

```python
# Import a module
import math
math.sqrt(16)      # 4.0
math.pi            # 3.14159...

# Import specific names
from math import sqrt, pi
sqrt(16)           # 4.0

# Import with alias
import numpy as np
from collections import defaultdict as dd

# Import everything (avoid in production code)
from math import *
```

### Creating your own module
Any `.py` file is a module. Create `utils.py`:
```python
def add(a, b):
    return a + b
```
Then import it:
```python
from utils import add
```

### The `__name__` guard
Code inside this block only runs when the file is executed directly, not when it is imported.
```python
def main():
    print("Running as script")

if __name__ == "__main__":
    main()
```

---

## 17. Generators and Iterators

### Generators
A generator is a function that yields values one at a time instead of returning them all at once. It pauses execution at each `yield` and resumes on the next call to `next()`. Memory-efficient for large sequences.

```python
def count_up(n):
    i = 0
    while i < n:
        yield i
        i += 1

gen = count_up(3)
next(gen)    # 0
next(gen)    # 1
next(gen)    # 2
next(gen)    # StopIteration

# Use in a for loop
for num in count_up(5):
    print(num)
```

### Practical use: large data processing
```python
def read_large_file(path):
    with open(path) as f:
        for line in f:
            yield line.strip()

# Process a multi-GB file without loading it all into memory
for line in read_large_file("huge.log"):
    process(line)
```

### The Iterator Protocol
Any object implementing `__iter__` and `__next__` is an iterator.

```python
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

for n in Countdown(3):
    print(n)    # 3, 2, 1
```

---

## 18. Decorators

A decorator is a function that takes a function and returns a modified version of it. Used for cross-cutting concerns like logging, timing, authentication, and caching.

```python
def timer(func):
    import time
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    import time
    time.sleep(1)

slow_function()   # prints: slow_function took 1.0012s
```

The `@timer` syntax is equivalent to `slow_function = timer(slow_function)`.

### Preserving function metadata
```python
from functools import wraps

def my_decorator(func):
    @wraps(func)   # preserves __name__, __doc__, etc.
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

### Built-in decorators
```python
@property           # turns a method into a read-only attribute
@classmethod        # receives the class as first argument
@staticmethod       # no implicit first argument
@functools.lru_cache(maxsize=128)   # memoization cache
```

---

## 19. Context Managers

Context managers handle setup and teardown automatically using the `with` statement.

```python
# Built-in: file handling
with open("file.txt") as f:
    data = f.read()
# file is automatically closed here, even if an exception occurred

# Creating a context manager with a class
class DatabaseConnection:
    def __enter__(self):
        print("Opening connection")
        self.conn = connect_to_db()
        return self.conn

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Closing connection")
        self.conn.close()
        return False   # False means: don't suppress exceptions

with DatabaseConnection() as conn:
    conn.query("SELECT * FROM users")

# Creating a context manager with contextlib
from contextlib import contextmanager

@contextmanager
def timer():
    import time
    start = time.time()
    yield
    end = time.time()
    print(f"Elapsed: {end - start:.4f}s")

with timer():
    expensive_operation()
```

---

## 20. Type Hints

Type hints (Python 3.5+) add optional type annotations. Python does not enforce them at runtime, but IDEs, linters, and `mypy` use them for static analysis.

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    return a + b

def process(items: list[int]) -> dict[str, int]:
    return {"sum": sum(items), "count": len(items)}

# Optional: value can be the type or None
from typing import Optional

def find_user(user_id: int) -> Optional[str]:
    # returns a str or None
    ...

# Union: one of multiple types
from typing import Union

def parse(value: Union[int, str]) -> int:
    return int(value)

# Python 3.10+ shorthand for Union
def parse(value: int | str) -> int:
    return int(value)

# Callable, Any, TypeVar
from typing import Callable, Any, TypeVar

T = TypeVar("T")

def first(lst: list[T]) -> T:
    return lst[0]
```

---

## 21. Async and Await

Python's async/await syntax enables concurrent I/O without threads. While one coroutine waits for a network call, another coroutine can run. This is what makes FastAPI fast.

```python
import asyncio

async def fetch_data(url: str) -> str:
    # Simulating an async HTTP call
    await asyncio.sleep(1)    # yields control while "waiting"
    return f"Data from {url}"

async def main():
    # Run two tasks concurrently (total time ~1s, not ~2s)
    task1 = asyncio.create_task(fetch_data("https://api1.com"))
    task2 = asyncio.create_task(fetch_data("https://api2.com"))
    result1 = await task1
    result2 = await task2
    print(result1, result2)

asyncio.run(main())
```

### Key rules
- `async def` defines a coroutine function
- `await` can only be used inside an `async def` function
- `await` pauses the current coroutine and yields control to the event loop
- Use `asyncio.gather()` to run multiple coroutines concurrently and wait for all of them

```python
# Run multiple coroutines concurrently
results = await asyncio.gather(
    fetch_data("url1"),
    fetch_data("url2"),
    fetch_data("url3"),
)
```

---

## 22. Common Standard Library Modules

### collections
```python
from collections import Counter, defaultdict, deque, namedtuple, OrderedDict

# Counter: count occurrences
c = Counter("mississippi")
# Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})
c.most_common(2)    # [('i', 4), ('s', 4)]

# defaultdict: dict with a default value factory
dd = defaultdict(list)
dd["key"].append(1)   # no KeyError, creates empty list automatically

dd = defaultdict(int)
dd["count"] += 1      # starts at 0 automatically

# deque: double-ended queue, O(1) append and pop from both ends
dq = deque([1, 2, 3])
dq.appendleft(0)      # [0, 1, 2, 3]
dq.popleft()          # 0
dq.rotate(1)          # [3, 1, 2]

# namedtuple: tuple with named fields
Point = namedtuple("Point", ["x", "y"])
p = Point(1, 2)
p.x    # 1
p.y    # 2
```

### itertools
```python
import itertools

# chain: flatten multiple iterables
list(itertools.chain([1,2], [3,4], [5]))   # [1,2,3,4,5]

# combinations and permutations
list(itertools.combinations([1,2,3], 2))   # [(1,2),(1,3),(2,3)]
list(itertools.permutations([1,2,3], 2))   # [(1,2),(1,3),(2,1),(2,3),(3,1),(3,2)]

# groupby: group consecutive elements
from itertools import groupby
data = [1, 1, 2, 2, 2, 3]
for key, group in groupby(data):
    print(key, list(group))
# 1 [1, 1]
# 2 [2, 2, 2]
# 3 [3]

# product: cartesian product
list(itertools.product([1,2], ["a","b"]))
# [(1,'a'),(1,'b'),(2,'a'),(2,'b')]
```

### functools
```python
from functools import reduce, partial, lru_cache

# reduce: apply function cumulatively
reduce(lambda a, b: a + b, [1,2,3,4])   # 10

# partial: pre-fill function arguments
def power(base, exp):
    return base ** exp

square = partial(power, exp=2)
square(5)    # 25

# lru_cache: memoization
@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

fibonacci(100)   # fast due to caching
```

### heapq
```python
import heapq

# Min-heap operations
heap = [3, 1, 4, 1, 5, 9]
heapq.heapify(heap)             # transform list into a heap in-place
heapq.heappush(heap, 2)         # add element
heapq.heappop(heap)             # remove and return smallest element

# Useful patterns
# Kth largest element
heapq.nlargest(3, [3,1,4,1,5,9,2,6])    # [9,6,5]
heapq.nsmallest(3, [3,1,4,1,5,9,2,6])  # [1,1,2]
```

---

## 23. Advanced Collections

### Dataclasses (Python 3.7+)
Auto-generate `__init__`, `__repr__`, and `__eq__` for classes that primarily hold data.

```python
from dataclasses import dataclass, field

@dataclass
class User:
    name: str
    age: int
    tags: list = field(default_factory=list)   # mutable default
    active: bool = True

u = User("Hayden", 28)
print(u)        # User(name='Hayden', age=28, tags=[], active=True)
u.name          # "Hayden"

# Immutable dataclass
@dataclass(frozen=True)
class Point:
    x: float
    y: float

p = Point(1.0, 2.0)
p.x = 3.0   # FrozenInstanceError
```

### Enums
```python
from enum import Enum, auto

class Status(Enum):
    PENDING = auto()
    RUNNING = auto()
    COMPLETE = auto()
    FAILED = auto()

job_status = Status.RUNNING
job_status == Status.RUNNING    # True
job_status.name                 # "RUNNING"
job_status.value                # 2
```

---

## 24. Common Interview Patterns in Python

### Two Sum (Hash Map)
```python
def two_sum(nums, target):
    seen = {}
    for i, n in enumerate(nums):
        diff = target - n
        if diff in seen:
            return [seen[diff], i]
        seen[n] = i
```

### Sliding Window Maximum
```python
from collections import deque

def max_sliding_window(nums, k):
    dq = deque()
    result = []
    for i, n in enumerate(nums):
        while dq and nums[dq[-1]] < n:
            dq.pop()
        dq.append(i)
        if dq[0] == i - k:
            dq.popleft()
        if i >= k - 1:
            result.append(nums[dq[0]])
    return result
```

### BFS Shortest Path
```python
from collections import deque

def bfs_shortest_path(graph, start, end):
    queue = deque([(start, [start])])
    visited = {start}
    while queue:
        node, path = queue.popleft()
        if node == end:
            return path
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, path + [neighbor]))
    return None
```

### Binary Search
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

### Merge Sort
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
    return result + left[i:] + right[j:]
```

---

## Quick Reference: Time Complexities

| Structure | Access | Search | Insert | Delete |
|---|---|---|---|---|
| list (by index) | O(1) | O(n) | O(1) end / O(n) middle | O(n) |
| dict | O(1) avg | O(1) avg | O(1) avg | O(1) avg |
| set | N/A | O(1) avg | O(1) avg | O(1) avg |
| deque (ends) | O(1) | O(n) | O(1) | O(1) |
| heapq | O(1) min | O(n) | O(log n) | O(log n) |
| sorted list | O(1) | O(log n) | O(n) | O(n) |
