# Week 6 - Day 3: Honest Evaluation, the One-Command Pipeline, and Finishing the Project

## Day Goal
Yesterday you trained one model and saved it. Today you finish the project: compare two models with time-aware cross-validation, see where the model fails, make the whole pipeline run with one command, evaluate once on the locked test month, and write up the results. By the end of today, the code, the results, and the write-up are final.

**Key theme for today:** *"A result is only believable if the evaluation is honest and someone else can reproduce it."*

**Reminder:** Day 4 and Day 5 are for preparing the presentation only. The presentation is on Day 5 at 2:30 pm. Everything is frozen at the end of today, so any pipeline work, results, and README must be done today.

---

## Concept 1: Two Candidates, One Shared Path
Compare two models that behave differently:

| Candidate | Why include it |
|---|---|
| Ridge regression | Simple and fast. Needs scaled numbers and encoded categories |
| HistGradientBoostingRegressor | Handles mixed features and large tables quickly |

- Both go through the same pipeline factory and the same evaluation code
- Use sensible defaults, with no long hyperparameter search
- The winner is chosen by validation MAE compared with the baseline
- If neither beats the baseline, report that as a finding

---

## Concept 2: Time-Aware Cross-Validation
Random k-fold lets a model train on the future and predict the past. Use expanding windows instead: train on the earlier part, validate on the next part, and repeat.

- Sort the table by time first (hour, then zone), so splits fall on time boundaries
- Use `TimeSeriesSplit` inside the training period only
- Report every score as mean +/- standard deviation across folds
- A small gap between two models with a large standard deviation is not a real difference

---

## Concept 3: Look at the Errors, Not Just One Number
A single MAE hides where the model fails. On the validation month, break the error down by:

- Hour of day
- Borough
- Rain vs. no rain

Ask where the model is worst and whether that matters for the business question. Clip predictions at 0, since demand cannot be negative. These breakdowns feed straight into the presentation.

---

## Concept 4: One Command, With Logs and Checks
The whole project should run with `python run_pipeline.py`.

- Stages run in order: ingest, clean, features, train, evaluate
- Every stage logs what it did, using `logging` and not `print`
- Checks sit between stages and stop the run with a clear message when something is wrong
- All settings come from `config.yaml`
- Test it from a fresh start: delete the processed data and rerun

---

## Concept 5: The Locked Test Month, Used Once
The test month is the honest final score.

- Open it only after the model and settings are final
- Evaluate once, then record the number, whatever it is
- If test is worse than validation, report it and offer a reason
- Do not adjust the model after seeing the test result

---

## Concept 6: Finish and Freeze
A project is finished when someone else can use it without asking you.

- The README says how to set up and run the project, in a few lines
- The decision log lists each choice and the evidence behind it
- A short results summary states the scores, the main finding, and the limitations
- Commit and tag the final state (for example `v1.0`), then test it by cloning into a new folder and running it
- After the freeze, no code changes. Anything you notice later goes on the limitations slide

---

## Common Mistakes to Avoid
1. Shuffled k-fold on time-ordered data
2. Forgetting to sort by time before `TimeSeriesSplit`
3. Fitting a scaler on all the data before cross-validation
4. Comparing models on different data or different code paths
5. Using the test month to make choices
6. A pipeline that only works because earlier files are already on disk
7. Leaving the README and decision log for later

---

## Key Takeaways
1. Two simple models, one shared path, and a baseline to beat
2. Time-aware CV means sort by time and use expanding windows
3. Report mean +/- standard deviation, not a single score
4. Break the error down by hour, borough, and weather
5. One command should rebuild everything from raw data
6. The test month is used once
7. Write up and freeze today, so the next two days are only for the presentation

---
*Next: `Week6_Day3_Tasks.md`*