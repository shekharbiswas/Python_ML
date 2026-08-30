# Week 1, Day 2 — Tasks

**Total time:** ~6 hours. Work through these in order — each block builds on the last. Activate your virtual environment from Day 1 before starting.

---

## Block 1 (45 min) — if/elif/else Practice

Create `week-1/day-2/conditionals_practice.py`:

1. Write a script that takes a number via `input()` and prints whether it's positive, negative, or zero.
2. Extend it: given an exam score (0–100) from `input()`, print a letter grade using this scale: 90+ = A, 80–89 = B, 70–79 = C, 60–69 = D, below 60 = F. Use `elif` chains.
3. Write a small check using truthy/falsy logic: given a list that may be empty, print "List has items" or "List is empty" using `if my_list:` (not `len()`).
4. Deliberately write a badly-indented `if` block, run it, read the error, then fix it.

**Checkpoint:** Grade calculator correctly handles at least 3 test scores you choose yourself (e.g., 95, 72, 40), and each check runs without errors.

---

## Block 2 (1 hour) — for Loops

Create `week-1/day-2/for_loops_practice.py`:

1. Loop through a list of 5 fruits and print each one in the format `"I like <fruit>"`.
2. Use `range()` to print all even numbers from 0 to 20.
3. Use `enumerate()` to print each fruit from your list along with its position, e.g. `"0: apple"`.
4. Given a list of numbers, use a `for` loop (not the built-in `sum()`) to manually calculate and print their total.
5. Use `break` to loop through numbers 1–100 and stop as soon as you find one divisible by 7 and 11 both — print which number it was.

**Checkpoint:** All 5 exercises run and produce correct output — verify the "divisible by 7 and 11" answer by hand.

---

## Block 3 (45 min) — while Loops

Create `week-1/day-2/while_loops_practice.py`:

1. Write a simple countdown from 10 to 1 using a `while` loop, then print "Liftoff!".
2. Write a "guess the number" style loop: set a fixed target number (e.g., `7`), and repeatedly ask the user (via `input()`) to guess until they get it right, printing "Try again" each wrong guess.
3. Deliberately write an infinite loop on purpose (e.g., forget to increment a counter), run it, and manually stop it (Ctrl+C) — this is so you recognize the symptom quickly in the future. Then fix it.

**Checkpoint:** The "guess the number" loop correctly exits only when the right number is entered.

---

## Block 4 (1.5 hours) — Functions

Create `week-1/day-2/functions_practice.py`:

1. Write a function `is_even(number)` that returns `True` or `False`.
2. Write a function `celsius_to_fahrenheit(celsius)` that returns the converted value (formula: `F = C * 9/5 + 32`).
3. Write a function `get_stats(numbers)` that takes a list and returns three values: minimum, maximum, and average — unpack all three into separate variables when calling it.
4. Write a function `greet_user(name, greeting="Hello")` with a default argument, and call it three different ways (no greeting, positional greeting, keyword greeting).
5. Write a function `describe_grade(score)` that reuses your grading logic from Block 1 — this time as a reusable function that returns the letter grade instead of printing it directly. Call it with 3 different scores and print each result.

**Checkpoint:** Every function is called at least twice with different inputs, and you print the results to confirm they're correct.

---

## Block 5 (1 hour) — try/except Error Handling

Create `week-1/day-2/error_handling_practice.py`:

1. Write code that asks the user for a number via `input()` and divides 100 by it, wrapped in `try`/`except` to handle both `ValueError` (non-numeric input) and `ZeroDivisionError` (dividing by zero) with clear, separate messages for each.
2. Add a `finally` block that always prints `"Attempt finished."` regardless of what happened.
3. Take your `celsius_to_fahrenheit` function from Block 4 and wrap a call to it in a `try`/`except` that catches the case where a user enters a non-numeric string instead of a number.
4. Write a function `safe_divide(a, b)` that returns the division result normally, but returns `None` and prints a warning instead of crashing if `b` is `0`.

**Checkpoint:** Running the script with intentionally bad input (letters instead of numbers, dividing by zero) never crashes the program — it always prints a helpful message instead.

---

## Block 6 (1 hour) — Mini Project: "Simple Quiz Game"

Build `week-1/day-2/quiz_game.py`, combining everything from today:

**Requirements:**
1. Create a list of at least 5 quiz questions, where each question is stored as a dictionary with `"question"` and `"answer"` keys, e.g.:
   ```python
   questions = [
       {"question": "What is 2 + 2?", "answer": "4"},
       {"question": "Capital of France?", "answer": "Paris"},
       # ... at least 3 more
   ]
   ```
2. Write a function `run_quiz(questions)` that loops through each question, asks it via `input()`, checks the answer (case-insensitive), and keeps a running score.
3. Use `try`/`except` so that if something unexpected happens during input, the quiz doesn't crash — it should print a message and continue to the next question.
4. At the end, print the user's final score out of the total number of questions, and a message that changes based on performance (e.g., "Great job!" for 80%+, "Keep practicing!" below that) — reuse `if`/`elif`/`else` logic here.
5. Add comments explaining the structure of your dictionary-based questions and why you chose that structure.

**Stretch goal (optional):** Let the user choose to play again in a `while` loop, without restarting the script.

---

## End of Day Checklist

- [ ] Comfortable writing `if`/`elif`/`else` chains, including compound conditions with `and`/`or`
- [ ] Understand the difference between `for` (known iterable) and `while` (condition-based) loops
- [ ] Can write a function with parameters, default arguments, and a `return` value
- [ ] Understand that variables inside a function are local by default
- [ ] Comfortable using `try`/`except`/`finally` to handle bad input without crashing
- [ ] Completed the Simple Quiz Game mini-project, running cleanly from the terminal

If any checkbox is unchecked, revisit that block before Day 3 — tomorrow's core data structures (lists, dicts, sets) build directly on today's loops and functions.
