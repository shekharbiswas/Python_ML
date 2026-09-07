# Week 2 — Data Wrangling with pandas

## Week Goal
Week 1 got Python fundamentals and a working Snowflake connection in place — you can already pull a raw table out of Snowflake into a script. This week is about what happens the moment that data lands: **pandas** becomes the primary tool for inspecting, cleaning, reshaping, and summarizing it, with NumPy underneath and matplotlib/seaborn for a first look at what the data actually shows.

The throughline for the week is deliberately tied to dbt: every exercise pulls a **raw** Snowflake table into pandas, and you reproduce — by hand, in code — some of what a dbt staging model does automatically. By Friday, you're not just competent in pandas; you can point at a dbt model and say exactly what transformation step it's doing and why.

**Key theme to keep in mind throughout the week:** *"dbt does this transformation for you in production. This week, you do it by hand in pandas — so you understand exactly what dbt is doing, not just that it works."*

This week moves at a working-engineer pace: idiomatic pandas (vectorized operations, method chaining, `.loc`/`.iloc` used correctly), not just "a way that works." Expect performance/anti-pattern traps (`iterrows()`, chained indexing, `SettingWithCopyWarning`) to be called out as they come up rather than avoided.

---

## Dataset Used
Raw Snowflake tables pulled live via the `snowflake-connector-python` setup from Week 1 — not static CSVs. Each day's exercises query a raw source table and, where relevant, sit it side-by-side with the corresponding **dbt staging model** output, so you're always comparing "before" and "after" on real data rather than a toy dataset.

---

## Week Structure

| Day | Focus | Real-World Question It Answers |
|-----|-------|----------------------------------|
| 1 | pandas fundamentals — DataFrames/Series, indexing, filtering, sorting | "How do I load a raw Snowflake table into pandas and actually work with it efficiently?" |
| 2 | Missing data, merging & joining datasets | "How do I combine and clean data pulled from multiple Snowflake tables?" |
| 3 | GroupBy & aggregation, NumPy array basics | "How do I reproduce SQL-style `GROUP BY` logic in Python, and where does NumPy fit underneath pandas?" |
| 4 | Exploratory Data Analysis (EDA) | "Given a raw table I've never seen, how do I quickly understand its shape, quality, and quirks?" |
| 5 | Visualization + raw vs. dbt staging model comparison (capstone) | "Can I prove, with code and charts, exactly what a dbt model transformed and why it matters?" |

---

## Approach This Week (Advanced Pace)
1. Minimal syntax hand-holding — a pandas method is introduced once, by use case, with the expectation it sticks; no repeated re-explanation of basics already covered in Week 1
2. Every exercise is framed against the dbt staging model comparison, not an abstract "clean this data" prompt
3. Idiomatic vs. non-idiomatic pandas gets flagged explicitly (vectorization over loops, `.loc` over chained indexing) — the goal is production-grade pandas, not just working pandas
4. One core new capability per day, but moving through it fast; remaining time goes to edge cases and real messiness (nulls, duplicate keys, type mismatches) rather than clean toy examples
5. Each day's exercises pull live from Snowflake — no pre-cleaned local files
6. The week ends with a real analytical deliverable, not just a working script

---

## Deliverable by End of Week 2
A Python analysis (script or notebook) that:
1. Pulls a raw Snowflake source table directly into a pandas DataFrame
2. Cleans and handles missing/malformed data
3. Merges in at least one additional Snowflake table
4. Produces grouped/aggregated summary statistics
5. Includes at least one visualization highlighting a pattern or data quality issue
6. Directly compares the raw table's output against the corresponding dbt staging model's output, with a written explanation of what the dbt transformation changed and why it matters

This mirrors the actual first step of most real analytics work: before trusting a transformation layer, know how to verify it yourself.

---
*See `Week2_Day1_Concepts.md` / `Week2_Day1_Tasks.md` through `Week2_Day5_Concepts.md` / `Week2_Day5_Tasks.md` for detailed daily breakdowns.*