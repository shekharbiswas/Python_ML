# Week 1 — Python Foundations
### Module 4: Python + Machine Learning

---

## Overview

This week builds core Python fluency from the ground up — no prior Python experience assumed. By Friday, you'll write a script that connects to your existing Snowflake account and pulls a table into Python, tying this new skill directly to the SQL/dbt/Snowflake foundation you already have.

**Prerequisites:** None for Python. Snowflake account access and credentials from the earlier module.

**Tools used this week:** Python 3, a code editor (VS Code recommended), `pip`, `venv`, `snowflake-connector-python`.

---

## Structure

Each day has two files:
- **`concepts.md`** — explains the day's ideas with short, clear examples
- **`tasks.md`** — hands-on exercises to practice what you just learned, plus an end-of-day checklist

```
week-1/
├── README.md          (this file)
├── day-1/
│   ├── concepts.md
│   └── tasks.md
├── day-2/
│   ├── concepts.md
│   └── tasks.md
├── day-3/
│   ├── concepts.md
│   └── tasks.md
├── day-4/
│   ├── concepts.md
│   └── tasks.md
└── day-5/
    ├── concepts.md
    └── tasks.md
```

---

## Day-by-Day Breakdown

### Day 1 — Python Basics & Environment Setup
Variables, data types (int, float, str, bool), basic operators, input/output, setting up a virtual environment with `venv`, installing packages with `pip`. Ends with a working, isolated Python environment ready for the rest of the week.

### Day 2 — Control Flow & Functions
`if`/`elif`/`else`, `for` and `while` loops, writing and calling functions, arguments and return values, basic error handling with `try`/`except`.

### Day 3 — Core Data Structures
Lists, tuples, dictionaries, sets — when to use each, indexing and slicing, list comprehensions, nested data structures (e.g., a list of dictionaries, which is what most API/JSON data looks like).

### Day 4 — Files, Modules & Basic OOP
Reading and writing files (text/CSV), organizing code into modules/imports, introduction to classes and objects (attributes, methods, `__init__`), when OOP is useful vs. overkill for a script.

### Day 5 — Connecting Python to Snowflake
Installing and configuring `snowflake-connector-python`, authenticating to your Snowflake account, running a query from Python, and loading the result into a basic Python data structure. Closes the week by linking everything back to the data platform you already know.

---

## By End of Week 1, You Should Be Able To:

- [ ] Write a Python script using variables, loops, conditionals, and functions without referencing notes
- [ ] Choose the right data structure (list, dict, tuple, set) for a given problem
- [ ] Read from and write to a file in Python
- [ ] Explain what a class and object are, and write a simple one
- [ ] Connect to Snowflake from a Python script and run a query, printing the results

---

## Looking Ahead

Week 2 takes everything from this week and applies it to real data wrangling with `pandas` — the Snowflake connection you built on Day 5 becomes the starting point for every exercise going forward.
