# Sustainability A/B Testing: Green Nudge for Paperless Billing

## Problem

TD wants to increase paperless billing adoption as part of its sustainability strategy. One proposed lever is a "green-themed" nudge message shown to customers in their online banking portal. Before rolling it out broadly, the launch decision needs answers to four questions:

1. **Does the nudge causally increase paperless enrolment**, and by how much?
2. **Is the effect big enough to act on**, or could an observed lift just be noise from an underpowered test?
3. **Does it work better for some customer segments than others** (age, channel), pointing to a targeted rather than broad rollout?
4. **Does it come at a cost**, such as more unsubscribes or complaints, that would make it a bad trade even if adoption goes up?

The goal is a complete, decision-ready experimentation workflow, not just a significance test: was the experiment set up correctly, did it work, was it safe, and who did it work best for.

## Data

- **Synthetic dataset** generated in the notebook (no proprietary TD data): ~20,000 customers randomly assigned 50/50 to control (standard messaging) or treatment (green nudge), 10,000 per group.
- **Customer attributes:** segment (youth / affluent / mass), preferred channel (push / SMS / email), and age, with realistic variation across groups.
- **Outcomes:** paperless enrolment (primary), unsubscribe rate and complaint rate (guardrails).
- A treatment effect is built into the simulation (stronger for younger customers and push-notification users) so the experiment has something real to detect.

## Methodology

- **Power / sample-size analysis:** calculated the minimum sample size needed to detect a 2.5-percentage-point lift at 80% power and checked the actual sample against it. This is what determines whether a "no significant effect" result means the nudge doesn't work, or just that the test was too small to tell.
- **Sample Ratio Mismatch (SRM) check:** chi-square test confirming the 50/50 randomization held, plus covariate balance checks (segment, channel) across groups. Without this, an observed "effect" could just be broken randomization.
- **Primary analysis:** two-sample proportions z-test on paperless enrolment, plus a logistic regression controlling for segment, channel, and age for a more robust effect estimate.
- **Guardrail metrics:** z-tests on unsubscribe rate and complaint rate, to check the nudge isn't causing enough friction to outweigh the adoption gain.
- **Subgroup analysis:** conversion broken out by customer segment and preferred channel.
- **Tools:** Python, pandas/NumPy, SciPy/statsmodels (proportions test, logistic regression, power analysis), matplotlib.

## Results

| Check | Result |
|---|---|
| Power analysis | Required: 1,654 customers/group vs. actual: 10,000/group → **adequately powered** (6x the minimum needed) |
| SRM check | p = 0.562 → randomization held; segment and channel balanced across groups |
| Primary lift | Control: 8.71%, Treatment: 11.49%, uplift: **+2.79 pp** (+32% relative), p < 0.001 |
| Logistic regression (adjusted) | Treatment odds ratio: **1.36** (36% higher odds of conversion, holding segment/channel/age constant), p < 0.001 |
| Guardrail: unsubscribe rate | A: 0.34%, B: 0.39%, p = 0.291 → **pass** |
| Guardrail: complaint rate | A: 0.18%, B: 0.18%, p = 0.510 → **pass** |
| Subgroup: segment | Youth: +4.12 pp (+54% relative) · Affluent: +2.96 pp (+27%) · Mass: +2.30 pp (+28%) |
| Subgroup: channel | Push: +3.53 pp (+38%) · SMS: +3.24 pp (+56% relative, small base) · Email: +2.40 pp (+28%) |

Age itself was not a significant predictor in the regression (p = 0.947) once segment is controlled for; the segment variable captures the age-related differences in response.

## Key takeaways

- **Roll out the nudge broadly.** The lift is statistically significant (+2.79 pp, +32% relative), the test was well powered (6x the minimum sample), and neither guardrail moved. The effect is real, large enough to matter, and doesn't come at a customer-experience cost.
- **Sequence the rollout by segment and channel.** Youth customers (+54% relative) and push-notification users (+38%) respond meaningfully more than other groups. If rollout has to be phased, start there for the fastest measurable impact.
- **SMS needs its own follow-up test.** It shows the largest relative lift (+56%) but is only ~5% of customers, so it's the least precise estimate. It's worth a dedicated, adequately powered test before treating it as a confirmed high performer.
- **Significance alone isn't a launch decision.** Power, SRM, and guardrail checks are what make the result trustworthy enough to ship.

## How to run

1. Clone the repo and install Python with `pandas`, `numpy`, `scipy`, `statsmodels` and `matplotlib`.
2. Open [`AB Sustainability Testing Notebook.ipynb`](<AB Sustainability Testing Notebook.ipynb>) and run all cells in order. It generates its own synthetic data, so no downloads are needed.

## Repo structure

```
├── AB Sustainability Testing Notebook.ipynb   # analysis notebook (synthetic data generated in-notebook)
├── AB Sustainability Report.pdf               # write-up
└── README.md
```
