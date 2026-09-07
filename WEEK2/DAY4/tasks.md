# Week 2, Day 4 — Tasks
## Exploratory Data Analysis (6-Hour Session)

Read today's Concepts doc first. Today deliberately uses a **new raw Snowflake table** — one you haven't touched in Days 1–3. The point of the day is practicing the *process* of getting oriented fast, which only works if the table is genuinely unfamiliar to you going in. Pick any raw source table you haven't queried yet this week.

---

## Block 1 (Hour 1) — Shape & Schema, First Pass
1. Load the new table via `fetch_pandas_all()`.
2. Run `.shape`, `.dtypes`, `.head()`, and `.sample(10)` — in that order.
3. Before looking anything up or asking anyone, write a quick guess (2–3 sentences) of what this table represents and what a row means, based only on column names and the sample.

**Note down:** your initial guess, so you can check it against what you actually find by the end of the day.

---

## Block 2 (Hour 2) — Distributions
1. Run `df.describe()` on numeric columns and `df.describe(include='object')` on categorical ones.
2. For each numeric column, sanity check min/max against what you'd expect — flag anything that looks implausible (negative values where they shouldn't exist, a max that looks like an error code rather than real data, etc.).
3. For each categorical column, check the `unique` count against your assumption of whether it's a true category or an identifier.

**Note down:** at least one column that surprised you — a range, a cardinality, or a value that didn't match your Block 1 guess.

---

## Block 3 (Hour 3) — Data Quality
1. Run full missingness checks (from Day 2: `isna()`, plus a placeholder-value check on string columns).
2. Check for fully duplicate rows (`df.duplicated().sum()`) and, if there's a plausible ID column, duplicate keys specifically.
3. For every categorical column, run `.nunique()` and decide: real category, identifier, or free text mislabeled as categorical?

**Note down:** a short data-quality summary — one line per issue found (missingness, duplicates, suspicious cardinality) — like a bug list.

---

## Block 4 (Hour 4) — Relationships
1. Run `df.corr(numeric_only=True)` and identify the strongest and weakest numeric relationships.
2. Pick one numeric column and one categorical column and run `df.groupby('cat_col')['num_col'].describe()` — does the distribution genuinely differ across categories, or is it roughly the same everywhere?
3. Pick one pair of numeric columns with a **low** correlation coefficient and inspect them further in Block 5's scatter plot before assuming "no relationship" — set this up as a check on Phase 4's limits from today's Concepts doc.

---

## Block 5 (Hour 5) — Visual Confirmation
1. Plot a histogram for at least 2 numeric columns — look for skew or multi-modal shapes.
2. Plot a boxplot for a numeric column split by a categorical column — check whether outliers cluster in a specific group.
3. Plot the low-correlation pair you flagged in Block 4 as a scatter plot — did the plot confirm "genuinely no relationship," or reveal a non-linear pattern the correlation coefficient missed?

**Note down:** whether any plot changed a conclusion you'd drawn from the numbers alone in Blocks 2–4.

---

## Block 6 (Hour 6) — Capstone: EDA Write-Up
Produce a short written EDA summary (markdown cell, or separate doc) covering:
1. **What this table is** — your revised understanding, compared against your Block 1 guess.
2. **Data quality issues found** — the bug list from Block 3, prioritized by severity.
3. **Notable relationships** — anything from Block 4/5 worth flagging to a teammate before they build on this table.
4. **Recommendation** — for each data quality issue, is it something a dbt staging model should handle before this table is used downstream, or something to flag back to the source system as a genuine upstream problem?

This write-up should be something you could hand directly to a teammate who's about to build on this table for the first time.

---

## End-of-Day Checklist
- [ ] Ran a full shape/schema/distribution/quality/relationship pass on a genuinely new table
- [ ] Read `.describe()` output critically and flagged implausible values
- [ ] Correctly distinguished true categorical columns from identifiers using cardinality
- [ ] Found at least one case where a plot changed or confirmed a numeric-only conclusion
- [ ] Produced a written EDA summary with a clear staging-model-vs-upstream-issue recommendation