# Week 6 - Day 4: Presentation Guidelines

## Day Goal
The project is finished and frozen. Today is about planning how to explain it. There is no new modeling, no new features, and no pipeline changes. The presentation is tomorrow at 2:30 pm.

**Key theme for today:** *"A good presentation is not a tour of your code. It is a short story: a question, a method you can trust, a result you can defend, and limits you are honest about."*

---

## Concept 1: The Story to Tell
Your audience cares about four things, in this order:

1. **The problem:** what business question does this answer?
2. **The approach:** why should I trust the method?
3. **The result:** does it work, compared with something simple?
4. **The honesty:** where does it fail, and what would you do next?

If a slide does not serve one of these, cut it.

---

## Concept 2: Suggested Slide Structure (6-7 Slides)
Adjust the number to the time you are given. Fewer, clearer slides beat more, denser ones.

| # | Slide | What goes on it | Take it from |
|---|---|---|---|
| 1 | The question | The business decision, the target, and the metric | Decision log |
| 2 | The data | Taxi trips plus weather, holidays, and the zone lookup | Config, audit |
| 3 | The pipeline | A simple diagram of the stages and the checks between them | README |
| 4 | Data quality | What the audit found and what was removed (two or three numbers) | `data_audit.csv`, `cleaning_report.csv` |
| 5 | Results | Baseline vs. final model, on validation and test | `model_comparison.csv`, `test_results.csv` |
| 6 | Where it fails | Errors by hour, borough, and weather | `error_breakdown.csv` |
| 7 | Limits and next steps | Limitations and how you will reuse the pipeline | Results summary |

The pipeline diagram is the most important slide in a pipeline project. Spend the most time on it.

---

## Concept 3: Present Numbers Honestly
- Always show the baseline next to the model
- Show both validation and test. If test is worse, say so and offer a reason
- Round sensibly (MAE to one decimal place) and state the unit ("trips per zone-hour")
- If the model barely beats the baseline, that is still a valid result
- One clear chart beats a table of twenty numbers

---

## Concept 4: Explain the Choices, Not Just the Outcomes
For each major decision, use one sentence with a "because":

- "We split by time because taxi demand is a time series, and random splits would leak the future."
- "We built the full zone-hour grid because a plain groupby drops the hours with zero trips."
- "We saved the whole pipeline because the preprocessing has to travel with the model."

Your decision log already contains these. Pull them out of it.

---

## Common Mistakes to Avoid
1. Changing code now, which breaks the frozen version
2. Slides full of code instead of the story
3. Reporting only the good numbers
4. Explaining what you did without explaining why

---

## Key Takeaways
1. Question, method, result, honesty
2. The pipeline diagram is the centerpiece
3. Baseline next to the model, on validation and test
4. Every choice gets a one-sentence "because"

---
*Next: `Week6_Day4_Tasks.md`*