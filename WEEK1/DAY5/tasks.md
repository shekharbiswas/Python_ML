# Week 1, Day 5 — Tasks (Light Load)

**Total time:** ~ 4.5 hours. 
---

## Block 1 (45 min) — Environment & Connection Setup

1. Install what's needed: `pip install snowflake-connector-python python-dotenv`, then update `requirements.txt`.
2. Create a `.env` file in your project root with your Snowflake credentials (account, user, password, warehouse, database, schema).
3. Add `.env` to your `.gitignore` (create the file if you don't have one yet).
4. Create `week-1/day-5/snowflake_helpers.py` with `get_connection()` as shown in `concepts.md`.

**Checkpoint:** No credentials appear anywhere in your `.py` files — only in `.env`, which is gitignored.

---

## Block 2 (1.5 hour) — First Connection & Query

Create `week-1/day-5/first_connection.py`:

1. Import your `get_connection()` function from `snowflake_helpers`.
2. Wrap the connection and a simple test query (`SELECT CURRENT_VERSION()`) in `try`/`except`/`finally`, printing the result.
3. Run a second query against a real table in your Snowflake account — `SELECT * FROM <your_table> LIMIT 10` — using a regular cursor, and print each row as a tuple.
4. Re-run the same query using `DictCursor` instead, and print each row as a dictionary this time. Compare the two outputs.

**Checkpoint:** Both versions run without errors and return the same underlying data, just shaped differently (tuple vs. dict).

---

## Block 3 (1.5 hour) — Reusable Query Function + Mini Task

Create `week-1/day-5/query_practice.py`:

1. Add a `run_query(query)` function to `snowflake_helpers.py` (as shown in `concepts.md`), and import it here instead of repeating connection code.
2. Run a query that returns at least 2 columns from a real table, and loop through the results, printing a formatted line per row (reuse an f-string, like you did all week).
3. Take the results and, using what you learned on Day 3, build a Python `list` containing just one column's values (e.g., all the names) — a small comprehension is fine here.
4. Print how many rows were returned using `len()`.

**Checkpoint:** `query_practice.py` uses `run_query()` from your helper module — no duplicated connection logic in this file.

---

## Block 4 (45 min) —  Review, Your Choice

Pick **one** of the following, based on what felt least solid this week:

- Revisit one exercise from Day 2 (functions) or Day 3 (data structures) and redo it from memory, without looking at your old solution.
- Re-read the "Key Takeaways" sections from Days 1–4 and jot down (in a new file, `week-1/review_notes.md`) 3 things you want to remember going into Week 2.
- Extend today's `snowflake_helpers.py` with one more small function of your choosing (e.g., a `run_query_to_list_of_names(query, column)` helper) — optional, only if you have energy left.

**Checkpoint:** None required — this block is for consolidation, not new material.

---

## End of Day (and Week 1) Checklist

- [ ] `.env` file created and excluded from Git via `.gitignore`
- [ ] Successfully connected to Snowflake from Python
- [ ] Ran at least one real query and printed results both as tuples and as dictionaries
- [ ] `snowflake_helpers.py` contains a working, reusable `get_connection()` and `run_query()`
- [ ] Feel reasonably confident with: variables/types, control flow, functions, core data structures, files, and basic OOP

---

## Looking Ahead to Week 2

Next week, `snowflake_helpers.py` becomes the starting point for every exercise — instead of printing query results manually, you'll load them straight into a `pandas` DataFrame and start doing real data wrangling and exploratory analysis on data pulled live from Snowflake.
