# Week 5 — Day 3 Tasks: Train, Evaluate, Save

**One direction today:** put the model inside a proper pipeline, compare two candidates fairly, and save the winner so a separate script can use it. Four tasks, one straight line.

```
processed table → Pipeline → time-aware CV → validation check → save → reload + predict
```

**Reminder:** Days 1-4 are project work. Day 5 is presentation prep. The presentation is **Friday at 15:30**. Do not open the test file today.

---

## Task 1: Build the Pipeline Factory (75 min)
In `src/train.py`, write `build_pipeline(model_name, config)` that returns one sklearn `Pipeline`:
1. A `ColumnTransformer` that handles numeric columns (impute and scale where the model needs it) and categorical columns (zone ID, borough) with an encoder that ignores unseen values
2. The model: **Ridge** or **HistGradientBoostingRegressor**, chosen by `model_name` from `config.yaml`
3. Sensible default parameters, kept in the config

**Check:** switching `model_name` in the config swaps the model with no other code change.

---

## Task 2: Compare Candidates with Time-Aware CV (90 min)
1. Load the **train** file (January-April) and **sort by time** (hour, then zone)
2. Run expanding-window cross-validation (`TimeSeriesSplit`, 3 folds) for both candidates through the **same** function, scoring MAE
3. Fit each candidate on the full train set and score MAE on the **validation** file (May)
4. Append one row per candidate to `results/model_comparison.csv`: model, parameters, CV MAE mean ± std, validation MAE, baseline MAE (from Day 2), date
5. Choose the winner by validation MAE and write one sentence in `decision_log.md` on why

**Check:** both candidates were scored by the same code. If neither beats the baseline, record that honestly and continue with the better one.

---

## Task 3: Look at Where the Model Fails (45 min)
For the chosen model on the validation month, clip predictions at 0, then save a small table to `results/error_breakdown.csv`:
- MAE by **hour of day**
- MAE by **borough**
- MAE for **rainy vs. dry** hours

Write two sentences in the decision log: where is the model worst, and why might that be?

**Check:** you can say which hours or boroughs the model handles worst, in words, in one breath.

---

## Task 4: Save, Reload, Predict (75 min)
1. Fit the winner on the full train set and save the **whole pipeline** to `models/demand_model.joblib` with `joblib`, plus `models/model_metadata.json` (model name, parameters, training window, feature list, validation and baseline MAE, library versions, date)
2. Pin your library versions in `requirements.txt`
3. Write `src/predict.py`: load the pipeline, check the expected columns are present (raise a clear error if not), and return clipped predictions with zone and hour attached
4. **Round-trip test:** in a fresh Python process, load the model, predict on the validation file, and confirm the predictions match the in-memory model
5. Add `python run_pipeline.py --stage train` (train, evaluate, save) and commit

**Check:** delete `models/` and rerun `--stage train`. It rebuilds the same files and the same validation MAE.

---

## Deliverables by End of Day 3
1. `src/train.py` and `src/predict.py`, with `--stage train` in `run_pipeline.py`
2. `models/demand_model.joblib` and `models/model_metadata.json`
3. `results/model_comparison.csv` and `results/error_breakdown.csv`
4. Decision log entries for the model choice and the error analysis

---

## Self-Check Questions
1. Why is the scaler inside the `Pipeline` and not applied before it?
2. Why must the data be sorted by time before `TimeSeriesSplit`?
3. Why save the whole pipeline instead of just the model?
4. Why pin the sklearn version, and why only load `joblib` files you created?
5. What would the round-trip test catch that a normal training run wouldn't?

---
*Next: Week 5 Day 4, Productionizing: One-Command Pipeline, Reliability, and the Final Test Evaluation*