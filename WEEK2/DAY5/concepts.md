# Week 2, Day 5 — Concepts
## Visualization & the Raw-vs-dbt Capstone

---

## Question of the Day
**"Can I prove, with code and charts, exactly what a dbt model transformed and why it matters?"**

Today has two parts: leveling up your visualization from Day 4's fast/disposable plots to something you'd actually put in front of someone, and assembling the whole week — cleaning, merging, aggregating — into one deliverable that proves what a dbt staging model actually does. This is the day everything from Monday through Thursday gets used for real, not practiced in isolation.

---

## 1. Disposable vs. Presentation Plots — What Actually Changes
Day 4's plots existed to inform *you*. Today's exist to inform someone else, so the bar changes:
- **Every axis is labeled**, with units where relevant — not the raw column name as-is.
- **Every plot has a title that states the finding**, not just the variable names (`"Discount rates spike above 40% in the West region"` beats `"Discount by Region"`).
- **Default matplotlib/seaborn styling is a starting point, not a finish line** — at minimum, set a reasonable figure size (`figsize=`) and remove chart junk (unnecessary gridlines, redundant legends).
- **One idea per chart.** Resist cramming multiple metrics into one plot just because you can — a second chart is cheaper than a confusing one.

`seaborn` over raw `matplotlib` by default — better statistical defaults (confidence intervals, sensible color palettes), less boilerplate. Drop to raw `matplotlib` when you need fine control seaborn doesn't expose (custom annotations, precise subplot layouts).

---

## 2. Choosing the Right Chart for a Comparison Story
Today's charts specifically need to show **before vs. after** (raw vs. dbt staging). A few patterns that work well for that:
- **Side-by-side bar charts** — same metric, two bars per category (raw vs. staging), when comparing counts or sums across groups.
- **Overlaid histograms** (`sns.histplot` with `hue=` or two calls with `alpha=`) — when showing a distribution shift (e.g., how a numeric column's spread changed after dbt's cleaning).
- **A simple before/after row-count or null-count bar chart** — often the most convincing chart in the whole deliverable, because it's immediately legible to a non-technical reviewer.

Avoid pie charts for this kind of comparison — they're hard to compare precisely across two datasets. Bars beat pies almost every time you're comparing magnitudes.

---

## 3. The Comparison Framework — Make It Systematic, Not Anecdotal
You've been doing pieces of this all week (Day 1's shape/dtype check, Day 2's row-count discipline). Today, formalize it into one repeatable comparison, run on the full raw table vs. its dbt staging model output:

| Dimension | What to check | What it reveals |
|---|---|---|
| Shape | `df_raw.shape` vs `df_staging.shape` | Rows filtered/deduped, columns added/dropped |
| Dtypes | `df_raw.dtypes` vs `df_staging.dtypes` | Type casting/standardization dbt applied |
| Nulls | `isna().sum()` on both | What dbt coalesced, defaulted, or filtered out |
| Duplicates | `duplicated()` on both, by key | Whether dbt deduped, and on what key |
| Cardinality | `.nunique()` on shared categorical columns | Value standardization (e.g., `'west'`/`'West'` collapsed) |
| Aggregates | Sum/mean of key numeric columns on both | Whether business totals shifted, and by how much |

Every row in this table should turn into either a chart or a written finding in your final deliverable — this is the actual backbone of the capstone, not an afterthought after you've made some pretty charts.

---

## 4. Writing the Comparison Up
A finding without a "why it matters" isn't done. For each difference you find between raw and staging, write it as: **what changed → likely reason dbt does this → what would break downstream if it didn't.** For example: *"Row count dropped 3% in staging → dbt deduplicates on order_id → without this, revenue aggregates downstream would be inflated by duplicate orders."* This is the sentence structure a real data engineer uses when documenting a model, and it's what turns today's work from an exercise into something genuinely useful.

---

## 5. Assembling the Final Deliverable
Today isn't new isolated exercises — it's pulling Monday through Thursday's pieces into the single deliverable the week has been building toward: pull raw → clean → merge → aggregate → visualize → compare against dbt staging → write it up. Reuse the `clean_and_merge()` function from Day 2 and the named aggregations from Day 3 directly; don't rebuild them from scratch.

---

## What Success Looks Like Today
By the end of Day 5, you should have:
- At least one polished, presentation-ready visualization illustrating a specific raw-vs-staging difference
- A completed systematic comparison across shape, dtypes, nulls, duplicates, cardinality, and aggregates
- A written explanation, per finding, following the what/why/impact structure
- One consolidated script or notebook representing the full Week 2 deliverable, built from the pieces you've already written, not from scratch