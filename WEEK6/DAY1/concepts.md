# Week 6 - Day 1: Finish the Data Stages (Ingest, Audit, Clean)

## Day Goal
Today you finish the front half of the NYC taxi pipeline. By the end of the day, one command should take you from raw files to clean files, with a data audit and a cleaning report along the way. You will not build features or train a model today.

**Key theme for today:** *"Measure the problems first, then fix them with rules you can explain."*

**Reminder:** This is the same project as Week 5. If a stage is already done, check it and move on.

---

## Concept 1: Stages With a Clear Contract
Each stage reads from one folder and writes to the next:

```
ingest   : downloads to data/raw/
validate : reads data/raw/, writes data/interim/
clean    : reads data/interim/, writes data/processed/
```

- Raw files are never edited
- Each stage can be run alone: `python run_pipeline.py --stage clean`
- If a stage fails, the pipeline stops. It does not continue with bad output

---

## Concept 2: Reproducible Ingestion
Running ingest twice should give the same result as running it once.

- If a file already exists in `data/raw/`, skip it and log that you skipped it
- Download to a temporary name, then rename, so a half-finished file never looks valid
- If a download fails, raise a clear error with the file name and URL
- Select columns by name. A missing expected column should stop the run with a clear message
- Check the column list of each month. The 2025 files include `cbd_congestion_fee`, and earlier months may not

---

## Concept 3: Audit Before Cleaning
An audit measures problems without changing the data. Each problem gets a count and a percentage, so every cleaning rule has a number behind it.

Today's audit table has one row per issue:

| issue | count | % of rows | proposed rule |
|---|---|---|---|
| Pickup outside window | ... | ... | Drop rows with pickup outside the window |

Rule for today: **audit only, do not delete.**

---

## Concept 4: Cleaning Rules as Functions
Each rule from the audit becomes one small function:

```
drop_outside_window(df)
drop_bad_duration(df)
drop_nonpositive_fares(df)
```

- One function per rule, named for what it removes
- Log how many rows each rule removed
- Save a cleaning report to `results/cleaning_report.csv`
- Thresholds live in `config.yaml`, not in the code
- If a large share of rows is removed (for example, more than 10%), stop and investigate

---

## Common Mistakes to Avoid
1. Loading every column of every file and running out of memory
2. Editing files in `data/raw/`
3. Cleaning during the audit, so the original counts are lost
4. Hard-coding thresholds inside functions
5. Removing rows without logging how many

---

## Key Takeaways
1. Ingestion can be rerun safely
2. Raw data is never changed
3. Audit first, then clean
4. Each cleaning rule is a function with a logged count
5. A pipeline should stop loudly when something is wrong

---
*Next: `Week6_Day1_Tasks.md`*