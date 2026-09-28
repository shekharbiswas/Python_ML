# Week 5 — Day 1: Pipeline Foundations, Data Ingestion & the Data-Quality Audit

## Day Goal
Today you build the **front door of the pipeline**: a repeatable way to pull real NYC taxi data plus external data (weather, holidays, zone lookup) into a trustworthy, validated starting point. You will not train a model today. By the end of Day 1, one command should fetch, load, validate, and audit all data sources, and rerunning it should give the same result.

**Key theme for today:** *"Pipelines fail at the edges: bad inputs, silent schema changes, and broken joins. Build the checks first, and the modeling stays easy."*

> **Note on models this week:** model choice is deliberately **not** the focus of Week 5. The pipeline is the product. By Day 3 you will use one simple, sensible model, and it should be swappable through a config setting. Time spent on the pipeline pays off in Week 6, when you apply it to your own project.

**Reminder:** Days 1-4 are project work, Day 5 is presentation prep, and the presentation is **Friday at 15:30**. Everything is frozen at the end of Day 4.

---

## Concept 1: What Is a Pipeline?
A pipeline is a chain of **stages**, where each stage has a clear input, a clear output, and one job:

```
ingest → validate → clean → features → train → evaluate → save
```

Rules for a good stage:
- **One job:** ingest doesn't clean, and clean doesn't engineer features
- **Explicit contract:** it says what it reads and what it writes (for example, "reads `data/raw/`, writes `data/interim/`")
- **Rerunnable:** running it twice gives the same output, with no manual steps in between
- **Checked:** its output is validated before the next stage starts

A notebook that does everything top to bottom is fine for exploring. A pipeline is what you hand to someone else.

---

## Concept 2: Data Layers (Raw → Interim → Processed)
| Layer | Folder | Rule |
|---|---|---|
| **Raw** | `data/raw/` | Exactly as downloaded. **Never edited, never overwritten by code that changes content.** |
| **Interim** | `data/interim/` | Loaded, column-selected, type-fixed, validated. Still not cleaned of business-logic problems. |
| **Processed** | `data/processed/` | Cleaned, aggregated, feature-ready tables (Day 2 onward). |

Why it matters: if a cleaning rule turns out to be wrong, you rerun from raw. If raw was edited, you can't. Add `data/` to `.gitignore`. Data is not committed, but the code that recreates it is.

---

## Concept 3: Project Structure and Config-Driven Code
Hard-coded paths, months, and URLs scattered through code are the most common reason a project only runs on one laptop.

```
project/
├── config.yaml          # months, paths, URLs, columns to keep, weather settings
├── run_pipeline.py      # single entry point: python run_pipeline.py --stage ingest
├── requirements.txt
├── src/
│   ├── ingest.py
│   ├── validate.py
│   └── utils.py         # logging setup, config loader
├── data/{raw,interim,processed}/
├── results/
├── notebooks/           # exploration only, never required to run the pipeline
└── README.md
```

**Config-driven** means changing the month range means editing `config.yaml`, not the code.

---

## Concept 4: Reproducible (Idempotent) Ingestion
An ingestion step is **idempotent** if running it many times leaves you in the same state as running it once.

In practice:
- If the file is already in `data/raw/`, **skip the download** and log that you skipped it
- If a download fails, **raise a clear error** (which file, which URL). Don't continue with half the data
- Never leave a partially downloaded file that looks valid (download to a temp name, then rename)
- **Log** what happened. Use Python's `logging` module, not `print`, so messages carry timestamps and levels (INFO, WARNING, ERROR)

---

## Concept 5: Working with Parquet at Scale
The TLC publishes monthly **Parquet** files. Six months of yellow taxi trips is tens of millions of rows, which is too much to load carelessly.

- Parquet is **columnar**: you can read only the columns you need (`pd.read_parquet(path, columns=[...])`). This is called **column pruning** and it is your biggest memory saver
- Process **one monthly file at a time**, save each result to `data/interim/`, and combine only after reducing the data
- Parquet stores a **schema** (column names and types). Check it instead of assuming it
- Use appropriate dtypes (for example, zone IDs as integers, not floats or strings)

**Schema drift is real.** The TLC page notes a `cbd_congestion_fee` column added for 2025 data. Column names and casing can also differ between months (check the airport-fee column). Your pipeline should select columns by name, and a missing expected column should produce a clear error, not a silent wrong answer.

