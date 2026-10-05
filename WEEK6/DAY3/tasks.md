# Week 6 - Day 3 Tasks: Compare, Analyze, Run, Test, Freeze

Work in your Week 5 repo. The results you produce today are the results you present on Day 5.

**Day 5 presentation: 2:30 pm.** Days 4 and 5 are for presentation preparation only, so the project must be finished today.

---

## Suggested Timeline
| Time | Activity |
|---|---|
| Block 1 | Task 1: second model and time-aware cross-validation |
| Block 2 | Task 2: error breakdown |
| Block 3 | Task 3: one-command pipeline, logging, and checks |
| Block 4 | Task 4: final test evaluation |
| Block 5 | Task 5: save results and write up |
| Final 50 min | Task 6: freeze and test from a fresh clone |

Total: 7 hours.

---

## Task 1: Second Model and Time-Aware Cross-Validation (100 min)
1. Make sure your pipeline factory can build both Ridge and HistGradientBoostingRegressor from a name in `config.yaml`
2. Sort the training data by time, then run expanding-window cross-validation with `TimeSeriesSplit`, on the training period only
3. Record each model's score as mean +/- standard deviation across folds
4. Score both models on the validation month and compare them with the baseline
5. Choose the winner by validation MAE

**Check:** both models use the same code path, and your choice can be explained with the numbers.

---

## Task 2: Error Breakdown (60 min)
1. Predict on the validation month with the chosen model, and clip predictions at 0
2. Compute MAE by hour of day, by borough, and for rain vs. no rain
3. Save the tables to `results/error_breakdown.csv`
4. Draw one chart, for example MAE by hour of day
5. Write two sentences: where is the model worst, and why might that be?

**Check:** you can name one place where the model does well and one where it does not.

---

## Task 3: One-Command Pipeline, Logging, and Checks (100 min)
1. Make sure `python run_pipeline.py` runs ingest, clean, features, train, and evaluate in order, and that `--stage` runs a single stage
2. Replace any `print` with `logging`, so each stage logs what it did and how many rows went in and out
3. Add or confirm checks between stages: expected columns, row counts, null rates in key columns, no overlap between the time periods
4. Make a stage stop with a clear message when a check fails
5. Delete `data/processed/` and `models/`, then run the whole pipeline from the start

**Check:** the full run finishes without manual steps and the log tells the story of the run.

---

## Task 4: Final Test Evaluation (40 min)
1. Confirm the model and settings are final
2. Evaluate the chosen model on the locked test month, once
3. Save the result to `results/test_results.csv`, next to the baseline score on the same month
4. Note whether test is better or worse than validation, with one sentence on why

**Check:** you evaluated the test month once, and you did not change anything afterwards.

---

## Task 5: Save Results and Write Up (70 min)
1. Append one row per model to `results/model_comparison.csv`: model, cross-validation mean +/- standard deviation, validation MAE, baseline MAE, date
2. Save the final model and its metadata file
3. Finish the decision log: why you chose this model, the test result, and the main choices with their evidence
4. Write the README: setup, the one command to run, and what the outputs are
5. Write a short results summary (half a page): the scores, the main finding, and two or three limitations

**Check:** a teammate could find the reason for your model choice in a file, not in your memory.

---

## Task 6: Freeze and Test From a Fresh Clone (50 min)
1. Commit everything and tag the final state, for example `v1.0`
2. Clone the repo into a new folder
3. Follow only your own README and run the pipeline
4. Fix any README gap you find, then commit and tag again if needed

**Check:** the project runs from a fresh clone using only the README. After this, no code changes.

---

## Deliverables by End of Day 3
1. Two models compared with time-aware cross-validation
2. `results/error_breakdown.csv` and one chart
3. A pipeline that runs from raw data with one command
4. `results/test_results.csv` from a single test evaluation
5. `results/model_comparison.csv`, the saved model, and a finished decision log
6. A README and a short results summary
7. A tagged final version that runs from a fresh clone

---

## Self-Check Questions
1. Why do we sort by time before `TimeSeriesSplit`?
2. Does your model beat the baseline, and by how much?
3. Where does the model fail, and does that matter for the business question?
4. Why must the test month be used only once?
5. Could someone else run your project using only the README?

---
*Next: Week 6, Day 4 (presentation concepts)*