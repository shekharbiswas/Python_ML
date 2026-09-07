# Week 2, Day 4 — Concepts
## Exploratory Data Analysis (EDA)

---

## Question of the Day
**"Given a raw table I've never seen, how do I quickly understand its shape, quality, and quirks?"**

Everything up to now used a table you already knew well. Today's exercise deliberately uses a **new** raw table you haven't touched yet — because the actual skill being built is a repeatable process for getting oriented fast, not familiarity with one dataset. By the end of today you should have a mental checklist you run on autopilot the moment a new table lands in front of you.

---

## 1. EDA Is a Sequence, Not a Grab-Bag
Random exploration wastes time. Work through these phases in order — each one tells you what to look for in the next:

1. **Shape & schema** — what am I even looking at?
2. **Distributions** — what does "normal" look like for each column?
3. **Data quality** — nulls, duplicates, cardinality, obvious errors
4. **Relationships** — how do columns relate to each other?
5. **Visual confirmation** — does a plot confirm or contradict what the numbers suggested?

Skipping straight to visualization before you've checked shape/dtypes/nulls is the most common wasted-effort mistake — you'll build a chart on a column that turns out to be 40% garbage.

---

## 2. Phase 1 — Shape & Schema
```python
df.shape
df.dtypes
df.head()
df.sample(5)      # more informative than head() alone — head() can hide issues that only show up later in the table
```
`.sample()` matters here: a raw table is often sorted or partitioned in a way that makes the first few rows unrepresentative. Always sample randomly, not just glance at the top.

---

## 3. Phase 2 — Distributions
```python
df.describe()                      # numeric columns: count, mean, std, min, quartiles, max
df.describe(include='object')      # categorical columns: count, unique, top, freq
```
Read `.describe()` critically, not just as a print statement:
- **min/max vs. domain knowledge** — does a negative value show up somewhere it shouldn't (negative quantity, negative age)? Does max look like a plausible business value or a data entry error?
- **std relative to mean** — a very high standard deviation relative to the mean is your first hint of outliers, before you've even plotted anything.
- **`unique` count for categoricals** — a column you assumed was low-cardinality (a handful of categories) turning out to have hundreds of unique values usually means it's either a free-text field mislabeled as categorical, or has real dirty/inconsistent values (`'West'` vs `'west'` vs `'West '`).

---

## 4. Phase 3 — Data Quality
By now you're fluent in missingness checks from Day 2 — apply them here too, but also add:
```python
df.duplicated().sum()                      # fully duplicate rows
df.duplicated(subset=['id_col']).sum()     # duplicate keys specifically
df['categorical_col'].nunique()             # cardinality check
```
**Cardinality drives your next decision.** A column with 3–20 unique values is a natural groupby dimension. A column with near-row-count-many unique values is probably an identifier, not a category — don't try to `groupby()` or one-hot-encode it.

---

## 5. Phase 4 — Relationships
```python
df.corr(numeric_only=True)          # correlation matrix, numeric columns only
df.groupby('cat_col')['num_col'].describe()   # how does a numeric column's distribution vary by category?
```
Correlation only captures **linear** relationships — a strong non-linear pattern can show a near-zero correlation coefficient. Don't conclude "no relationship" from `corr()` alone; that's what visualization in the next phase is for.

---

## 6. Phase 5 — Visual Confirmation
Three plot types cover most EDA needs — know when to reach for each:
- **Histogram** (`sns.histplot` or `df['col'].hist()`) — shape of a single numeric column's distribution. Look for multi-modal shapes (a hint the column secretly mixes two populations) and skew.
- **Boxplot** (`sns.boxplot`) — fast outlier detection, especially split by category (`sns.boxplot(x='cat_col', y='num_col', data=df)`) to see if outliers are concentrated in one group.
- **Scatter plot** (`sns.scatterplot`) — relationship between two numeric columns, and a visual sanity check against what `.corr()` reported.

Keep plots fast and disposable during EDA — default styling, no polishing. Polished, presentation-ready visuals are Day 5's job; today's plots exist to inform you, not to show anyone else.

---

## 7. Why This Matters for the dbt Comparison
EDA is exactly the diagnostic work someone did (or should have done) before writing the first line of a dbt staging model. Every filter, every coalesce, every dedup key in an existing dbt model is a decision that EDA like today's would have surfaced. When you hit something odd in today's raw table — a weird cardinality, an outlier cluster, a suspicious null pattern — ask yourself: is this already handled somewhere downstream in dbt, or is it a real, undocumented data quality issue?

---

## What Success Looks Like Today
By the end of Day 4, you should be able to:
- Run a fast, ordered EDA pass on a genuinely unfamiliar table without hesitating on where to start
- Read `.describe()` output critically, not just report it
- Use cardinality to correctly judge whether a column is categorical or an identifier
- Know when correlation is insufficient and a plot is required
- Produce fast, informative (not polished) histograms, boxplots, and scatter plots