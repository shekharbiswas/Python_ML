# Week 2, Day 2 — Concepts
## Missing Data, Merging & Joining Datasets

---

## Question of the Day
**"How do I combine and clean data pulled from multiple Snowflake tables?"**

Yesterday you learned to slice and filter a single table correctly. Today's the day this stops being single-table work — you'll be pulling two (or more) raw tables into pandas, handling the mess each one carries, and joining them into something coherent. This is exactly the reasoning behind most dbt staging and intermediate models, just done by hand.

---

## 1. Missing Data — Detect Before You Decide
Never assume you know how much is missing. Start every table with:
```python
df.isna().sum()          # count of nulls per column
df.isna().mean() * 100   # percentage missing per column
```
`NaN` isn't pandas' only representation of "missing" in practice — Snowflake data landing in pandas can also carry empty strings (`''`), placeholder sentinels (`'N/A'`, `-1`, `'null'` as literal text), or `None`. Don't trust `isna()` alone on string columns pulled from a raw source; check `df['col'].value_counts()` for suspicious placeholder values before deciding a column is "clean."

**Three strategies, and when each is defensible:**
- **Drop** (`dropna()`) — only when missingness is rare and effectively random. `subset=` lets you drop based on specific columns rather than the whole row. Dropping silently shrinks your dataset — always compare `len(df)` before/after and be able to justify the loss.
- **Fill** (`fillna()`) — a defensible fill needs a domain reason: `0` for a count that's genuinely absent, a sentinel like `'Unknown'` for a missing category, forward/backward fill (`ffill`/`bfill`) only when there's a genuine ordering (time series) that justifies carrying a value forward.
- **Leave it, but flag it** — sometimes the right move is `df['col'].isna()` as its own indicator column, rather than destroying the "missingness" signal by filling it. This is a real pattern in dbt staging models — a `is_missing_x` flag column is common.

The wrong move, always: filling with a default without checking whether the missingness is informative first. A null "cancellation_reason" almost certainly means "not cancelled" — filling it with `'Unknown'` destroys that meaning.

---

## 2. Merging — `pd.merge()` Is Your SQL JOIN
```python
pd.merge(left, right, on='key', how='inner')
```
`how` maps directly to SQL join types you already know: `'inner'`, `'left'`, `'right'`, `'outer'`. The mental model transfers 1:1 — the pandas-specific things to get right are:

- **`on=` vs `left_on=`/`right_on=`** — use the latter when the join key has different column names in each table (extremely common pulling from raw Snowflake sources with inconsistent naming).
- **`validate=`** — pass `'one_to_one'`, `'one_to_many'`, `'many_to_one'`, or `'many_to_many'` and pandas will raise a `MergeError` if the actual data violates your assumption. **Use this by default, not as an afterthought** — a silent many-to-many join is the single most common source of a mysteriously-inflated row count in real pipelines, and `validate=` catches it before it corrupts downstream analysis.
- **`indicator=True`** — adds a `_merge` column (`'left_only'`, `'right_only'`, `'both'`) so you can immediately audit what didn't match, instead of just trusting the join worked.

---

## 3. `merge()` vs `join()` vs `concat()` — Pick the Right Tool
- **`merge()`** — general-purpose, column-based joins (what you'll use almost all the time).
- **`.join()`** — a `merge()` convenience method specifically for joining on the **index** rather than a column. Worth knowing, rarely your first choice.
- **`pd.concat()`** — stacking, not joining. `axis=0` stacks rows (e.g., combining monthly extracts of the same schema); `axis=1` stacks columns side by side (dangerous unless indexes are guaranteed aligned — this is not a join, it just glues by position/index).

Don't reach for `concat(axis=1)` when what you actually want is a `merge()` — they look similar but `concat` doesn't match on keys, it aligns on the index, which will silently produce garbage if the two frames aren't already index-aligned.

---

## 4. Row-Count Discipline
Every merge should be bracketed by an explicit check:
```python
print(len(left), len(right), len(merged))
```
If `len(merged)` isn't what you expected given the join type and `validate=`, stop and investigate before continuing — duplicate keys on the "many" side are the usual cause of unexpected row inflation.

---

## 5. Why This Matters for the dbt Comparison
A dbt staging or intermediate model almost always does at least one of: coalescing nulls with a documented business reason, joining in a reference/dimension table, and asserting (via tests like `unique`, `not_null`, or relationship tests) exactly the kind of row-count and key-integrity guarantees `validate=` gives you for free in pandas. Today, when you clean and join by hand, you're reproducing both the transformation *and* the implicit test suite a dbt model would carry — keep that connection in mind for Day 5.

---

## What Success Looks Like Today
By the end of Day 2, you should be able to:
- Audit a raw table for missing data, including non-`NaN` representations of missingness
- Justify — not just execute — a drop/fill/flag decision per column
- Perform merges with the correct `how`, using `left_on`/`right_on` when needed
- Use `validate=` and `indicator=True` as a default habit, not an afterthought
- Explain why a merge's row count changed the way it did, every time