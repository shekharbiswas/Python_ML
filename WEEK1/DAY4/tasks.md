# Week 1, Day 4 — Tasks

**Total time:** ~6 hours. Work through these in order. Activate your virtual environment before starting.

---

## Block 1 (45 min) — Reading & Writing Text Files

Create `week-1/day-4/files_practice.py`:

1. Write a list of 5 short strings (e.g., quotes or facts) to a text file called `notes.txt`, one per line, using `with open(...)`.
2. Read the file back and print each line with its line number (e.g., using `enumerate()` from Day 3).
3. Append one more line to the file without erasing the existing content, then read and print the full file again to confirm it worked.
4. Deliberately open the file with `"w"` mode a second time and write just one new line — read it back and observe that the earlier content is gone. Add a comment explaining what happened and why.

**Checkpoint:** You can explain, in your own words, the difference between `"w"` and `"a"` mode without looking it up.

---

## Block 2 (1 hour) — CSV Files

Create `week-1/day-4/csv_practice.py` and a data file `week-1/day-4/students.csv` with this content:
```
name,grade,city
Alice,92,Bangalore
Bob,78,Mumbai
Carla,85,Delhi
Dave,64,Chennai
```

1. Read the CSV using `csv.DictReader` and print each student's name and grade.
2. Filter and print only students with a grade above 80 (reuse your Day 3 logic style).
3. Calculate and print the average grade across all students in the file.
4. Write a NEW CSV file `week-1/day-4/high_performers.csv` containing only the students with grade > 80, using `csv.DictWriter` (include a header row).
5. Open `high_performers.csv` in a text editor (or read it back in Python) to confirm it looks correct.

**Checkpoint:** `high_performers.csv` contains exactly the students you expect, with a proper header row.

---

## Block 3 (1 hour) — Modules

Create two files in `week-1/day-4/`:

**`grade_helpers.py`:**
1. Move your `average_grade(grades)` and `letter_grade(avg)` functions (from Day 3) into this file.
2. Add one new function: `pass_or_fail(avg)` that returns `"Pass"` if avg >= 60, else `"Fail"`.

**`main_day4.py`:**
1. Import `grade_helpers` (try both `import grade_helpers` and `from grade_helpers import average_grade` styles — use each at least once).
2. Reuse your Day 3 `students` list of dictionaries, and for each student, print their average, letter grade, AND pass/fail status using the imported functions.
3. Wrap your script's main logic in a `main()` function, and use the `if __name__ == "__main__":` pattern to call it.

**Checkpoint:** `main_day4.py` runs cleanly and correctly uses functions imported from `grade_helpers.py` — not copy-pasted duplicates.

---

## Block 4 (1.5 hours) — Basic OOP

Create `week-1/day-4/oop_practice.py`:

1. Write a `Student` class with `__init__(self, name, grades)`, and methods `average_grade()`, `letter_grade()`, and `pass_or_fail()` (reuse the logic from Block 3, just moved into the class).
2. Create 4 `Student` objects with different names and grade lists.
3. Loop through them (put them in a list first) and print a formatted summary line for each, e.g.:
   ```
   Alice — Avg: 86.3, Grade: B, Status: Pass
   ```
4. Add a new method `add_grade(self, new_grade)` that appends a grade to `self.grades` — call it on one student and confirm their average updates correctly afterward.
5. Add a `__str__` method to the class (look up what this special method does) so that `print(alice)` shows a nice readable summary instead of the default `<Student object at 0x...>` output.

**Checkpoint:** `print(some_student)` shows a clean, readable line instead of the default Python object representation.

---

## Block 5 (1 hour) — Combining Files + OOP

Create `week-1/day-4/students_from_csv.py`:

1. Read `students.csv` from Block 2 using `csv.DictReader`.
2. For each row, create a `Student` object (reusing your class from Block 4) — note the CSV only has one grade per row, so store it as a single-item list: `grades=[int(row["grade"])]`.
3. Store all the created `Student` objects in a list.
4. Loop through the list and print each student's summary using the `__str__` method from Block 4.
5. Find and print the name of the student with the highest average grade (hint: use `max()` with a `key=` argument, or loop manually and track the highest so far).

**Checkpoint:** The correct top student is identified and printed, matching what you'd expect from manually checking the CSV.

---

## Block 6 (1 hour) — Mini Project: "Personal Library Tracker"

Build `week-1/day-4/library_tracker.py`, combining everything from today:

**Requirements:**
1. Create a `Book` class with `__init__(self, title, author, pages, finished=False)`.
2. Add a method `mark_finished(self)` that sets `finished = True`.
3. Add a `__str__` method that prints something like: `"Atomic Habits by James Clear (320 pages) — Finished"`.
4. Create a list of at least 5 `Book` objects (mix of finished and not finished).
5. Write a function `save_books_to_csv(books, filename)` that writes all books out to a CSV file with columns: title, author, pages, finished.
6. Write a function `load_books_from_csv(filename)` that reads that CSV back in and reconstructs a list of `Book` objects from it.
7. In your main script logic: create the books, save them to `library.csv`, then load them back from the file into a NEW list, and print each one using `__str__` to confirm the round-trip worked correctly.
8. Use the `if __name__ == "__main__":` pattern for your script's entry point.

**Stretch goal (optional):** Split this into two files — `book.py` (containing just the `Book` class) and `library_tracker.py` (importing `Book` and containing the save/load logic and main script).

---

## End of Day Checklist

- [ ] Comfortable reading and writing text files with `with open(...)`, and know the difference between `"w"` and `"a"`
- [ ] Can read and write CSV files using `csv.DictReader`/`DictWriter`
- [ ] Can split code across multiple files using `import`, in more than one import style
- [ ] Understand `if __name__ == "__main__":` and why it's used
- [ ] Can write a class with `__init__`, at least two methods, and a `__str__` method
- [ ] Completed the Personal Library Tracker mini-project, including the save-to-CSV and load-from-CSV round trip

If any checkbox is unchecked, revisit that block before Day 5 — tomorrow you connect everything to Snowflake, and the file-reading and object patterns from today carry directly into how query results get handled.
