# Week 2, Day 1 — Tasks
## pandas Fundamentals (6-Hour Session)

Read today's Concepts doc before starting — this session assumes you already understand `.loc` vs `.iloc`, boolean masks, and chained-indexing risk, and focuses on applying them against real Snowflake data. Pick any raw source table available in your Snowflake environment with at least 1,000+ rows and a handful of different column types (numeric, string, date) — use the same one throughout the day for consistency.

---

## Block 1 (Hour 1) — Connect and Load Properly
1. Using your Week 1 Snowflake connection setup, install the pandas extra if you haven't:
   ```
   pip install "snowflake-connector-python[pandas]"
   ```
2. Run a query against your chosen raw table and load it using `cursor.fetch_pandas_all()`.
3. Immediately inspect what you got back:
   ```python
   df.info()
   df.dtypes
   df.shape
   df.head()
   ```
4. **Do this deliberately:** also load the same query result using the manual method (`pd.DataFrame(cursor.fetchall(), columns=[...])`) and compare `.dtypes` between the two approaches.

**Note down:** which columns differ in dtype between the two loading methods, and why that matters (think about what happens the first time you try to do math on a column that came back as `object` instead of `int64`).

---

## Block 2 (Hour 2) — Indexing Precision
Using `fetch_pandas_all()`'s output as `df` for the rest of the day:

1. Select the first 10 rows using `.iloc`.
2. Select the same 10 rows using `.loc` (careful — your index may not be 0-based or contiguous; check `df.index` first).
3. Pick a column that looks like it could serve as a natural unique identifier for a row. Set it as the index with `.set_index()`, then select a specific row by that label using `.loc`.
4. Deliberately write a chained-indexing assignment that should trigger `SettingWithCopyWarning` (e.g., filtering then trying to assign into the filtered result). Confirm you see the warning, then rewrite it correctly as a single `.loc` call and confirm the warning disappears.

**Note down:** in your own words, why does chained indexing produce a warning instead of a hard error? (Hint: think about what pandas can and can't know about whether your intermediate result is a view or a copy.)

---

## Block 3 (Hour 3) — Filtering, Multi-Condition
1. Write a single-condition boolean filter (e.g., a numeric column above some threshold).
2. Write a multi-condition filter combining at least two columns with `&`, properly parenthesized.
3. Rewrite the same multi-condition filter using `.query()`. Confirm both approaches return identical results (`.equals()` is useful here).
4. Find or construct a condition using `.isin()` against a list of values, and one using string matching (`.str.contains()`).
5. Chain a filter and a column selection together correctly in one `.loc` call (row filter + specific columns, not all columns).

**Note down:** one real observation about this table's data — something a filter revealed that you didn't expect (an outlier, an unexpected category, a suspicious concentration of one value).

---

## Block 4 (Hour 4) — Sorting
1. Sort the DataFrame by one column, descending.
2. Sort by two columns with mixed sort order (first ascending, second descending).
3. Compare `df.sort_values(...)` output's index to the original — note that the index doesn't reset automatically. Use `.reset_index(drop=True)` and explain to yourself why you'd want that in some cases but not others (e.g., if you still need to trace rows back to their original position).
4. Use `.sort_index()` after your sort_values call and confirm you're back to original row order.

---

## Block 5 (Hour 5) — Vectorization vs. the Loop Trap
1. Pick a numeric column and compute a new derived column two ways:
   - **Vectorized:** `df['new_col'] = df['col_a'] * 2`
   - **Loop-based (anti-pattern, for comparison only):** using `.iterrows()` to compute the same thing row by row into a list, then assign it back.
2. Time both approaches using `%timeit` (in a notebook) or `time.perf_counter()` (in a script).

**Note down:** the actual timing difference you observed. This isn't a toy warning — carry the number forward as your own evidence for why vectorization matters, not just something you were told.

---

## Block 6 (Hour 6) — Capstone: Structural Comparison Against dbt
1. Find the dbt staging model that corresponds to your raw source table (ask if you're unsure which one maps to it).
2. Pull the staging model's output into a second DataFrame the same way you loaded the raw table.
3. Compare, using what you learned today (not cleaning yet — that's Day 2):
   - `df_raw.shape` vs `df_staging.shape` — row count difference?
   - `df_raw.dtypes` vs `df_staging.dtypes` — any type changes?
   - Column names — did anything get renamed or dropped?
4. Write 3–5 sentences summarizing what structurally changed between raw and staging, using only what you can observe from shape/dtypes/columns today. You'll go deeper into *why* on Day 5.

---

## End-of-Day Checklist
- [ ] Loaded a raw Snowflake table via `fetch_pandas_all()` and compared it against the manual-construction method
- [ ] Demonstrated correct use of `.loc` vs `.iloc`, including a custom-set index
- [ ] Reproduced and then fixed a `SettingWithCopyWarning`
- [ ] Wrote multi-condition boolean filters and an equivalent `.query()` version
- [ ] Sorted by multiple columns with mixed order and understand `.reset_index()`
- [ ] Measured and can state the real vectorization-vs-loop timing difference
- [ ] Completed a structural (shape/dtype/columns) comparison against the corresponding dbt staging model