# Week 1, Day 1 — Tasks

**Total time:** ~6 hours. Work through these in order — each block builds on the last.

---

## Block 1 (45 min) — Environment Setup

1. Confirm Python is installed: run `python --version` (or `python3 --version`) in your terminal. You need Python 3.9+.
2. Create a project folder for the whole course, e.g. `py-ml-course/`.
3. Inside it, create a `week-1/day-1/` folder — this is where today's script files will live.
4. Create a virtual environment inside the `py-ml-course/` root folder:
   ```bash
   python -m venv venv
   ```
5. Activate it, and confirm activation worked (you should see `(venv)` in your prompt).
6. Install one package as a test: `pip install requests`. Run `pip list` and confirm it shows up.
7. Create a `requirements.txt` with `pip freeze > requirements.txt` and open it to see what's inside.

**Checkpoint:** You should have an activated virtual environment and a `requirements.txt` file with at least one package listed.

---

## Block 2 (45 min) — Variables & Types Practice

Create a file `week-1/day-1/variables_practice.py` and write code (not just run in the REPL) that:

1. Creates a variable for your name (`str`), your age (`int`), your height in meters (`float`), and whether you're currently a student (`bool`).
2. Prints each variable along with its type, e.g.:
   ```python
   print(name, type(name))
   ```
3. Takes a string like `"25"` and converts it to an `int`, then adds `5` to it and prints the result.
4. Deliberately writes a line that would try to convert `"3.14"` directly to `int` using `int("3.14")` — run it, read the error message carefully, then fix it using `int(float("3.14"))`.

**Checkpoint:** Script runs with no errors and correctly prints all four variables with their types.

---

## Block 3 (1 hour) — Operators & Strings

Create `week-1/day-1/operators_strings.py`:

1. Write a small "receipt calculator": define `price = 45.99` and `quantity = 3`, calculate the total, and print it using an f-string like:
   ```python
   print(f"Total: ${price * quantity:.2f}")
   ```
   (The `:.2f` formats to 2 decimal places — look up what this does if unfamiliar.)
2. Practice floor division and modulo: given `total_minutes = 130`, calculate and print how many full hours and leftover minutes that is (hint: use `//` and `%`).
3. Take a string variable with extra whitespace and mixed case, e.g. `"  Hello WORLD  "`, and print a cleaned-up version using `.strip()` and `.lower()`.
4. Practice slicing: given `word = "snowflake"`, print just `"snow"` and just `"flake"` using slicing (not by retyping the letters).

**Checkpoint:** All four exercises print correct, expected output — check your math and slicing by hand if unsure.

---

## Block 4 (1 hour) — Input/Output + Comments

Create `week-1/day-1/mini_interactive.py`:

1. Use `input()` to ask the user for their name and their favorite number.
2. Print a message using both values in an f-string.
3. Convert the "favorite number" input to an `int`, then print that number multiplied by 10.
4. Add comments above each major step explaining *why* you're doing it (not just what the line does).
5. Run the script from the terminal (not just in an editor) — this is how you'll run everything for the rest of the course.

**Checkpoint:** Script runs from the terminal, takes input, and produces correctly formatted output.

---

## Block 5 (1 hour) — pip & Package Management Practice

1. In your activated virtual environment, install two more packages: `pandas` and `python-dotenv`.
2. Run `pip list` again and note the new entries.
3. Update your `requirements.txt` (`pip freeze > requirements.txt`).
4. Deactivate your virtual environment (`deactivate`), then run `pip list` again — notice how different (much shorter) the global package list is. This is the point of isolation.
5. Reactivate the virtual environment before continuing.

**Checkpoint:** You can explain, in your own words, why we don't just install everything globally.

---

## Block 6 (1.5 hours) — Mini Project: "Profile Card Generator"

Build a single script, `week-1/day-1/profile_card.py`, that pulls together everything from today:

**Requirements:**
1. Ask the user (via `input()`) for: name, age, city, and favorite programming language (this may be their first time answering that one!).
2. Store each answer in a clearly named variable, with the correct type (age should be converted to `int`).
3. Print a nicely formatted "profile card" using f-strings, for example:
   ```
   ==============================
    PROFILE CARD
   ==============================
   Name: Alice
   Age: 29
   City: Bangalore
   Favorite Language: Python
   ==============================
   ```
4. Add a calculated line: how many years until this person turns 100 (use their age and basic arithmetic).
5. Add at least 3 comments explaining different sections of the script.
6. Make sure the script runs cleanly from the terminal with no errors, using your virtual environment.

**Stretch goal (optional, if time allows):** Wrap the "profile card" logic in a function called `print_profile_card(name, age, city, language)` so it can be reused, and call it with the collected input values.

---

## End of Day Checklist

- [ ] Virtual environment created, activated, and used for all scripts today
- [ ] `requirements.txt` exists and lists at least 3 packages
- [ ] Comfortable using `int()`, `float()`, `str()` conversions and know why `int("3.14")` fails directly
- [ ] Comfortable writing and reading f-strings
- [ ] Understand the difference between `=` and `==`
- [ ] Completed the Profile Card Generator mini-project, running cleanly from the terminal

If any checkbox is unchecked, spend extra time there before moving to Day 2 — tomorrow builds directly on today's basics with loops and functions.
