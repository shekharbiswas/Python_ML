# Week 2, Day 3 — Concepts
## GroupBy & Aggregation, NumPy Array Basics

---

## Question of the Day
**"How do I reproduce SQL-style `GROUP BY` logic in Python, and where does NumPy fit underneath pandas?"**

You're staying on the same raw table you've used since Day 1 — today's new capability is summarizing it. By the end of today you should be as fluent in `groupby()` as you already are in SQL's `GROUP BY`, and understand that pandas' speed comes from NumPy doing the actual numeric work underneath.

---

## 1. GroupBy Is Split-Apply-Combine — Say the Phrase, Understand the Mechanics
```python
df.groupby('region')['sales'].sum()
```
Three steps happen, always, whether you think about them or not:
1. **Split** — the DataFrame is broken into groups, one per unique value (or combination of values) of the group key.
2. **Apply** — some function runs independently on each group.
3. **Combine** — the per-group results are stitched back into a single Series/DataFrame.

`groupby()` itself is lazy — it doesn't compute anything until you chain an aggregation onto it. `df.groupby('region')` alone returns a `DataFrameGroupBy` object, not a result.

---

## 2. Single vs. Multiple Aggregations — Use `.agg()` Deliberately
- **Single column, single stat:** `df.groupby('region')['sales'].sum()`
- **Single column, multiple stats:** `df.groupby('region')['sales'].agg(['sum', 'mean', 'count'])`
- **Multiple columns, different stats each — named aggregation (preferred for anything going into a report or handed to someone else):**
```python
df.groupby('region').agg(
    total_sales=('sales', 'sum'),
    avg_discount=('discount', 'mean'),
    order_count=('order_id', 'nunique'),
)
```
Named aggregation should be your default the moment you're aggregating more than one column — it produces clean, immediately-readable output column names instead of pandas' default MultiIndex column mess.

**`nunique()` vs `count()`** — a distinction that matters constantly: `count()` counts non-null rows; `nunique()` counts distinct values. If you want "how many orders" and there can be duplicate order IDs in a raw table (there often are, before dbt dedupes), you want `nunique()`, not `count()`.

---

## 3. Multi-Column GroupBy
```python
df.groupby(['region', 'category'])['sales'].sum()
```
This returns a Series with a **MultiIndex** (region, category). Two common next steps:
- `.reset_index()` — flattens the MultiIndex back into regular columns, usually what you want before merging or exporting.
- `.unstack()` — pivots one of the index levels into columns, useful for a wide, report-style view (e.g., regions as rows, categories as columns).

---

## 4. `agg()` vs `transform()` vs `apply()` — Know Which One You Need
This trips people up constantly, so be precise:
- **`agg()`** — many rows in, **one row out per group**. Use for summaries.
- **`transform()`** — many rows in, **same number of rows out**, broadcast back to the original shape. Use when you need a group-level stat attached back onto every original row — e.g., `df['pct_of_region_total'] = df['sales'] / df.groupby('region')['sales'].transform('sum')`. This is the idiomatic way to do "each row's value as a percentage of its group" — no merge required.
- **`apply()`** — the general escape hatch, can return anything (scalar, Series, DataFrame), but it's slower and less predictable in output shape. Use it only when `agg`/`transform` genuinely can't express what you need.

If you reach for `apply()` first out of habit, stop and check whether `agg()` or `transform()` actually covers it — they will, most of the time, and they're faster.

---

## 5. NumPy Underneath — Why pandas Is Fast (When Used Right)
A pandas Series is a wrapper around a NumPy `ndarray` (for numeric dtypes) plus an Index. Every vectorized pandas operation you've been doing since Day 1 (`df['col'] * 2`, boolean masks, `.sum()`) is ultimately calling into NumPy's compiled C code — operating on the whole underlying array at once instead of looping in Python. This is *why* vectorization was fast in yesterday's `%timeit` comparison.

Practical NumPy today:
- **`.to_numpy()`** (preferred) or `.values` (older, avoid in new code — ambiguous with extension dtypes) — pulls the raw array out of a Series/DataFrame when you need pure NumPy operations or are feeding data into a library that expects arrays (scikit-learn, for instance).
- **Broadcasting** — NumPy (and pandas by extension) applies an operation between a scalar and an array, or between arrays of compatible shapes, without an explicit loop: `arr * 2`, `arr_a + arr_b`. Understand this now — it's the same mechanism letting `df['a'] - df['b']` work column-wise without you writing a loop.
- **`np.where()`** — a vectorized if/else: `np.where(df['sales'] > 1000, 'high', 'low')`. Faster and more idiomatic than `.apply()` with a lambda for simple conditional column creation.
- **NaN-aware functions** — `np.nansum()`, `np.nanmean()`, etc. — pandas' own `.sum()`/`.mean()` already skip NaN by default, but know these exist for raw NumPy array work.

---

## 6. Why This Matters for the dbt Comparison
Almost every dbt mart/reporting model is, structurally, a `GROUP BY` with aggregations — the SQL equivalent of everything above. When you write `agg()` with named aggregations today, you're writing the same logic a mart model's SQL expresses, just in pandas. Keep track of any aggregation you build today that resembles a metric you've seen defined in a dbt model (total sales by region, average discount by category, etc.) — you'll draw that connection explicitly in Day 5.

---

## What Success Looks Like Today
By the end of Day 3, you should be able to:
- Explain split-apply-combine without hesitating
- Write multi-column named aggregations fluently
- Correctly choose between `agg()`, `transform()`, and `apply()` for a given task
- Explain why vectorized pandas operations are fast, in terms of NumPy underneath
- Use `.to_numpy()`, broadcasting, and `np.where()` appropriately