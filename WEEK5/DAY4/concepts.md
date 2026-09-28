# Week 5 — Day 4: One-Command Pipeline, Reliability, and the Final Test

## Day Goal
Today is the **last build day**, and deliberately a lighter one, because Day 5 is presentation prep. You will not add features or try new models. Today you do three things: wire every stage into one command, run the final test evaluation **once**, and write down how the project works. At the end of today, **everything is frozen**.

**Key theme for today:** *"A project is finished when someone else can run it from scratch, and when its final number was measured once, honestly."*

**Reminder:** Day 5 is presentation prep only. The presentation is **Friday at 15:30**.

---

## Concept 1: The Stage Runner
Your `run_pipeline.py` already runs stages one at a time. Now add `--stage all`, which runs them in order:

```
ingest → clean → features → train → test
```

Rules:
- Stages run in a fixed order, and a failed stage **stops the run**. Never continue with broken inputs
- Each stage logs when it starts, when it ends, and how long it took
- One command, no manual steps in between: `python run_pipeline.py --stage all`

---

## Concept 2: Errors and Logging
Reliability comes from clear failures, not from hiding them.

- Wrap the stage runner in a single top-level `try/except` that **logs the error with the stage name** and exits with a non-zero code
- Error messages say **what failed, where, and what to check** ("Weather file missing for 2025-03: check `data/raw/`"), not just "Error"
- Do not catch exceptions and carry on silently
- The log file (`results/pipeline.log`) should tell the full story of a run: stages, row counts, checks passed, and durations

---

## Concept 3: The Clean-Run Test
The real test of a pipeline is a run from scratch:

1. Delete everything the pipeline generates: `data/interim/`, `data/processed/`, `models/`, and `results/`
2. Keep only `data/raw/` (or delete that too if your connection allows)
3. Run `python run_pipeline.py --stage all`
4. Confirm the same files appear and the **same validation numbers** come out

Make this reliable by setting `random_state` wherever a model uses randomness, and by pinning library versions in `requirements.txt`. If the numbers change between runs, find out why before you present.

---

## Concept 4: The Final Test Evaluation (Once)
Today the locked test month (June) is opened **for the first time and exactly once**.

Report:
- Test **MAE** of the final model
- Test MAE of the **baseline** (same zone, same hour last week)
- The gap between validation and test MAE

Rules:
- **No changes after seeing the test result.** No retuning, no new features, no swapping the model. If you change anything, the test set is no longer a fair test
- If the test MAE is much worse than validation, report it honestly and explain what might differ (a different month, seasonal effects). Do not "fix" it quietly

**Limitations worth stating in your write-up** (pick the ones that apply):
- Only six months of data, so a full year of seasons is not covered
- Yellow taxis only. App-based services carry a large share of NYC trips, so this measures one part of demand
- Weather is treated as known in advance, but a real deployment would rely on forecasts, which are less accurate
- One weather source for the whole city
- Congestion pricing began in 2025, so demand patterns in 2025 may differ from earlier years

---

## Concept 5: Documentation That Lets Someone Else Run It
Your project `README.md` should answer, in order:
1. **What does this project do?** (two sentences)
2. **Data sources** with links (TLC trips, zone lookup, weather, holidays)
3. **How to run it** (setup, then the one command)
4. **Project structure** (the folder tree)
5. **Results** (baseline vs. model, on validation and test)
6. **Limitations and known issues**

The decision log is the detailed record. The README is the front door.

---

## Concept 6: Freeze and Reuse
- **Freeze:** commit everything and add a git tag (for example, `v1.0`). After today, nothing changes
- **Reuse for Week 6:** your own project will use the same skeleton. What stays: the stage runner, the validation module, logging, the train/evaluate/save structure, and the README format. What you change: the config, the ingest code, the cleaning rules, the features, and the target

Write a short "Reusing this template" note at the bottom of the README listing those two groups. Future you will be grateful.

---

## Common Mistakes to Avoid
1. Running the test set more than once, or tuning after seeing it
2. Presenting a clean run you never actually tested from scratch
3. Error messages that don't say which stage or file failed
4. Forgetting `random_state`, so the numbers change between runs
5. A README that only works on your laptop
6. Starting new features today instead of freezing

---

## Key Takeaways
1. One command runs the whole pipeline, and a failed stage stops it
2. The clean-run test is the proof that the pipeline is reproducible
3. The test set is used once, and the result is reported as it is
4. State the limitations plainly. It makes the results more credible
5. A README and a decision log let someone else understand and rerun the work
6. Freeze today so Day 5 is only about explaining

---
*Next: `Week5_Day4_Tasks.md`*