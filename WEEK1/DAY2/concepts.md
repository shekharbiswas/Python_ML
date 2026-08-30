# Week 1, Day 2 — Control Flow & Functions

**Estimated time:** 6 hours (concepts + hands-on practice, paired with `tasks.md`)

---

## Why This Day Matters

Yesterday you learned to store and manipulate individual values. Today you learn to make decisions and repeat work — the two things that turn a script from "a list of steps" into a real program. Functions then let you package logic into reusable, testable pieces. Every data pipeline you build for the rest of this course — cleaning a Snowflake table, looping through rows, retrying a failed query — depends on what you learn today.

---

## 1. Conditional Logic: if / elif / else

Python decides what to do based on whether a condition is `True` or `False`.

```python
age = 17

if age >= 18:
    print("You can vote.")
elif age >= 13:
    print("You're a teenager.")
else:
    print("You're a kid.")
```

**Critical detail: indentation is not a style choice — it's syntax.** Python uses indentation (4 spaces is the standard convention) to define code blocks, unlike languages that use `{}`. Getting indentation wrong will either break your code or silently change its logic.

### Combining conditions
```python
age = 25
has_id = True

if age >= 18 and has_id:
    print("Entry allowed")

if age < 18 or not has_id:
    print("Entry denied")
```

### Truthy and falsy values
Python treats certain values as automatically "falsy" in an `if` check, even if they're not literally `False`:
```python
if "":        # empty string → falsy
if 0:         # zero → falsy
if []:        # empty list → falsy
if None:      # None → falsy
if "hello":   # non-empty string → truthy
if 5:         # non-zero number → truthy
```
This comes up constantly in real code: `if my_list:` is a common, idiomatic way to check "does this list have anything in it?" instead of `if len(my_list) > 0:`.

---

## 2. Loops: for and while

### `for` loops — when you know what you're iterating over
```python
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)

for i in range(5):        # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 10, 2):  # start, stop, step → 2, 4, 6, 8
    print(i)
```

### `while` loops — when you're repeating until a condition changes
```python
count = 0
while count < 5:
    print(count)
    count += 1   # without this, infinite loop!
```

**Common beginner bug: the infinite loop.** If the condition in a `while` loop never becomes `False`, your program hangs forever. Always double-check that something inside the loop moves you toward the exit condition.

### Loop control: break and continue
```python
for num in range(10):
    if num == 5:
        break          # exits the loop entirely
    if num % 2 == 0:
        continue        # skips to the next iteration
    print(num)
```

### Looping with index and value together
```python
fruits = ["apple", "banana", "cherry"]
for index, fruit in enumerate(fruits):
    print(index, fruit)
```
`enumerate()` is used constantly in real code whenever you need both position and value.

---

## 3. Functions

A function is a named, reusable block of code. Functions are how you avoid repeating yourself and how you organize a script into understandable pieces.

```python
def greet(name):
    return f"Hello, {name}!"

message = greet("Alice")
print(message)   # Hello, Alice!
```

### Anatomy of a function
- `def` starts the definition
- `greet` is the function name
- `(name)` are the parameters — the inputs the function expects
- `return` sends a value back to whoever called the function (without `return`, a function returns `None` by default)

### Default arguments
```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

greet("Bob")                  # "Hello, Bob!"
greet("Bob", "Hi")             # "Hi, Bob!"
greet("Bob", greeting="Hey")   # "Hey, Bob!" — using the parameter name explicitly
```

### Multiple return values
```python
def get_min_max(numbers):
    return min(numbers), max(numbers)

low, high = get_min_max([4, 1, 9, 2])
```
Python lets a function return multiple values, which get "unpacked" into separate variables on the receiving end.

### Variable scope
```python
x = 10

def my_function():
    x = 20   # this is a LOCAL variable, separate from the outer x
    print(x)  # 20

my_function()
print(x)       # still 10 — the outer x was untouched
```
Variables created inside a function stay inside that function unless explicitly returned. This trips up a lot of beginners who expect the outer variable to change.

---

## 4. Error Handling: try / except

Real-world code deals with things going wrong — a file that doesn't exist, a value that can't be converted, a network call that times out. `try`/`except` lets you handle these gracefully instead of crashing.

```python
try:
    value = int(input("Enter a number: "))
    print(100 / value)
except ValueError:
    print("That wasn't a valid number.")
except ZeroDivisionError:
    print("Can't divide by zero.")
finally:
    print("Done trying.")   # always runs, whether there was an error or not
```

- `try` — the code you're attempting to run
- `except <ErrorType>` — what to do if that specific error occurs (you can have multiple `except` blocks for different error types)
- `finally` — optional, always runs regardless of whether an error happened (often used for cleanup, like closing a file or a database connection)

**Why this matters for later weeks:** when you're pulling data from Snowflake in Python, network issues or bad credentials can throw errors. Wrapping that logic in `try`/`except` is standard practice, not an edge case.

---

## Key Takeaways for Today

1. Indentation defines code blocks in Python — it's not optional formatting.
2. Use `for` when you know what you're looping over; `while` when you're looping until a condition changes.
3. Functions should do one clear thing and `return` a value when there's a result to hand back.
4. Variables inside a function are local by default — they don't leak out.
5. `try`/`except` is how you handle things going wrong without crashing the whole script — essential once you start talking to Snowflake.

Move to `tasks.md` now to put all of this into practice.
