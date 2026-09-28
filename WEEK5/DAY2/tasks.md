# Week 5 — Day 2 Tasks: From Raw Trips to a Model-Ready Table

**One direction today:** turn the interim trip data into a clean, joined, feature-ready zone × hour table, split by time, with a baseline score. Four tasks, one straight line.

```
interim trips → clean → zone×hour grid → join + features → time split → baseline
```

**Reminder:** Days 1-4 are project work. Day 5 is presentation prep. The presentation is **Friday at 15:30**.

---

## Task 1: Clean Stage (75 min)
In `src/clean.py`, turn the cleaning rules from your Day 1 decision log into **one function per rule**. Put the thresholds in `config.yaml`.

Add a `run_clean(config)` that:
1. Loads the interim trips month by month
2. Applies each rule in order and **logs how many rows each rule removed**
3. Saves the cleaned trips to `data/processed/` and a summary table to `results/cleaning_report.csv` (rule, rows removed, % removed)

**Check:** the report matches your Day 1 audit numbers, and the total share removed is plausible (a few percent, not a large fraction).

---

## Task 2: Build the Zone × Hour Table (90 min)
In `src/features.py`:
1. Floor each pickup time to the hour and count trips per (zone, hour)
2. Build the **complete grid** of every valid zone × every hour in the window, left-join the counts, and fill missing counts with `0`
3. Join zone attributes, weather, and holidays. **Assert that the row count is unchanged after each join**
4. Validate: rows = zones × hours, no duplicate (zone, hour), no missing weather values

**Check:** the table has roughly 1.1 million rows for a six-month window, and zero-demand rows are present.

---

## Task 3: Build the Features (75 min)
Still in `src/features.py`, add:
- **Calendar:** hour, day of week, month, is_weekend, is_holiday
- **Lags, per zone:** demand 1 hour, 24 hours, and 168 hours ago
- **Rolling:** mean demand over the previous 24 hours (shifted so it excludes the current hour)

Drop the first 168 hours of each zone (no full history) and log how many rows were dropped.

**Check:** pick one zone and one hour and verify a lag value by hand against the raw counts. Confirm the rolling mean does not include the current hour.

---

## Task 4: Time Split and Baseline (60 min)
1. Split by time (train: first four months, validation: fifth month, test: sixth month) and save each as its own file in `data/processed/`. **Do not open the test file again until Day 4**
2. Compute the baseline (`lag_168`) **MAE on the validation set** only
3. Write the result in `decision_log.md`: the split dates, the number of features, the baseline MAE, and one sentence on what a "good" model has to beat
4. Make `python run_pipeline.py --stage features` run clean, build, features, and split in order, then commit

**Check:** delete `data/processed/` and rerun the command. You should get the same files and the same baseline MAE.

---

## Deliverables by End of Day 2
1. `src/clean.py` and `src/features.py`, run through `run_pipeline.py`
2. `results/cleaning_report.csv`
3. Train, validation, and locked test files in `data/processed/`
4. Baseline validation MAE recorded in `decision_log.md`

---

## Self-Check Questions
1. Why do zone-hours with zero trips have to be added manually?
2. Why must lags be computed within each zone?
3. Why must the rolling mean be shifted?
4. Which preprocessing steps wait for the sklearn `Pipeline` tomorrow, and why?
5. Why is a random split wrong for this problem?

---
*Next: Week 5 Day 3, Training, Time-Aware Cross-Validation, and Model Persistence*