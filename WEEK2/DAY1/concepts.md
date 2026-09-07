# Week 2, Day 1 — Concepts
## pandas Fundamentals: DataFrames, Indexing, Filtering, Sorting



[![Watch the video](https://img.youtube.com/vi/858zyLzpsnE/0.jpg)](https://www.youtube.com/watch?v=858zyLzpsnE)



## Question of the Day
**"How do I load a raw Snowflake table into pandas and actually work with it efficiently?"**

You already know how to run a query against Snowflake from Week 1. Today's shift: instead of iterating over `cursor.fetchall()` rows in a loop, everything lands in a **DataFrame**, and virtually everything you do to it should be vectorized — operating on whole columns at once, not row by row. That mental shift is the actual point of today, more than any individual method.

---

## 1. DataFrame vs. Series — Not Two Separate APIs
A **DataFrame** is a 2D table; a **Series** is a single column (or row) — and critically, a Series is what you get back the moment you slice a single column out of a DataFrame. Almost every DataFrame method has a Series equivalent, because a DataFrame is really a dict-like collection of Series sharing a common index. Internalize this now — it explains why `df['col'].sort_values()` and `df.sort_values('col')` both exist and behave differently (one reorders a Series, the other reorders the whole table by that column).

**The Index is not just a row counter.** Every DataFrame and Series has an Index object, and a lot of pandas' power (alignment during joins/arithmetic, fast `.loc` lookups) comes from that Index being meaningful — not just `0, 1, 2, ...`. Keep this in mind for Day 2's joins.

---

## 2. Getting Snowflake Data Into pandas — Two Paths
You have two options when pulling from `snowflake-connector-python`:

1. **Manual construction** — `pd.DataFrame(cursor.fetchall(), columns=[c[0] for c in cursor.description])`. Works everywhere, but you lose Snowflake's native type inference.
2. **`cursor.fetch_pandas_all()`** — requires the `pyarrow` extra (`pip install "snowflake-connector-python[pandas]"`), but hands you back a DataFrame with far better dtype fidelity (proper `int64`, `datetime64`, `float64` instead of everything coming back as `object`).

Use option 2 as your default from today onward. Option 1 is worth knowing because you'll hit it in older codebases or minimal environments.

---

## 3. Indexing — Get `.loc` and `.iloc` Right, Permanently
This is the single most common source of subtle bugs in production pandas code, so we're being precise about it today rather than glossing over it:

- **`.loc[]`** — label-based. Selects by index/column *labels*. Slices are **inclusive** on both ends (`df.loc[2:5]` includes row label 5 — unlike Python's normal slicing).
- **`.iloc[]`** — position-based. Selects by integer *position*, exactly like a Python list/array. Slices are **exclusive** on the end, as usual.
- **Plain bracket indexing (`df['col']`, `df[df.x > 5]`)** — fine for column selection and boolean filtering, but avoid using it for anything involving row+column selection together. That's where chained indexing bugs and `SettingWithCopyWarning` come from.

**Chained indexing is the trap to internalize today:**
```python
df[df.region == 'West']['sales'] = 0   # DON'T — may silently fail to modify df
df.loc[df.region == 'West', 'sales'] = 0   # DO — single, unambiguous operation
```
The first line creates an intermediate, possibly-copy DataFrame and assigns into *that* — pandas can't guarantee the original `df` gets updated, and will warn you (`SettingWithCopyWarning`). The fix is always to do the row-and-column selection as one `.loc` call.

---

## 4. Filtering — Boolean Masks, Not Loops
Filtering in pandas means building a boolean Series (a "mask") and using it to select rows:
```python
mask = (df['sales'] > 1000) & (df['region'] == 'West')
df.loc[mask]
```
Two things that trip people up coming from plain Python:
- Use `&`, `|`, `~` — not `and`, `or`, `not`. Python's boolean operators don't vectorize; pandas' bitwise operators do.
- **Always wrap each condition in parentheses** — operator precedence means `df.a > 1 & df.b < 2` parses wrong without them.

`.query()` is worth knowing as an alternative for readability on complex conditions (`df.query("sales > 1000 and region == 'West'")`), but boolean masks are what you'll use most and what you need to be fluent in for merges and joins tomorrow.

---

## 5. Sorting — `sort_values` vs. `sort_index`
- `df.sort_values('col')` — sorts rows by a column's values. Supports multiple columns (`['col1', 'col2']`) and mixed ascending/descending (`ascending=[True, False]`).
- `df.sort_index()` — sorts by the Index itself, not the data. Useful after operations (like groupby, covered Day 3) that can leave the index out of order.
- Both return a **new** DataFrame by default — pass `inplace=True` if you genuinely want to mutate in place, but prefer method chaining over `inplace` in real pipelines; it composes better and is easier to debug.

---

## 6. Why This Matters for the dbt Comparison
Every dbt staging model you've seen so far exists because someone had to decide: what index/keys matter, what rows to filter out, what order to normalize things into. Today you're doing that same reasoning by hand — which columns actually identify a row uniquely, which filters represent "valid" data versus noise. Keep a mental note through today's exercises of anything you clean or filter that *feels like it should be a dbt model's job* — you'll revisit that instinct explicitly on Day 5.

---

## What Success Looks Like Today
By the end of Day 1, you should be able to:
- Pull a raw Snowflake table into a DataFrame using `fetch_pandas_all()`
- Explain, without hesitating, when to use `.loc` vs `.iloc` vs plain brackets
- Write a multi-condition boolean filter correctly on the first try
- Sort a DataFrame by multiple columns with mixed sort order
- Recognize chained indexing in code and rewrite it as a single `.loc` call