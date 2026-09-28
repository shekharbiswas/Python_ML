# Week 5 — Day 2: Cleaning and Feature Engineering as Pipeline Stages

## Day Goal
Yesterday you ingested and audited the data. Today you turn that into a **model-ready table**, built entirely by pipeline code: cleaning rules applied and logged, trips aggregated to a complete zone × hour table, external data joined, features built without leakage, and a time-based split with a baseline score. You still don't pick or tune a model today.

**Key theme for today:** *"Every row that disappears and every feature that is created must be explainable, logged, and rerunnable."*

**Reminder:** Days 1-4 are project work, Day 5 is presentation prep, and the presentation is **Friday at 15:30**.

---

## Concept 1: Cleaning Rules as Code
Yesterday's audit produced numbers and proposed rules. Today each rule becomes **one small function** in `src/clean.py`:

```
drop_outside_window(df) → df
drop_bad_duration(df) → df
drop_nonpositive_fares(df) → df
...
```

Rules for a good cleaning stage:
- **One function per rule**, named for what it removes
- **Log how many rows each rule removed** (and the percentage)
- Save a **cleaning report** (`results/cleaning_report.csv`) so anyone can see what was removed and why
- Thresholds (for example, max trip duration) live in `config.yaml`, not in the code
- Never modify `data/raw/` or `data/interim/`. The output goes to `data/processed/`

If the cleaning report says 40% of rows were removed, something is wrong with a rule or with the data. Fail loudly and investigate.

---

## Concept 2: Aggregation to a Complete Grid
Your target is the number of pickups per (zone, hour). A `groupby` on the trips gives you only the zone-hours that **had at least one trip**. Zone-hours with zero pickups simply don't appear, and that is a serious problem: the model would never see a quiet hour.

The fix is to build the **complete grid first**: every valid zone × every hour in the window, then left-join the counts and fill missing counts with **0**.

```
grid rows = number of zones × number of hours
```

For about 263 zones and a 181-day window (4,344 hours), that is roughly 1.14 million rows. This is your real dataset size.

**Validate:** no duplicate (zone, hour) pairs, and the row count equals zones × hours exactly.

---

## Concept 3: Joining External Data (Now at the Right Grain)
Yesterday you planned the joins. Today you perform them on the zone-hour table:

| Join | Key | Expected row count after join |
|---|---|---|
| Zone lookup (borough, zone name) | `zone_id` | Unchanged |
| Weather | `pickup_hour` | Unchanged |
| Holidays | `date` (derived from the hour) | Unchanged |

Rule: **the row count after each join must equal the row count before**. If it changes, you have duplicate keys on the right side. Assert this in code.

---

## Concept 4: Feature Engineering Without Leakage
Two kinds of preprocessing exist, and they live in different places:

| Kind | Examples | Where it goes |
|---|---|---|
| **Stateless** (each row is computed from the data itself, no "learning") | hour, day of week, is_weekend, is_holiday, lags | The **features stage** (today) |
| **Stateful** (learns from data, then applies) | scaling, encoding, imputation | An sklearn `Pipeline` fitted on training data only (Day 3) |

**Features for today:**
- **Calendar:** hour, day of week, month, is_weekend, is_holiday
- **Weather:** temperature, precipitation, snowfall, wind (already joined)
- **Zone:** zone ID and borough (encoded on Day 3)
- **Lag features:** demand in the same zone 1 hour ago, 24 hours ago, and 168 hours ago (same hour last week)
- **Rolling feature:** mean demand over the previous 24 hours

**The information rule from Day 1:** only use information available *before* the hour being predicted.
- Lags must be computed **within each zone** (sort by zone, then time, then `shift`). A shift across zone boundaries leaks one zone's data into another
- A rolling mean must **shift first**, or it includes the current hour, which is the answer you are trying to predict
- The first rows of each zone have no history, so their lags are missing. Decide once (drop the first 168 hours, for example) and log it

---

## Concept 5: The Time-Based Split
Taxi demand is a time series. A random split lets the model see the future, so **random splits are not allowed this week.**

| Set | Period (for a Jan-Jun window) | Use |
|---|---|---|
| Train | January-April | Fit the model |
| Validation | May | Compare choices, tune |
| **Test (locked)** | June | Used **once**, on Day 4 |

Because lags only look backward, you can compute them on the full table before splitting. Anything stateful, like scaling, must be fitted on train only (Day 3).

Save the three sets to `data/processed/` as separate files. **The test file is not opened again until Day 4.**

---

## Concept 6: Baselines Come Before Models
A baseline tells you how hard the problem is and what "good" means. Today's baseline needs no learning:

> **Predict this hour's demand = demand in the same zone, same hour, one week ago** (`lag_168`)

Compute its **MAE on the validation set** and record it in the decision log. Every model later this week must beat this number to justify its existence. Because model choice is not the focus this week, a strong, simple baseline plus one sensible model is enough.

---

## Concept 7: Checks After Every Stage
Add validation checks to the stages you build today:

| After | Check |
|---|---|
| Clean | Rows removed are logged; removed share is plausible; no nulls in key columns |
| Grid | Rows = zones × hours; no duplicate (zone, hour); counts ≥ 0 |
| Joins | Row count unchanged; count of missing weather values = 0 |
| Features | Only the expected lag warm-up rows have missing values |
| Split | No time overlap between train, validation, and test; test month matches config |

---

## Common Mistakes to Avoid
1. Aggregating with `groupby` and forgetting the zero-demand zone-hours
2. Computing lags without grouping by zone
3. A rolling mean that includes the current hour
4. Joining weather at the wrong hour or time zone
5. Silently dropping rows with no log
6. Using a random split on time-ordered data
7. Looking at the test month "just to see"

---

## Key Takeaways
1. Cleaning rules are functions with logged, reported row counts
2. Build the full zone × hour grid first, then fill zeros
3. Joins must never change the row count
4. Stateless features go in the features stage, and stateful ones go in a fitted `Pipeline`
5. Lags must be grouped by zone and shifted so they respect the information rule
6. Split by time and lock the test set
7. A naive baseline is the number every later step has to beat

---
*Next: `Week5_Day2_Tasks.md`*