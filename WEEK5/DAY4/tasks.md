# Week 5 — Day 4 Tasks: Wire, Test, Freeze

**A lighter day, on purpose.** Three tasks, one straight line, and you are done early enough to start thinking about tomorrow.

```
wire all stages → clean-run test → final test evaluation → README + freeze
```

**Reminder:** No new features or models today. Day 5 is presentation prep. The presentation is **Friday at 15:30**.

---

## Task 1: One Command, From Scratch (75 min)
1. Add `--stage all` to `run_pipeline.py` so it runs `ingest → clean → features → train` in order, logging the start, end, and duration of each stage
2. Add a top-level `try/except` that logs the error **with the stage name** and exits with a non-zero code
3. Set `random_state` wherever the model uses randomness, and confirm `requirements.txt` is pinned
4. **Clean-run test:** delete `data/interim/`, `data/processed/`, `models/`, and `results/`, then run `python run_pipeline.py --stage all`

**Check:** the same files reappear and the validation MAE matches yesterday's number. If it doesn't, find out why before moving on.

---

## Task 2: The Final Test Evaluation, Once (45 min)
1. Add a `--stage test` that loads the saved model and the locked June file
2. Compute MAE for the **final model** and for the **baseline**, and save them to `results/test_results.csv` along with the validation numbers for comparison
3. Add one entry to `decision_log.md`: the test MAE, the baseline MAE, the gap from validation, and two sentences on what it means
4. Add two or three limitations that apply to your project (see the Concepts file)

**Check:** you ran it once, and made no changes to the model or features afterward.

---

## Task 3: README and Freeze (60 min)
1. Write the project `README.md`: what it does, data sources with links, how to run it, project structure, results (validation and test), and limitations
2. Add a short "Reusing this template" section: what stays and what changes for a new project
3. Make a final commit and add a tag: `git tag v1.0`

**Check:** a classmate could clone the repo, follow only the README, and run the pipeline.

---

## Deliverables by End of Day 4
1. `python run_pipeline.py --stage all` works from scratch, and `--stage test` produces the final result
2. `results/test_results.csv`
3. Completed `decision_log.md` (all entries, limitations included)
4. `README.md`, a final commit, and the `v1.0` tag

---

## Self-Check Questions
1. What must happen when one stage fails halfway through a run?
2. What does the clean-run test prove that a normal run doesn't?
3. Why can't you change anything after seeing the test result?
4. What stays the same and what changes when you reuse this for Week 6?

---
*Next: Week 5 Day 5, Presentation Prep (presentation at 15:30)*