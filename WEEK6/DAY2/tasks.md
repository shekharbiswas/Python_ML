# Week 6 - Day 2 Tasks: Features, Split, Baseline, Train, Save

Work in your Week 5 repo. If a task is already done, check that it works and move on to the next one.

---

## Suggested Timeline
| Time | Activity |
|---|---|
| Block 1 | Task 1: grid and joins |
| Block 2 | Task 2: features and split |
| Block 3 | Task 3: baseline, train, and save |
| Final 40 min | Task 4: predict script and decision log |

Total: 6 hours.

---

## Task 1: Grid and Joins (110 min)
1. Build the complete grid: every valid zone for every hour in your window
2. Count pickups per zone and hour from the cleaned files, join them to the grid, and fill missing counts with 0
3. Join the zone lookup, weather, and holidays, one at a time
4. After each join, assert that the row count did not change

**Check:** rows = zones x hours, there are no duplicate (zone, hour) pairs, and there are no missing weather values.

---

## Task 2: Features and Split (90 min)
1. Add calendar features: hour, day of week, is_weekend, is_holiday
2. Add lag features for 1, 24, and 168 hours, computed within each zone
3. Drop the warm-up rows that have no history, and log how many
4. Split by time using the dates in `config.yaml`, and save train, validation, and test as separate files in `data/processed/`

**Check:** the periods do not overlap, and only the warm-up rows had missing lags.

---

## Task 3: Baseline, Train, and Save (120 min)
1. Compute the baseline MAE on the validation set (same zone, same hour, last week)
2. Build one sklearn `Pipeline` with a model chosen in `config.yaml`
3. Fit it on the training set and report validation MAE next to the baseline MAE
4. Save the pipeline with `joblib` and write a small metadata JSON

**Check:** you can say in one sentence whether the model beats the baseline. If it does not, that is a finding to record, not to hide.

---

## Task 4: Predict Script and Decision Log (40 min)
1. Write a short `predict.py` that loads the saved model, checks that the expected columns are present, and returns predictions clipped at 0
2. Test it on a few validation rows
3. Add two or three lines to the decision log: the split dates, the baseline score, and the model score

**Check:** predictions from the reloaded model match the in-memory model.

---

## Deliverables by End of Day 2
1. Processed train, validation, and test files (test untouched)
2. Baseline and validation scores in the decision log
3. A saved model and metadata file in `models/`
4. A working `predict.py`

---

## Self-Check Questions
1. Why do we build the full grid before counting?
2. Why must lags be computed within each zone?
3. Why is the test month locked?
4. Why do we save the whole pipeline and not only the model?

---
*Next: Week 6, Day 3*