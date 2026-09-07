# Week 2, Day 2 — Tasks
## Missing Data & Merging (6-Hour Session)

Read today's Concepts doc first. You'll need **two** related raw Snowflake tables today — pick a table you used on Day 1 plus a second table that shares a join key with it (a fact table + a reference/dimension table is ideal; ask if you're unsure which pair makes sense in your environment).

---

## Block 1 (Hour 1) — Missing Data Audit
1. Load your primary table via `fetch_pandas_all()`.
2. Run `df.isna().sum()` and `df.isna().mean() * 100`.
3. For every string/categorical column, also run `.value_counts()` and check for placeholder values that *aren't* `NaN` but represent missingness (`''`, `'N/A'`, `'null'`, `-1`, etc.).
4. Build a short table (in a markdown cell or comment) listing every column with any missingness, the % missing, and whether it's `NaN`-based or a disguised placeholder.

**Note down:** at least one column where the "true" missing percentage was higher than what `isna()` alone reported.

---

## Block 2 (Hour 2) — Decide and Justify a Strategy Per Column
For each column you flagged in Block 1, pick one of: **drop**, **fill**, or **flag-and-keep** — and implement it.

1. For at least one column, use `dropna(subset=[...])` and confirm `len(df)` before/after.
2. For at least one column, use a domain-justified `fillna()` (not just `fillna(0)` everywhere — pick a fill value you can defend).
3. For at least one column, instead of filling, create a new boolean indicator column (`df['col_is_missing'] = df['col'].isna()`) and leave the original column's nulls in place.

**Note down:** for each of the three, one sentence justifying *why* that strategy (not another one) was the right call for that specific column.

---

## Block 3 (Hour 3) — Your First Merge
1. Load your second table.
2. Identify the join key(s) — check whether the column names match exactly or need `left_on=`/`right_on=`.
3. Run an **inner** merge first, printing `len(left)`, `len(right)`, `len(merged)` before and after.
4. Re-run the same merge as a **left** join, and again as an **outer** join. Compare row counts across all three and explain why they differ.

**Note down:** the three row counts (inner/left/outer) and, in one or two sentences, why the difference makes sense given what you know about the data.

---

## Block 4 (Hour 4) — Validate and Audit the Join
1. Re-run your merge with `validate='many_to_one'` (or whichever cardinality you actually expect — check first with `.duplicated(subset=[key]).sum()` on each side if you're unsure).
2. If it raises a `MergeError`, don't just remove `validate=` to make it pass — investigate which side has duplicate keys and why, then decide whether that's expected or a data quality issue worth flagging.
3. Re-run the merge with `indicator=True` and check the `_merge` column's value counts. Look specifically at any `'left_only'` or `'right_only'` rows — do they reveal a real data issue (e.g., orphaned foreign keys) or expected non-overlap?

**Note down:** what `validate=` and `indicator=True` each caught (or confirmed was clean) that you wouldn't have noticed from row counts alone.

---

## Block 5 (Hour 5) — Build a Reusable Cleaning + Merge Function
Consolidate Blocks 1–4 into a single function that takes the two raw DataFrames and returns a cleaned, merged result:
```python
def clean_and_merge(df_primary: pd.DataFrame, df_secondary: pd.DataFrame) -> pd.DataFrame:
    # your missing-data handling from Block 2
    # your validated merge from Block 4
    ...
    return result
```
This should be a real function you could hand to a teammate — clear variable names, no hardcoded magic values without a comment explaining them, and it should raise/warn (not silently continue) if the merge cardinality assumption is violated.

---

## Block 6 (Hour 6) — Capstone: Compare Against the dbt Model
1. Find the dbt model (staging or intermediate) that performs a comparable join/cleaning step to what you just built.
2. Pull its output into a DataFrame.
3. Compare your `clean_and_merge()` output against the dbt model's output:
   - Row counts — do they match? If not, why not?
   - Null handling — did dbt coalesce/handle missingness the same way you chose to, or differently?
   - Any columns present in one but not the other?
4. Write 4–6 sentences: where did your hand-built logic match the dbt model, where did it diverge, and if it diverged, which approach do you think is more defensible and why?

---

## End-of-Day Checklist
- [ ] Audited a raw table for both `NaN` and disguised-placeholder missingness
- [ ] Applied and justified drop / fill / flag-and-keep strategies on different columns
- [ ] Performed inner, left, and outer merges and explained the row-count differences
- [ ] Used `validate=` and `indicator=True` to catch and investigate join issues
- [ ] Built a reusable `clean_and_merge()` function
- [ ] Compared the result against the corresponding dbt model's output and can explain any divergence