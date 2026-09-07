# Week 2, Day 3 — Tasks
## GroupBy, Aggregation & NumPy (6-Hour Session)

Read today's Concepts doc first. Keep using the **same primary raw Snowflake table** you've worked with since Day 1 — you already know its columns and quirks, so today's effort goes entirely into summarizing it well, not re-learning a new dataset.

---

## Block 1 (Hour 1) — Basic GroupBy
1. Pick a categorical column (region, category, status — whatever your table has) and a numeric column.
2. Run a single-column, single-stat groupby: `df.groupby('category_col')['numeric_col'].sum()`.
3. Run the same grouped sum but sorted descending by value.
4. Try `.size()` vs `.count()` on the same groupby — note the difference (`.size()` counts all rows including nulls in the numeric column, `.count()` doesn't).

**Note down:** which category/group has the highest and lowest total, and one sentence guessing why (based on what you know about the data so far).

---

## Block 2 (Hour 2) — Multiple Aggregations, Done Properly
1. Using named aggregation, build a summary with at least 3 different metrics across at least 2 different columns:
   ```python
   df.groupby('category_col').agg(
       total_x=('numeric_col_1', 'sum'),
       avg_y=('numeric_col_2', 'mean'),
       distinct_z=('id_col', 'nunique'),
   )
   ```
2. Deliberately compare `nunique()` vs `count()` on an ID-like column in your table — check whether they actually differ (i.e., whether your raw table has duplicate IDs).

**Note down:** whether `nunique()` and `count()` gave different results for your ID column, and what that implies about the raw table's data quality.

---

## Block 3 (Hour 3) — Multi-Column GroupBy and Reshaping
1. Group by two categorical columns at once and aggregate a numeric column.
2. Call `.reset_index()` on the result and confirm you get back flat, mergeable columns.
3. Instead of `reset_index()`, try `.unstack()` on the same grouped result — compare the wide, pivoted shape against the flat one from step 2. Note when you'd want each.

---

## Block 4 (Hour 4) — `transform()` in Practice
1. Use `transform()` to add a new column showing each row's value as a percentage of its group's total:
   ```python
   df['pct_of_group'] = df['numeric_col'] / df.groupby('category_col')['numeric_col'].transform('sum')
   ```
2. Confirm the row count of `df` is unchanged (this is the whole point of `transform` vs `agg`).
3. Now try to get the same result using a merge instead (group, aggregate, then merge the group totals back onto the original rows). Compare the two approaches — which was less code, and which would you actually use in practice?

**Note down:** your own one-sentence rule for when to reach for `transform()` instead of a groupby-then-merge pattern.

---

## Block 5 (Hour 5) — NumPy Underneath
1. Pull a numeric column out as a raw NumPy array: `arr = df['numeric_col'].to_numpy()`.
2. Do a broadcasted operation directly on the array (`arr * 1.1`, or similar) and confirm it matches what you'd get doing the same operation on the pandas Series.
3. Use `np.where()` to create a new categorical column based on a numeric threshold (e.g., `'high'`/`'low'`), and compare it against writing the same logic with `.apply()` + a lambda — time both with `%timeit` or `time.perf_counter()`.
4. Deliberately introduce a `NaN` into a small test array and compare `arr.sum()` vs `np.nansum(arr)` — confirm you understand why they differ.

**Note down:** the timing difference between `np.where()` and `.apply()` for the same conditional logic — another concrete data point (like Day 1's vectorization timing) for why idiomatic pandas/NumPy matters.

---

## Block 6 (Hour 6) — Capstone: Reproduce a dbt Mart Metric
1. Find a dbt mart/reporting model whose logic is essentially a `GROUP BY` + aggregation over your raw table (or a table derived from it).
2. Reproduce that model's core metric(s) using `groupby().agg()` with named aggregation in pandas.
3. Pull the actual dbt model's output and compare your pandas result against it, row by row where possible (a merge with `validate=` from Day 2 is a good way to line them up).
4. Write 4–6 sentences: did your numbers match? If not, what did the dbt model do differently (a filter you didn't apply, a different grouping grain, deduping before aggregating)?

---

## End-of-Day Checklist
- [ ] Ran single- and multi-stat groupby aggregations, including named aggregation
- [ ] Correctly distinguished `nunique()` from `count()` on real data
- [ ] Performed a multi-column groupby and reshaped the result both flat and pivoted
- [ ] Used `transform()` to attach group-level stats back onto original rows, and compared it to a groupby-then-merge alternative
- [ ] Used `.to_numpy()`, broadcasting, and `np.where()`, and can explain NaN-handling differences between `.sum()` and `np.nansum()`
- [ ] Reproduced a real dbt mart metric in pandas and compared it against the model's actual output