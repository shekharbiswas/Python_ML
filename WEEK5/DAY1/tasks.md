# Week 5 — Day 1 Tasks: Ingest, Validate, Audit

**Goal for today:** one command (`python run_pipeline.py --stage ingest`) that fetches, loads, validates, and audits all four data sources, and gives the same result when rerun. **No modeling today.**

**Window:** use the same six months as the class (for example, January-June 2025). Set it in `config.yaml`, not in the code.

**Schedule reminder:** Days 1-4 are project work. Day 5 is presentation prep. The presentation is **Friday at 15:30**.

---

## Task 1: Set Up the Project (30 min)
1. Create the folder structure from the Concepts file (`src/`, `data/raw`, `data/interim`, `data/processed`, `results/`, `notebooks/`)
2. Create a virtual environment and a `requirements.txt` (at minimum: `pandas`, `pyarrow`, `requests`, `pyyaml`, `holidays`)
3. Run `git init` and add a `.gitignore` that excludes `data/` and the virtual environment
4. Create an empty `decision_log.md` in the project root

**Check:** you can run `python run_pipeline.py --help` without an error.

---

## Task 2: Config and Logging (30 min)
1. Create `config.yaml` with: start and end month, the paths for each data layer, the list of trip columns to keep, the weather latitude/longitude and variables, and the time zone (`America/New_York`)
2. In `src/utils.py`, write a `load_config()` function and a `setup_logging()` function that logs to both the console and a file (`results/pipeline.log`) with timestamps and levels
3. In `run_pipeline.py`, accept a `--stage` argument (`ingest` for now) and log a message when the stage starts and ends

**Check:** changing the month range in `config.yaml` changes what the pipeline attempts, with no code edits.

---

## Task 3: Ingest the TLC Trip Data (60 min)
Get the monthly yellow taxi Parquet files from the [TLC Trip Record Data page](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) (copy the file links from the page).

In `src/ingest.py`, write `download_trips(config)` so that it:
1. Downloads each month in the window into `data/raw/`
2. **Skips** files that already exist and logs that it skipped them
3. Downloads to a temporary name and renames only when complete
4. Raises a clear error (which month, which URL) if a download fails

Then write `load_trips_month(path, columns)` that reads **only the columns in your config** (column pruning), fixes dtypes, and saves the result to `data/interim/` as Parquet.

**Check:** run the ingest twice. The second run should skip all downloads and finish much faster.

---

## Task 4: Ingest the External Data (60 min)
1. **Zone lookup:** download the Taxi Zone Lookup Table (CSV) from the same TLC page into `data/raw/` and load it into `data/interim/`
2. **Weather:** write `fetch_weather(config)` using the Open-Meteo historical (archive) API for NYC. Request hourly temperature, precipitation, snowfall, and wind speed for your window, with the time zone set to `America/New_York`. Save the raw response to `data/raw/` and a tidy table (one row per hour) to `data/interim/`
3. **Holidays:** use the Python `holidays` package (US, state NY) to build a table of dates and holiday names for your window. Save it to `data/interim/`

**Check:** each source has a raw file, an interim file, and a log line saying how many rows it produced.

---

## Task 5: Write the Validation Checks (60 min)
In `src/validate.py`, write reusable check functions and use them on each source. Each check should **raise an error with a clear message** if it fails.

| Source | Minimum checks |
|---|---|
| Trips (each month) | Expected columns exist; row count > 0 and within a plausible range of the other months; pickup time and `PULocationID` have no nulls; timestamps are datetime types |
| Zone lookup | `LocationID` is unique; borough and zone columns exist |
| Weather | Row count equals the number of hours in your window; no duplicate timestamps; temperature within a plausible range |
| Holidays | Dates fall within the window |

**Check:** deliberately break one thing (for example, delete a column from a test copy) and confirm the pipeline stops with a helpful message.

---

## Task 6: Data-Quality Audit of the Trips (75 min)
Measure the problems. **Do not delete or fix anything today.**

Build a table (saved to `results/data_audit.csv`) with one row per issue: **issue, count, % of rows, proposed rule**. Include at least:
- Pickup timestamps outside your window
- Drop-off before pickup
- Trip duration < 1 minute and > 3 hours
- Distance ≤ 0, and distance above a high threshold you choose
- Fare or total amount ≤ 0
- Null `passenger_count`
- Pickup zone IDs 264 / 265, and any zone ID missing from the lookup
- Schema differences between months (columns present in some months but not others, such as `cbd_congestion_fee`)

**Check:** every row in the audit has a number behind it, and the proposed rule is written in one sentence.

---

## Task 7: Join-Readiness Checks (30 min)
Prepare, but **do not perform**, the full joins.
1. What percent of trips have a pickup zone found in the lookup table?
2. Does the weather table cover **every** hour in the window? (For 181 days, that is 4,344 hours. Adjust for your window.) List any missing hours
3. How many weather timestamps look duplicated or missing around the March and November clock changes?
4. For each join, write one line in the decision log: grain of both sides, join key, expected row count after the join

**Check:** you can explain, without running code, why weather is joined *after* aggregating trips to (zone, hour).

---

## Task 8: Frame the Problem in the Decision Log (30 min)
Fill in `decision_log.md` with a short entry for each item in "Framing the Prediction Problem" from the Concepts file: **grain, target, business question, metric, baseline, and the information rule.** For each, write the alternative you considered and why you chose this option.

Also add the **cleaning rules you propose** from your audit. These will be turned into code tomorrow.

---

## Task 9: Run It Clean and Commit (30 min)
1. Delete `data/interim/` (keep `data/raw/`) and run `python run_pipeline.py --stage ingest`. It should rebuild everything
2. Run it once more to confirm nothing breaks and nothing is re-downloaded
3. Commit the code, config, audit table, and decision log with a clear message

---

## Deliverables by End of Day 1
1. Project repo with the structure above and a working `--stage ingest` command
2. `config.yaml` controlling months, paths, and columns
3. Validated interim tables for trips, zone lookup, weather, and holidays
4. `results/data_audit.csv` and `results/pipeline.log`
5. `decision_log.md` with the problem framing, join plan, and proposed cleaning rules

---

## Self-Check Questions
1. Why is `data/raw/` never edited by code?
2. What happens if you run the ingest stage twice?
3. Why read only some columns from a Parquet file?
4. What would happen if the 2025 files had a new column your code didn't expect? What if an expected column were missing?
5. Why do we audit before we clean?
6. Why must weather be joined at the (zone, hour) grain and not at the trip level?
7. What is the information rule, and which kind of feature does it forbid?

---

## Stretch (Only If Everything Above Is Done)
- Fetch one optional external source (NYC permitted events or MTA subway hourly ridership) into raw and interim, with its own validation checks and a documented join key
- Add a `--months` command-line override that overrides the config for quick tests on one month

---
*Next: Week 5 Day 2, Cleaning and Feature Engineering as Pipeline Stages*