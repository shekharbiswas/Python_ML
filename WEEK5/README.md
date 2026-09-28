# Week 5 — End-to-End ML Pipelines

## Week Goal
Week 4 answered: *how do I make a model better, and know the improvement is real?* This week asks the next question: **how do I turn a model into a repeatable pipeline that anyone can run from raw data to saved results with one command?** The week is one continuous project on real NYC taxi data, enriched with external datasets (weather, holidays), covering data ingestion, cleaning, feature engineering, training, evaluation, model persistence, and pipeline reliability (logging, error handling, data checks).

> ## ⏰ Hard deadline: everything is finished within these 5 days
> **Days 1-4 are full project work. Day 5 is presentation prep only. The presentation is on Friday at 15:30.** All modeling and pipeline work is frozen at the end of Day 4. Week 6 is a **student-chosen project**, so this week must leave you with a pipeline you can reuse as a starter template for it.

**Key theme to keep in mind throughout the week:** *"A model that only runs in your notebook, once, on your machine, is not finished. A pipeline is finished when someone else can run it from scratch and get the same result."*

---

## Dataset Used
**One project, one dataset:** [NYC TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page), **Yellow Taxi trips**, published monthly as Parquet files. This is real operational data: a few million trips per month, with pickup/drop-off times and zones, distances, itemized fares, and payment types. It is not a curated teaching dataset, so expect messy rows (zero-distance trips, negative fares, impossible timestamps).

Use a fixed six-month window (for example, **January-June 2025**) so everyone works on the same data. Note that 2025 files include a new `cbd_congestion_fee` column, a real-world schema change you must handle rather than ignore.

**The ML problem:** predict **hourly taxi pickup demand per pickup zone** (regression). Raw trips are aggregated into a zone × hour table (hundreds of thousands to over a million rows), so it is a real modeling problem at real scale. The primary metric is **MAE**, with RMSE as a secondary check, and every model is compared against a naive baseline ("same zone, same hour, last week").

### External datasets (joined into the project)
| Dataset | Source | Use | Status |
|---|---|---|---|
| **Taxi Zone Lookup Table** | TLC page (CSV) | Map zone IDs to borough and zone name | Required |
| **Hourly weather** (temperature, precipitation, snowfall, wind) | Open-Meteo historical API or NOAA hourly data for NYC | Weather drives taxi demand | Required |
| **US / NY holidays** | Python `holidays` package | Holiday effect on demand | Required |
| **NYC special events** | NYC Open Data (permitted events) | Large events change local demand | Optional stretch |
| **MTA subway hourly ridership** | NY Open Data | Subway vs. taxi demand | Optional stretch |

Each external source must be joined on a clearly defined key (`pickup_hour`, `zone_id`, `date`), with join row counts checked so a bad join is caught immediately.

---

## Week Structure

| Day | Focus | Real-World Question It Answers |
|-----|-------|----------------------------------|
| 1 | Problem framing, data ingestion, external data, data-quality audit | "How do I get raw data from several sources into one trustworthy table, reproducibly?" |
| 2 | Cleaning and feature engineering as pipeline stages, time-based split, baselines | "How do I structure prep code so it's modular, leak-free, and rerunnable?" |
| 3 | Training, time-aware cross-validation, tuning, evaluation, model persistence | "How do I train and evaluate honestly on time-ordered data, and save the model so it can be reused?" |
| 4 | Productionizing: one-command pipeline, config, logging, error handling, data checks, final test evaluation | "Can someone else run my project from scratch and get the same result?" |
| 5 | Presentation prep (**presentation Friday at 15:30**) | "Can I explain what I built, what I chose, and why, in a short, honest presentation?" |

---

## Approach This Week (Advanced Pace)
1. Minimal syntax hand-holding: scikit-learn pipelines, `joblib`, and Parquet are introduced once, by use case
2. Every day is applied to the **same project**, so each day's output is the next day's input
3. **Stage separation:** ingest → clean → features → train → evaluate → save, each a function or module, not one long notebook
4. **Time-based split:** train on earlier months, validate on a later month, and lock the final month as the test set (used exactly once, on Day 4). Random splits are not allowed on time-ordered data
5. **Leakage is flagged explicitly:** lag features built from future rows, weather joined at the wrong hour, scaling fitted before the split
6. **Reliability is part of the work:** logging, clear error messages, and data validation checks (row counts, null rates, value ranges) between stages
7. A running **decision log** captures every choice and its evidence, so the Day 5 presentation is assembled, not reconstructed
8. The final repo is built to be **reused as a template for the Week 6 project**

---

## 📌 Project 2 (Week 5)
An end-to-end pipeline on NYC yellow taxi data with external weather and holiday data.

**Requirements:**
1. Problem framed as a business decision (for example, "where should drivers be positioned each hour?") with a justified primary metric
2. Raw TLC Parquet files, the zone lookup table, weather, and holiday data ingested and joined, with a data-quality audit
3. Cleaning rules documented and applied in code, including handling of the 2025 `cbd_congestion_fee` schema change
4. Aggregation to a zone × hour table, with time, lag, weather, and holiday features built as pipeline stages
5. A time-based train / validation / locked-test split
6. A naive baseline and at least two models compared with time-aware cross-validation, reported as mean ± std
7. The trained pipeline saved with `joblib`, plus a separate script that loads it and predicts on new data
8. A single command (`python run_pipeline.py`) that runs everything from raw data to saved results, with a config file, logging, and validation checks
9. Final model evaluated **once** on the locked test set, with error analysis by zone, hour, and weather condition
10. A short written summary and a presentation covering the results, the choices made, and why

---

## Deliverable by End of Week 5
1. A GitHub-ready repo with this structure:
   ```
   project/
   ├── config.yaml
   ├── run_pipeline.py
   ├── src/           (ingest, clean, features, train, evaluate)
   ├── notebooks/     (exploration only)
   ├── models/        (saved model)
   ├── results/       (tables, figures, predictions)
   └── README.md      (how to run it)
   ```
2. A decision log recording each choice, the alternatives, and the evidence
3. A short written results summary, including limitations
4. A presentation delivered **Friday at 15:30**

This mirrors real ML engineering: a model isn't finished when it scores well, it's finished when it can be rerun, checked, and handed to someone else.

---
*See `Week5_Day1_Concepts.md` / `Week5_Day1_Tasks.md` through `Week5_Day5_Concepts.md` / `Week5_Day5_Tasks.md` for detailed daily breakdowns.*