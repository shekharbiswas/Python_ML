# Week 1, Day 4 — Files, Modules & Basic OOP

**Estimated time:** 6 hours (concepts + hands-on practice, paired with `tasks.md`)

---

## Why This Day Matters

Today closes the gap between "writing a script" and "writing a small program." You'll learn to read/write real files (the same skill you'll use to handle CSV exports from Snowflake), organize code across multiple files instead of one giant script, and use classes to model real-world things — which is exactly how libraries like `pandas` and `scikit-learn` are built under the hood. Tomorrow, all of this comes together when you connect to Snowflake.

---

## 1. Working with Files

### Reading a text file
```python
with open("data.txt", "r") as f:
    content = f.read()          # reads the whole file as one string
    print(content)
```

```python
with open("data.txt", "r") as f:
    for line in f:                # reads line by line — memory-efficient for big files
        print(line.strip())        # .strip() removes the trailing newline character
```

**Always use `with open(...) as f:`.** This is called a *context manager* — it automatically closes the file when you're done, even if an error happens partway through. Manually calling `f.close()` is easy to forget; `with` makes that mistake impossible.

### Writing a text file
```python
with open("output.txt", "w") as f:
    f.write("Hello, file!\n")

with open("output.txt", "a") as f:   # "a" = append, adds to the end instead of overwriting
    f.write("Another line\n")
```

### File modes
| Mode | Meaning |
|---|---|
| `"r"` | Read (default) — file must exist |
| `"w"` | Write — creates the file if missing, **overwrites** if it exists |
| `"a"` | Append — adds to the end, creates the file if missing |
| `"r+"` | Read and write |

**Common trap:** opening a file with `"w"` when you meant `"a"` will silently erase existing content. Double-check the mode when writing to a file that matters.

---

## 2. Reading and Writing CSV Files

CSV (comma-separated values) is the most common flat-file format you'll deal with — including data exported from Snowflake.

```python
import csv

# Reading a CSV
with open("students.csv", "r") as f:
    reader = csv.DictReader(f)     # reads each row as a dictionary, using the header row as keys
    for row in reader:
        print(row["name"], row["grade"])
```

```python
# Writing a CSV
with open("output.csv", "w", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "grade"])
    writer.writeheader()
    writer.writerow({"name": "Alice", "grade": 92})
```

Notice `csv.DictReader` gives you back exactly the shape you practiced yesterday — a row becomes a dictionary, and the whole file becomes something you can loop through like a list of dictionaries. This is not a coincidence; it's the same pattern you'll see with Snowflake query results and, later, with pandas DataFrames.

---

## 3. Organizing Code with Modules

A **module** is just a `.py` file that contains code you want to reuse elsewhere. As scripts grow, splitting related functions into their own files keeps things organized and reusable.

**`helpers.py`:**
```python
def celsius_to_fahrenheit(c):
    return c * 9/5 + 32

def is_even(n):
    return n % 2 == 0
```

**`main.py`, in the same folder:**
```python
import helpers

print(helpers.celsius_to_fahrenheit(20))
print(helpers.is_even(7))
```

### Alternative import styles
```python
from helpers import celsius_to_fahrenheit   # import a specific function directly
print(celsius_to_fahrenheit(20))              # no need to prefix with "helpers."

import helpers as h                           # give the module a shorter alias
print(h.is_even(4))
```

You've actually already been doing this — `import csv` at the top of the file above is importing Python's built-in `csv` module. Starting next week, you'll do the exact same thing with `import pandas as pd` and `import snowflake.connector`.

### The `if __name__ == "__main__":` pattern
```python
def main():
    print("Running the main program")

if __name__ == "__main__":
    main()
```
This lets a file be run directly (as a script) OR imported as a module elsewhere without automatically running its top-level code. You'll see this pattern constantly in real-world Python code and pipeline scripts.

---

## 4. Basic Object-Oriented Programming (OOP)

A **class** is a blueprint for creating objects that bundle together data (attributes) and behavior (methods).

```python
class Student:
    def __init__(self, name, grades):
        self.name = name          # attribute
        self.grades = grades       # attribute

    def average_grade(self):       # method
        return sum(self.grades) / len(self.grades)

    def letter_grade(self):
        avg = self.average_grade()
        if avg >= 90:
            return "A"
        elif avg >= 80:
            return "B"
        else:
            return "C"
```

```python
alice = Student("Alice", [88, 92, 79])
print(alice.name)              # "Alice"
print(alice.average_grade())    # 86.33...
print(alice.letter_grade())     # "B"

bob = Student("Bob", [65, 70, 60])
print(bob.letter_grade())        # "C"
```

### Key vocabulary
- **Class** — the blueprint (`Student`)
- **Object / Instance** — a specific thing built from that blueprint (`alice`, `bob`)
- **`__init__`** — the special method that runs automatically when a new object is created, used to set up its initial attributes
- **`self`** — refers to "this specific object" — every method needs it as the first parameter so it knows which object's data to work with
- **Attribute** — data stored on an object (`self.name`)
- **Method** — a function defined inside a class, called on an object (`alice.average_grade()`)

### Why use a class instead of just a dictionary?
Yesterday's Grade Book task used a list of dictionaries — that works fine for simple cases. A class becomes more useful once you want to **bundle behavior with the data** (like `average_grade()` living directly on the student) rather than writing separate standalone functions that take a dictionary as an argument every time. Neither approach is "wrong" — it's a design choice, and you'll develop intuition for when each fits better as you write more code.

---

## Key Takeaways for Today

1. Always use `with open(...) as f:` — it handles closing the file automatically.
2. Be careful with file mode `"w"` — it overwrites existing content.
3. `csv.DictReader`/`DictWriter` map CSV rows to/from Python dictionaries — the same shape you learned yesterday.
4. Modules let you split code across files and reuse it with `import` — exactly what you'll do with `pandas` and `snowflake.connector` starting next week.
5. A class bundles data (attributes) and behavior (methods) together into reusable objects — `self` always refers to the specific object a method is being called on.

Move to `tasks.md` now to put all of this into practice.
