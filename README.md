<p align="center">
  <img src="assets/metric_breakdown.svg" alt="Contribution to automation metric breakdown" width="100%">
</p>

<p align="right"><a href="README.ru.md">Русская версия →</a></p>

# Contribution-to-Automation Metric for Intent Analysts

![Python](https://img.shields.io/badge/Python-pandas-14131a?style=flat-square&labelColor=14131a&color=7a1f2b)
![SQL](https://img.shields.io/badge/SQL-ETL-14131a?style=flat-square&labelColor=14131a&color=7a1f2b)
![BI](https://img.shields.io/badge/BI-reporting-14131a?style=flat-square&labelColor=14131a&color=7a1f2b)

> **Note on source code.** This repository documents methodology, design decisions, and results only. The implementation is proprietary to the employer where this project was built and is not published here.

A custom metric that, for the first time, isolated the Intent Analysts (IA) team's specific contribution to chatbot automation quality — separating "the intent is well-labeled" from "the underlying bot logic works" as two distinct, independently diagnosable causes of automation success or failure.

## The problem

The product team could measure overall chatbot automation, but not **whose work moved it**. Aggregate performance mixed the impact of developers, ML engineers, and intent analysts together, which made it impossible to justify IA-specific initiatives, staffing decisions, or process changes with data — decisions were argued qualitatively, not backed by a number anyone could point to.

## The metric

Every automated dialog falls into one of two buckets: it either completed without errors (`yes_auto`) or it was automated but ran into an error along the way (`not_yes_auto`). Together these make up all automated dialogs (`full_auto = yes_auto + not_yes_auto`).

```
contribution_to_automation = (yes_auto ÷ full_auto) × 100
```

This isolates automation **quality** from automation **coverage**: a dialog can be automated (counted in `full_auto`) but still fail — and that failure is far more often attributable to how the intent was defined and labeled than to whether the bot attempted to handle it at all. Tracking this ratio separately from raw automation rate is what let the team distinguish two very different problems that used to look identical from the outside: *"the intent is poorly defined"* versus *"the intent is fine, but the underlying procedure doesn't handle it well."*

*Numbers above are from an actual computed example (74.9% / 6.8% / 81.7% / 91.7%), shown to illustrate the metric's behavior rather than as an ongoing production figure.*

## Approach

- **Data pipeline** — extraction and aggregation from analytics storage: message logs, response-quality annotations, session metadata, filtered through an ETL step for dialog type and annotation completeness.
- **Metric construction** — designed `full_auto`, `yes_auto`, `not_yes_auto`, and an interpolated variant (`interpol_yes_threads_auto`) to handle partially-labeled periods without discarding them.
- **Analytics & reporting** — SQL logic to identify fully automated dialogs at scale, with KPI visualization built into the team's BI platform for ongoing reference.

## Scale

Computed across all chatbot channels and intents the team was responsible for, with the IA team itself numbering roughly 10 people per channel (more on the largest channel). Designed to run on a quarterly cadence, with the ability to pull the same calculation manually for any custom period.

## Results

- First quantitative measure of IA's specific contribution to chatbot automation quality, distinct from the product's overall automation rate.
- Made it possible to diagnose *where* an automation problem sat: bottom-line automation could look identical whether the intent was poorly defined or the underlying procedure was broken, and this metric was what let the team tell those two cases apart.
- Used to build the initial data-backed business case for IA's impact, at a point where that impact had previously only been argued qualitatively.

## Business impact

- Gave the product team a defensible, numeric basis for prioritizing IA-driven fixes versus engineering/procedure fixes — instead of guessing which side of the automation pipeline to invest in.
- Established a reusable methodology: the metric was designed to be recomputed on demand for any channel or period, not just as a one-off calculation.

## Tech stack

Python · pandas · SQL · ETL · BI reporting tools

---

<sub>Individual project completed as part of a Data Analyst / Intent Analytics role. Described here for portfolio purposes; production code is not publicly available.</sub>
