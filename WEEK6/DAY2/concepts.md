# Week 6 - Day 2: Finish the Modeling Stages (Features, Split, Baseline, Train, Save)

## Day Goal
Yesterday you finished ingest, audit, and clean. Today you finish the second half of the pipeline: a model-ready table, a time-based split, a baseline score, one trained model, and a saved model that a separate script can load. You will not compare many models or tune anything today.

**Key theme for today:** *"Build features without leakage, and save the whole pipeline, not just the model."*

**Reminder:** This is the same project as Week 5. If a stage is already done, check it and move on.

---

## Concept 1: The Complete Zone by Hour Table
Your target is the number of pickups per zone and hour. A plain `groupby` only returns hours that had at least one trip, so quiet hours disappear.

- Build the full grid first: every valid zone for every hour in the window
- Left-join the trip counts, then fill missing counts with 0
- Check that rows = number of zones x number of hours, with no duplicate (zone, hour) pairs

---

## Concept 2: Joins That Never Change the Row Count
Join weather, holidays, and the zone lookup onto the grid.

| Join | Key |
|---|---|
| Zone lookup | zone ID |
| Weather | pickup hour |
| Holidays | date |

Rule: the row count after each join must equal the row count before. If it changes, the right side has duplicate keys. Add an assertion in the code.

---

## Concept 3: Features Without Leakage
Only use information available before the hour being predicted.

- Calendar: hour, day of week, is_weekend, is_holiday
- Weather: temperature, precipitation, snowfall, wind
- Lags: demand in the same zone 1, 24, and 168 hours ago
- Compute lags within each zone: sort by zone and time, then group by zone before shifting
- The first rows of each zone have no history, so their lags are missing. Drop them and log how many

---

## Concept 4: Time-Based Split and Baseline
Taxi demand is a time series, so random splits are not allowed.

| Set | Period | Use |
|---|---|---|
| Train | Earlier months | Fit the model |
| Validation | Next month | Compare against the baseline |
| Test | Final month | Locked, not opened until the final evaluation |

The baseline needs no learning: predict this hour's demand as the demand in the same zone, same hour, one week ago. Record its MAE on the validation set. The model must beat it.

---

## Concept 5: One Pipeline, Saved Whole
Put preprocessing and the model in one sklearn `Pipeline`, fitted on training data only.

- Use one model, chosen in `config.yaml`
- Save the fitted pipeline with `joblib`, plus a small metadata file (training window, feature list, validation MAE, baseline MAE, library versions)
- A separate `predict.py` loads the model, checks the columns, and returns predictions clipped at 0
- Only load `joblib` files you created yourself

---

## Common Mistakes to Avoid
1. Forgetting the zero-demand hours
2. Computing lags without grouping by zone
3. Joins that quietly change the row count
4. Using a random split on time-ordered data
5. Opening the test file "just to see"
6. Saving only the model and losing the preprocessing

---

## Key Takeaways
1. Build the full zone by hour grid first, then fill zeros
2. Joins must not change the row count
3. Lags are computed within each zone
4. Split by time and keep the test month locked
5. The baseline is the number the model has to beat
6. Save the whole pipeline, and test that it reloads

---
*Next: `Week6_Day2_Tasks.md`*