---

## Concept 6: Validation Checks ("Fail Loudly")
Between every stage, run cheap automatic checks. If a check fails, stop and say why.

| Check type | Example for this project |
|---|---|
| **Schema** | Expected columns exist; timestamp columns are datetimes |
| **Row counts** | Each monthly file has a plausible number of rows (not 0, not 10× the others) |
| **Null rates** | `PULocationID` and pickup time are never null |
| **Ranges** | Fares and distances aren't negative; pickup hour is within the config window |
| **Keys** | Every `PULocationID` exists in the zone lookup (or is a documented unknown) |
| **Coverage** | Weather has one row for every hour in the window |

A pipeline without checks doesn't fail. It just quietly produces wrong results.

---

## Concept 7: Joining External Data Safely
Each source has a different **grain** (the level of detail per row):

| Source | Grain | Join key |
|---|---|---|
| Trips | One row per trip | Aggregated to (zone, hour) on Day 2 |
| Zone lookup | One row per zone | `PULocationID` = `LocationID` |
| Weather | One row per hour | `pickup_hour` |
| Holidays | One row per date | `date` |

Rules for safe joins:
1. **Know the grain of both sides before joining.** Joining hourly weather to trip-level rows multiplies work. Aggregate first, then join
2. **Check row counts before and after.** A join that changes your row count unexpectedly means duplicate keys
3. **Count unmatched keys.** How many zone IDs had no lookup match? How many hours had no weather?
4. **Watch time zones and daylight saving.** TLC timestamps are NYC local time, and weather data must be requested in the same time zone. In March one local hour doesn't exist, and in November one occurs twice. Decide and document how you handle it

Today you **prepare and check** the joins. You perform the full join on Day 2.

---

## Concept 8: The Data-Quality Audit
Before writing cleaning code, **measure** the problems. An audit gives every cleaning rule a number behind it ("0.4% of rows have a negative fare"), so you can defend each decision.

Typical issues in TLC yellow taxi data:
- Pickup timestamps **outside the month** of the file, or outside your window
- Drop-off **before** pickup, or absurdly long or short trips
- **Zero or negative** distance, fare, or total amount
- Missing `passenger_count` or rate-code values
- Zone IDs **264 / 265** (unknown or outside NYC)
- Implausible outliers (a 500-mile trip, a $10,000 fare)

Today's rule: **audit only, don't delete.** Record each issue, its count, and its percentage. Then write a proposed cleaning rule in the decision log. You will apply the rules as code on Day 2.

---

## Concept 9: Framing the Prediction Problem
Decide these now, because every later stage depends on them. Write them in the decision log.

| Decision | Our default | Why it matters |
|---|---|---|
| **Grain** | One row per (pickup zone, hour) | Defines the aggregation |
| **Target** | Number of yellow taxi pickups in that zone-hour | What we predict |
| **Business question** | Where should drivers be positioned each hour? | Justifies the metric |
| **Metric** | MAE (RMSE secondary) | Errors in "trips" are easy to explain |
| **Baseline** | Same zone, same hour, last week | Any model must beat this |
| **Information rule** | Only use information available *before* the hour being predicted (past demand, plus weather treated as a known forecast) | Prevents leakage |

The information rule is the most important row. It decides which lag features are allowed on Day 2.

---

## Common Mistakes to Avoid
1. Loading every column of every file, then running out of memory
2. Hard-coding file paths and months inside functions
3. Editing or overwriting files in `data/raw/`
4. "Cleaning" during ingestion and losing track of what was removed
5. Joining before checking grain, key coverage, or row counts
6. Ignoring the time zone of the weather data
7. Using `print` instead of logging, so nobody can tell later what ran
8. Trusting a file because it downloaded without an error

---

## Key Takeaways
1. A pipeline is separate, checked, rerunnable stages, not one long script
2. Raw data is immutable, and everything after it is regenerated by code
3. Config-driven code lets anyone change months or paths without touching logic
4. Validation checks make failures visible and early
5. Know the grain of every source before joining
6. Audit first, then clean. Every cleaning rule needs a measured reason
7. Write down the prediction problem and the information rule before building features

---
*Next: `Week5_Day1_Tasks.md`*