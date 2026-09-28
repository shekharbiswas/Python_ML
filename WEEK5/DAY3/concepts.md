# Week 5 — Day 3: Training, Time-Aware Validation, and Model Persistence

## Day Goal
Yesterday you produced a clean, feature-ready zone × hour table with a time-based split and a baseline. Today you add the **train → evaluate → save** stages: a single sklearn `Pipeline` that holds preprocessing and model together, honest time-aware cross-validation, and a saved model that a separate script can load and use.

**Model choice is not the point today.** You compare two candidates, pick one by validation score, and move on. The point is that the *stages around the model* are correct, repeatable, and reusable.

**Key theme for today:** *"The thing you save is not just a model. It is the whole pipeline, with the preprocessing that goes with it."*

**Reminder:** Days 1-4 are project work, Day 5 is presentation prep, and the presentation is **Friday at 15:30**.

---

## Concept 1: One Object for Preprocessing + Model
Yesterday's stateless features (lags, calendar) are already in the table. Stateful steps, which **learn from data**, belong inside an sklearn `Pipeline` so they are fitted on training data only:

```
Pipeline
├── ColumnTransformer
│   ├── numeric columns  → imputer / scaler (if the model needs it)
│   └── categorical cols → encoder
└── model
```

Why it matters:
- **No leakage:** inside cross-validation, the scaler and encoder are refitted on each training fold and never see the validation fold
- **One object to save:** predicting on new data applies the exact same preprocessing automatically
- **One place to change:** swapping the model changes one line

Write a `build_pipeline(model_name, config)` function that returns the full pipeline. The model name comes from `config.yaml`.

---

## Concept 2: Two Candidates, No Model Rabbit Hole
This week the goal is a reliable pipeline, not the best possible score. Use two candidates that behave differently:

| Candidate | Why include it |
|---|---|
| **Ridge regression** | Simple, fast, interpretable. Needs scaled numeric inputs and encoded categories |
| **HistGradientBoostingRegressor** | Handles missing values, mixed features, and 1M+ rows quickly. Supports categorical columns natively |

Rules:
- No extended hyperparameter search (you did that in Week 4). Use sensible defaults and record them
- Both go through the **same** pipeline factory and the **same** evaluation code
- The winner is chosen by **validation MAE** compared with the baseline from Day 2
- If neither beats the baseline, that is a finding to report, not something to hide

---

## Concept 3: Time-Aware Cross-Validation
Random k-fold on time-ordered data lets a model train on the future and predict the past. Use **expanding-window validation**: train on the earlier part, validate on the next part, repeat.

```
Fold 1:  train ████░░░░░░  validate ░░░░██
Fold 2:  train ██████░░░░  validate ░░░░░░██
Fold 3:  train ████████░░  validate ░░░░░░░░██
```

Practical detail: sklearn's `TimeSeriesSplit` splits by **row order**. Your table has many zones per hour, so **sort by time first** (hour, then zone). Then the splits fall on time boundaries and never cut through the middle of an hour.

Do this **inside the training period only** (January-April). The validation month (May) stays separate for the final comparison, and June stays locked.

Report every metric as **mean ± standard deviation across folds**. A small gap between two models with a large standard deviation is not a real difference.

---

## Concept 4: Evaluation Beyond One Number
A single MAE hides where the model fails. Evaluate the chosen model on the validation month:

- **MAE vs. the baseline** (same zone, same hour last week), the main comparison
- **Errors by hour of day, by borough, and by weather** (rain vs. no rain)
- **Count-data sanity:** demand cannot be negative, so clip predictions at 0 and record that decision

Ask *"where is the model worst, and does that matter for the business question?"* rather than only *"what is the score?"* You will use these breakdowns in Day 5's presentation.

---

## Concept 5: Model Persistence with `joblib`
Save the **entire fitted pipeline** (preprocessing and model), not just the model:

```
models/
├── demand_model.joblib      # the fitted Pipeline
└── model_metadata.json      # what it was trained on and how well it did
```

Metadata worth saving:
- Model name and parameters
- Training window (start and end dates)
- Feature column list, in order
- Validation MAE and the baseline MAE
- Library versions (sklearn, pandas), and the date it was trained

Three reliability rules:
1. **Pin library versions** in `requirements.txt`. A model saved with one sklearn version may not load correctly in another
2. **Only load `joblib` files you created yourself.** Loading files from untrusted sources can run arbitrary code
3. **Test the round trip:** save, reload in a fresh process, and confirm the predictions match the in-memory model exactly

---

## Concept 6: A Separate Prediction Script
A model is only useful if something else can use it. `src/predict.py` should:
1. Load the saved pipeline and metadata
2. Read a new table of rows with the same feature schema
3. **Check that the expected columns are present** and raise a clear error if they aren't
4. Return predictions (clipped at 0), with zone and hour attached

This script is the bridge from "a notebook result" to "something a team could run every morning."

---

## Concept 7: Recording Every Run
Every training run should leave a record: model name, parameters, CV mean ± std, validation MAE, baseline MAE, and the date. Append one row per run to `results/model_comparison.csv`. When someone asks "why did you pick this model?", the answer is in a file, not in your memory.

---

## Common Mistakes to Avoid
1. Fitting a scaler or encoder on the full dataset before cross-validation
2. Using shuffled k-fold on time-ordered data
3. Forgetting to sort by time before `TimeSeriesSplit`
4. Saving only the model and losing the preprocessing
5. Not pinning library versions
6. Comparing models on different data or different code paths
7. Spending the day tuning instead of finishing the stages
8. Touching the June test file

---

## Key Takeaways
1. Stateful preprocessing lives in the `Pipeline`, fitted on training data only
2. Two simple candidates, one shared evaluation path, and a baseline to beat
3. Time-aware CV means sort by time and use expanding windows
4. Break the error down by hour, borough, and weather
5. Save the whole pipeline plus metadata, and test the reload
6. A prediction script proves the model can be reused
7. Every run leaves a record

---
*Next: `Week5_Day3_Tasks.md`*