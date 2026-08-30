# Week 1, Day 1 — Python Basics & Environment Setup

**Estimated time:** 6 hours (concepts + hands-on setup, paired with `tasks.md`)

---

## Why This Day Matters

Every pipeline you'll build in this module — pulling data from Snowflake, transforming it, training a model — starts with a script running in a properly set up Python environment. Today is entirely foundational: get comfortable with Python's basic building blocks, and get your machine set up the *right* way, so every day after this one is smooth.

---

## 1. What Is Python, and Why Are We Using It?

Python is a general-purpose, interpreted programming language. "Interpreted" means you don't compile your code into a separate program first — you run it directly, line by line, through the Python interpreter. This makes it fast to write and test, which is why it's the dominant language in data work and machine learning.

You'll be running Python in two main ways this course:
- **Scripts** — a `.py` file you run from the terminal (`python my_script.py`)
- **Interactive shell / REPL** — typing `python` in a terminal drops you into a live prompt where you can test small bits of code line by line

Get comfortable with both. The REPL is great for quick experiments; scripts are how real, repeatable work gets done.

---

## 2. Variables and Data Types

A variable is just a name that points to a value in memory. Python doesn't require you to declare a type — it figures it out from the value you assign.

```python
name = "Alice"        # str (string)
age = 29               # int (integer)
height = 5.6            # float (decimal number)
is_active = True        # bool (True/False)
```

### Core built-in types you need cold:
| Type | Example | Notes |
|---|---|---|
| `int` | `42` | Whole numbers, no decimal |
| `float` | `3.14` | Decimal numbers |
| `str` | `"hello"` | Text, single or double quotes both work |
| `bool` | `True` / `False` | Capitalized — this trips up beginners from other languages |
| `NoneType` | `None` | Represents "no value" — similar to `null` in other languages |

### Checking a variable's type
```python
type(age)        # <class 'int'>
```

### Type conversion (casting)
You'll do this constantly when working with real data (e.g., a value read from a CSV comes in as a string even if it's a number):
```python
str(42)      # "42"
int("42")    # 42
float("3.14") # 3.14
```

**Common trap:** `int("3.14")` will throw an error — you can't convert a decimal-looking string directly to `int`. You'd need `int(float("3.14"))`.

---

## 3. Basic Operators

### Arithmetic
```python
5 + 3    # 8
5 - 3    # 2
5 * 3    # 15
5 / 3    # 1.666... (division always returns a float)
5 // 3   # 1  (floor division — drops the decimal)
5 % 3    # 2  (modulo — remainder after division)
5 ** 2   # 25 (exponent)
```

### Comparison (returns a bool)
```python
5 == 5     # True
5 != 3     # True
5 > 3      # True
5 <= 3     # False
```

### Logical operators
```python
True and False   # False
True or False    # True
not True         # False
```

**Exam-style trap to know now:** `=` is assignment (`x = 5`), `==` is comparison (`x == 5`). Mixing these up is one of the most common beginner bugs — and Python won't always warn you cleanly.

---

## 4. Strings — More Detail

Strings are used everywhere in data work (column names, file paths, query text). Know these operations well:

```python
name = "Alice"
greeting = "Hello, " + name          # concatenation → "Hello, Alice"
greeting = f"Hello, {name}"          # f-string (preferred, cleaner) → "Hello, Alice"

len(name)          # 5 — length of the string
name.upper()       # "ALICE"
name.lower()       # "alice"
name.strip()       # removes leading/trailing whitespace
name[0]             # "A" — indexing (0-based!)
name[0:3]           # "Ali" — slicing
```

**f-strings** (`f"..."`) are the modern, preferred way to build strings with variables inside them. You'll use these constantly, including later when building dynamic SQL queries to send to Snowflake.

---

## 5. Input and Output

```python
print("Hello, world!")              # basic output

user_input = input("Enter your name: ")   # basic input, always returns a string
print(f"Hi, {user_input}!")
```

`print()` is your main debugging tool for the next six weeks. When something's not working, printing intermediate values to see what's actually happening is the fastest way to find out why.

---

## 6. Comments

```python
# This is a single-line comment — Python ignores this line entirely

"""
This is a multi-line string,
often used as a comment block
or documentation at the top of a function/file.
"""
```

Good comments explain **why**, not **what** — the code already shows what it does. Get in the habit now; it matters a lot more once you're writing pipeline code others (or future-you) will read.

---

## 7. Virtual Environments — Why They Matter

A **virtual environment** is an isolated Python setup for a specific project, with its own installed packages, separate from your system's global Python. Without this, projects can silently break each other — e.g., Project A needs `pandas==1.5`, Project B needs `pandas==2.0`, and without isolation, installing one breaks the other.

### Creating and using a virtual environment

```bash
# Create a new virtual environment named "venv" in the current folder
python -m venv venv

# Activate it (macOS/Linux)
source venv/bin/activate

# Activate it (Windows)
venv\Scripts\activate

# Your terminal prompt will now show (venv) — that means it's active

# Deactivate when done
deactivate
```

**Rule for this whole course:** every project folder gets its own virtual environment. Always activate it before installing packages or running scripts.

---

## 8. Installing Packages with pip

`pip` is Python's package manager — it installs libraries other people have written (like `pandas`, `scikit-learn`, or `snowflake-connector-python`) so you don't have to write everything from scratch.

```bash
pip install pandas
pip install snowflake-connector-python

# See what's installed
pip list

# Save your project's dependencies to a file
pip freeze > requirements.txt

# Install everything from a requirements file (e.g., on a new machine)
pip install -r requirements.txt
```

`requirements.txt` is the standard way to make a Python project reproducible — anyone (including future you) can recreate the exact same environment with one command.

---

## Key Takeaways for Today

1. Python figures out variable types automatically — but you should always know what type you're working with.
2. `=` assigns, `==` compares. Never confuse them.
3. f-strings are the clean, modern way to build strings with variables.
4. Every project gets its own virtual environment — no exceptions.
5. `pip` + `requirements.txt` is how you install and track dependencies.

Move to `tasks.md` now to put all of this into practice.
