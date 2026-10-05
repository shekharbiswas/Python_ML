# Week 6 - Day 1 Tasks: Ingest, Audit, Clean

Work in your Week 5 repo. If a task is already done, check that it works and move on to the next one.

---

## Suggested Timeline
| Time | Activity |
|---|---|
| Block 1 | Task 1: ingest and check the files |
| Block 2 | Task 2: data audit |
| Block 3 | Task 3: cleaning rules and report |
| Final 40 min | Task 4: run the stage and update the decision log |

Total: 6 hours.

---

## Task 1: Ingest and Check the Files (100 min)
1. Run the ingest stage for the months in `config.yaml`
2. Run it a second time and confirm it skips files that already exist
3. For each monthly file, print the column list and note any differences (for example, `cbd_congestion_fee`)

**Check:** the second run downloads nothing, and the log says which files were skipped.

---

## Task 2: Data Audit (100 min)
Build `results/data_audit.csv` with one row per issue: issue, count, % of rows, proposed rule. Do not delete or fix anything.

Include these issues:
- Pickup outside your window
- Drop-off before pickup
- Trip duration under 1 minute, and over 3 hours
- Distance of 0 or less
- Fare or total amount of 0 or less
- Null `passenger_count`
- Pickup zone 264 or 265

Process one monthly file at a time and read only the columns you need.

**Check:** every row has a number, and every proposed rule is one sentence.

---

## Task 3: Cleaning Rules and Report (120 min)
1. Write one function in `src/clean.py` for each rule in your audit
2. Put the thresholds (minimum and maximum duration, maximum distance) in `config.yaml`
3. Log how many rows each rule removed, and save `results/cleaning_report.csv`
4. Add a check that stops the run if more than 10% of rows are removed

Write the cleaned files to `data/processed/`. Do not touch `data/raw/`.

**Check:** the cleaning report matches the audit numbers closely. If it does not, find out why before moving on.

---

## Task 4: Run the Stage and Update the Log (40 min)
1. Run `python run_pipeline.py --stage clean` from a fresh start
2. Add two or three lines to your decision log: which rules you applied, and the number behind each

**Check:** the stage runs without manual steps.

---

## Deliverables by End of Day 1
1. `results/data_audit.csv`
2. `results/cleaning_report.csv`
3. Clean files in `data/processed/`
4. Updated decision log

---

## Self-Check Questions
1. Why do we audit before cleaning?
2. What happens if you run the ingest stage twice?
3. Which cleaning rule removed the most rows, and does that make sense?
4. Why do thresholds live in the config file?

---
*Next: Week 6, Day 2 (features, split, baseline, train and save)*