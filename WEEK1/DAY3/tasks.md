# Week 1, Day 3 — Tasks

**Total time:** ~6 hours. Work through these in order. Activate your virtual environment before starting.

---

## Block 1 (45 min) — Lists Practice

Create `week-1/day-3/lists_practice.py`:

1. Create a list of 6 of your favorite movies.
2. Use `.append()` to add a new one, `.insert()` to add one at position 2, and `.remove()` to remove one by name.
3. Print the list reversed using slicing (`[::-1]`) — not `.reverse()`.
4. Print just the first 3 and just the last 2 movies using slicing.
5. Sort the list alphabetically with `.sort()` and print it.
6. Check whether a specific movie is `in` the list and print the result.

**Checkpoint:** All slicing operations produce the exact expected items — double check indices by hand if unsure.

---

## Block 2 (30 min) — Tuples Practice

Create `week-1/day-3/tuples_practice.py`:

1. Create a tuple representing a fixed coordinate, e.g. `location = (12.9716, 77.5946)` (latitude, longitude).
2. Access and print each value individually using indexing.
3. Try to modify one value directly (e.g. `location[0] = 10`) — run it, read the `TypeError`, then remove that line.
4. Write a function `get_min_max(numbers)` (if you don't already have one from Day 2) that returns a tuple of `(min, max)`, and unpack the result into two separate variables when calling it.

**Checkpoint:** You can explain out loud, in one sentence, why tuples are useful here instead of a list.

---

## Block 3 (1 hour) — Dictionaries Practice

Create `week-1/day-3/dicts_practice.py`:

1. Create a dictionary representing a product with keys: `name`, `price`, `in_stock` (bool), `quantity`.
2. Print each value by key, then add a new key `discount_percent`.
3. Use `.get()` to safely access a key that doesn't exist (e.g. `"category"`), providing a default value of `"Uncategorized"`.
4. Loop through the dictionary using `.items()` and print each key-value pair in the format `"key: value"`.
5. Write a function `calculate_final_price(product)` that takes the product dictionary and returns `price * (1 - discount_percent / 100)`.
6. Create a second dictionary for a different product, and write a function `compare_prices(product1, product2)` that returns the name of the cheaper one (after discount).

**Checkpoint:** `compare_prices` correctly identifies the cheaper product for at least 2 different test pairs you construct.

---

## Block 4 (45 min) — Sets Practice

Create `week-1/day-3/sets_practice.py`:

1. Create two sets of customer IDs, e.g. `week1_customers = {101, 102, 103, 104}` and `week2_customers = {103, 104, 105, 106}`.
2. Find and print: customers who ordered BOTH weeks (intersection), customers who ordered in EITHER week (union), and customers who ONLY ordered in week 1 (difference).
3. Take a list with duplicate values (e.g. `[1, 2, 2, 3, 3, 3, 4]`) and convert it to a set to remove duplicates, then back to a sorted list, and print the result.
4. Time-check (conceptually, no need to actually benchmark): explain in a comment why checking `x in my_set` is generally faster than `x in my_list` for large collections.

**Checkpoint:** The intersection/union/difference results are correct — verify at least one by counting manually.

---

## Block 5 (1 hour) — List Comprehensions

Create `week-1/day-3/comprehensions_practice.py`:

1. Given `numbers = list(range(1, 21))`, use a comprehension to build a list of only the even numbers.
2. Using the same list, build a list of each number squared, but only for numbers greater than 10.
3. Given a list of names `["alice", "BOB", "Carla", "dave"]`, use a comprehension to build a list where every name is capitalized properly (first letter upper, rest lower) — look up the right string method for this.
4. Rewrite one of your Day 2 `for` loops (e.g. the one calculating a total) as a list comprehension where it makes sense, and compare readability — write a one-line comment on which version you find clearer and why.

**Checkpoint:** Every comprehension produces the exact same output as an equivalent `for` loop would — verify by writing the loop version for at least one and comparing outputs.

---

## Block 6 (1.5 hours) — Mini Project: "Student Grade Book"

Build `week-1/day-3/gradebook.py`, combining everything from today:

**Requirements:**
1. Create a list of dictionaries representing students, each with `"name"`, `"grades"` (a list of 3–5 numeric scores), e.g.:
   ```python
   students = [
       {"name": "Alice", "grades": [88, 92, 79]},
       {"name": "Bob", "grades": [65, 70, 60]},
       {"name": "Carla", "grades": [95, 100, 98]},
       # add at least 2 more
   ]
   ```
2. Write a function `average_grade(grades)` that returns the average of a list of scores.
3. Write a function `letter_grade(avg)` that reuses your Day 2 grading logic (A/B/C/D/F) based on the average.
4. Loop through all students, calculate each one's average and letter grade, and print a formatted line per student, e.g.:
   ```
   Alice: Average 86.3 — Grade B
   ```
5. Use a list comprehension to build a list of just the names of students with an average of 90+ ("honor roll").
6. Use a set to find how many *unique* letter grades appear across the whole class (e.g., if grades are A, B, B, C, A → the set has 3 unique values).
7. Print a short summary: total students, class average (average of all students' averages), and the honor roll list.

**Stretch goal (optional):** Sort the students by average grade (highest to lowest) before printing, using `sorted()` with a `key=` argument — look up how to sort a list of dicts by one of their values.

---

## End of Day Checklist

- [ ] Comfortable indexing and slicing lists, and can explain why the end index in a slice is exclusive
- [ ] Know when to use a tuple instead of a list
- [ ] Comfortable using `.get()` on a dictionary and looping with `.items()`
- [ ] Understand what a set is for and can use union/intersection/difference
- [ ] Can write a simple list comprehension with a condition
- [ ] Comfortable navigating a "list of dictionaries" structure — this is critical before Week 2
- [ ] Completed the Student Grade Book mini-project, running cleanly from the terminal

If any checkbox is unchecked, revisit that block before Day 4 — tomorrow covers files, modules, and basic OOP, and Friday connects everything to Snowflake.
