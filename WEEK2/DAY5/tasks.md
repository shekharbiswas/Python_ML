# Week 2, Day 5 — Tasks
## Capstone: Visualization + Raw-vs-dbt Comparison (6-Hour Session)

Read today's Concepts doc first. This is the week's deliverable day — you're assembling and extending work from Days 1–3, not starting fresh. Use your primary table from Days 1–3 (not the Day 4 exploration table) as the subject of today's comparison, since you already have a `clean_and_merge()` function and named aggregations built for it.

---

## Block 1 (Hour 1) — Assemble the Pipeline
1. Pull your raw table(s) fresh via `fetch_pandas_all()`.
2. Run your `clean_and_merge()` function from Day 2 on it.
3. Run your named-aggregation summary from Day 3 on the cleaned/merged result.
4. Confirm the whole chain runs end-to-end without manual intervention — this is your pipeline's backbone for the rest of the day.

---

## Block 2 (Hour 2) — Pull the dbt Staging Comparison Data
1. Identify (or reconfirm from Day 1/2) the dbt staging model corresponding to your raw table.
2. Pull its output into `df_staging`.
3. If your pipeline also involves a joined second table, pull the corresponding downstream dbt model output for that too, if one exists.

---

## Block 3 (Hour 3) — Run the Systematic Comparison
Using today's Concepts doc comparison table as your checklist, run and record each of the following, comparing raw vs. staging:
1. Shape (`df_raw.shape` vs `df_staging.shape`)
2. Dtypes
3. Null counts per column
4. Duplicate row/key counts
5. Cardinality of shared categorical columns
6. Sum/mean of your key numeric metric(s) from Day 3's aggregation

**Note down:** fill in the comparison table from the Concepts doc with your actual numbers for each dimension.

---

## Block 4 (Hour 4) — Build the Presentation Visualizations
Produce at least 3 polished (labeled, titled, sized properly) charts illustrating specific findings from Block 3. At minimum, include:
1. A before/after bar chart of row counts or null counts (raw vs. staging).
2. A distribution comparison (overlaid or side-by-side histogram) for a numeric column that changed meaningfully.
3. One chart of your choice illustrating whichever Block 3 finding you think is most significant.

Every chart needs a title that states the finding, not just the variable names — apply today's Concepts doc standard, not Day 4's disposable-plot standard.

---

## Block 5 (Hour 5) — Write the Findings
For every meaningful difference found in Block 3, write it using the what/why/impact structure from today's Concepts doc:
> *What changed → likely reason dbt does this → what would break downstream if it didn't.*

Aim for at least 4–5 findings written this way. Reference the specific chart (from Block 4) or number (from Block 3) that supports each finding — don't state a finding without backing it with something concrete you produced today.

---

## Block 6 (Hour 6) — Final Assembly
Consolidate everything into one script or notebook that represents your full Week 2 deliverable:
1. Raw pull → `clean_and_merge()` → named aggregation (Blocks 1)
2. Systematic raw-vs-staging comparison (Block 3)
3. At least 3 presentation-quality visualizations (Block 4)
4. Written findings, what/why/impact structure (Block 5)
5. A short closing summary (3–5 sentences): looking back across the whole week, what's your overall takeaway on the value dbt's staging layer adds for this specific table?

This file is your actual Week 2 deliverable — it should be something you could hand to a manager or show in an interview as evidence you can verify a transformation layer, not just use one.

---

## End-of-Day Checklist
- [ ] Reused (not rebuilt) `clean_and_merge()` and named aggregations from earlier in the week
- [ ] Completed the full systematic raw-vs-staging comparison table
- [ ] Produced at least 3 properly labeled, titled, presentation-quality charts
- [ ] Wrote at least 4–5 findings using the what/why/impact structure, each backed by a specific chart or number
- [ ] Assembled everything into one consolidated deliverable with a closing summary