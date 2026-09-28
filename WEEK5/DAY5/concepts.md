# Week 5 — Day 5: Presentation Prep

## Day Goal
Everything is built and frozen. Today has one job: **explain what you built, what you chose, and why, clearly and honestly.** There is no new modeling, no new features, and no pipeline changes today. The presentation is **today at 15:30**.

**Key theme for today:** *"A good presentation is not a tour of your code. It is a short story: a question, a method you can trust, a result you can defend, and limits you are honest about."*

---

## Concept 1: The Story to Tell
Your audience cares about four things, in this order:

1. **The problem:** what business question does this answer?
2. **The approach:** why should I trust the method?
3. **The result:** does it work, compared with something simple?
4. **The honesty:** where does it fail, and what would you do next?

If a slide doesn't serve one of these, cut it.

---

## Concept 2: Suggested Slide Structure (6-7 Slides)
Adjust the number to the time you are given. Fewer, clearer slides beat more, denser ones.

| # | Slide | What goes on it | Source in your project |
|---|---|---|---|
| 1 | **The question** | The business decision (for example, "where should drivers be positioned each hour?"), the target, and the metric | Decision log |
| 2 | **The data** | NYC TLC yellow taxi trips, plus weather, holidays, and the zone lookup. Size of the data and the time window | Config, audit |
| 3 | **The pipeline** | A simple diagram of the stages: ingest → clean → features → train → test, with the checks between them | README |
| 4 | **Data quality decisions** | What the audit found, what was removed, and why (two or three numbers) | `data_audit.csv`, `cleaning_report.csv` |
| 5 | **Results** | Baseline vs. final model, on validation **and** test | `model_comparison.csv`, `test_results.csv` |
| 6 | **Where it fails** | Errors by hour, borough, and weather. One or two honest findings | `error_breakdown.csv` |
| 7 | **Limits and next steps** | Limitations, and how you will reuse the pipeline for your Week 6 project | README, decision log |

The pipeline diagram (slide 3) is the most important slide in a pipeline project. Spend the most time on it.

---

## Concept 3: Present Numbers Honestly
- Always show the **baseline next to the model**. A number alone means nothing
- Show **both validation and test**. If test is worse, say so and offer a reason. Do not hide it
- Round sensibly (MAE to one decimal place), and say what the unit is ("trips per zone-hour")
- If the model barely beats the baseline, that is still a valid result. Say what it implies
- One clear chart beats a table of twenty numbers

---

## Concept 4: Explain the Choices, Not Just the Outcomes
For each major decision, use one sentence with a *because*:
- "We split by time **because** taxi demand is a time series, and random splits would leak the future."
- "We built the full zone-hour grid **because** a plain `groupby` drops the hours with zero trips."
- "We saved the whole pipeline **because** the preprocessing has to travel with the model."

Your decision log already contains these. Pull them out of it.

---

## Concept 5: Showing the Pipeline Live (Carefully)
A full run can take a long time, and live runs fail at the worst moments.

- **Don't** run the entire pipeline live
- **Do** show the folder structure, the one command (`python run_pipeline.py --stage all`), and a **screenshot or saved excerpt of the log** from a successful run
- If you demo anything live, make it something short, such as loading the saved model and predicting a few rows with `predict.py`
- Have a backup: screenshots of every result in your slides, in case the demo fails

---

## Concept 6: Questions You Should Be Ready For
1. Why did you use MAE?
2. Why is a simple baseline included, and did your model beat it?
3. How do you know there was no data leakage?
4. Why did you split by time and not randomly?
5. What happens if a new month of data has a different schema?
6. What would you do first with more time?
7. Which parts of this pipeline will you reuse in Week 6?

Write a one-line answer to each. If you don't know something, say so plainly, and say how you would find out.

---

## Common Mistakes to Avoid
1. Changing code today, which breaks the frozen `v1.0`
2. Slides full of code instead of the story
3. Reporting only the good numbers
4. Running a long live demo with no backup
5. Not rehearsing the timing
6. Explaining what you did without explaining why

---

## Key Takeaways
1. Question, method, result, honesty: build the story in that order
2. The pipeline diagram is the centerpiece
3. Show baseline vs. model on both validation and test
4. Every choice gets a one-sentence "because"
5. Rehearse out loud, and prepare for the obvious questions

---
*Next: `Week5_Day5_Tasks.md`*