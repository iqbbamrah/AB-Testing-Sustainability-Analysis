# Sustainability A/B Testing Project

**Does a "green nudge" message in the online banking portal actually increase paperless billing enrolment and is it safe to ship?**

This project simulates and analyzes an A/B test answering that question, built around the type of experimentation work. It uses a synthetic dataset (no proprietary TD data) to demonstrate a complete, decision-ready experimentation workflow and not just a significance test, but the full set of checks a real launch decision would require: was the experiment set up correctly, did it work, was it safe, and who did it work best for.

## The business question

TD wants to increase paperless billing adoption as part of its sustainability strategy. One proposed lever: a "green-themed" nudge message shown to customers in their online banking portal, encouraging them to switch. Before rolling this out broadly, the question is:

1. **Does the nudge causally increase paperless enrolment**, and by how much?
2. **Is the effect big enough to be worth acting on**, or could an observed lift just be noise from an underpowered test?
3. **Does it work better for some customer segments than others** (age, channel), informing a targeted vs. broad rollout?
4. **Does it come at a cost** — more unsubscribes or complaints that would make it a bad trade even if adoption goes up?

## Approach

- **Synthetic data simulation:** ~20,000 customers randomly assigned to control (standard messaging) or treatment (green nudge), with realistic demographic and channel variation and a built-in treatment effect (stronger for younger customers and push-notification users) to make the experiment meaningful to analyze.
- **Power / sample-size analysis :** before treating any result as conclusive, calculates the minimum sample size needed to reliably detect a 2.5-percentage-point lift at 80% power, and checks the actual sample size against that requirement. The step that determines whether a "no significant effect" result means the nudge doesn't work, or just that the test wasn't big enough to tell.
- **Sample Ratio Mismatch (SRM) check:** chi-square test confirming the 50/50 randomization actually held, plus covariate balance checks (segment, channel) across groups, without this, any observed "effect" could just be a broken randomization.
- **Primary analysis:** two-sample proportions z-test on paperless enrolment, plus a logistic regression controlling for segment, channel, and age for a more robust effect estimate.
- **Guardrail metrics:** z-tests on unsubscribe rate and complaint rate, to check the nudge isn't causing enough friction to make it a bad trade even if adoption improves.
- **Subgroup analysis:** conversion broken out by customer segment and preferred channel, to see whether the effect is concentrated in specific groups.
- **Tech stack:** Python, pandas/NumPy, SciPy/statsmodels (proportions test, logistic regression, power analysis), matplotlib.

## Results

| Check | Result |
|---|---|
| Power analysis | Required: 1,654 customers/group vs. actual: 10,000/group → **adequately powered** (6x the minimum needed) |
| SRM check | p = 0.562 → randomization held; segment and channel balance confirmed across A/B (no meaningful skew) |
| Primary lift | Control: 8.71%, Treatment: 11.49%, uplift: **+2.79 pp** (+32% relative), p < 0.001 |
| Logistic regression (adjusted) | Treatment odds ratio: **1.36** (36% higher odds of conversion, holding segment/channel/age constant), p < 0.001 |
| Guardrail: unsubscribe rate | A: 0.34%, B: 0.39%, p = 0.291 → **pass**, no significant increase in unsubscribes |
| Guardrail: complaint rate | A: 0.18%, B: 0.18%, p = 0.510 → **pass**, no significant increase in complaints |
| Subgroup — segment | Youth: +4.12 pp (+54% relative) · Affluent: +2.96 pp (+27%) · Mass: +2.30 pp (+28%) — **youth responds most** |
| Subgroup — channel | Push: +3.53 pp (+38%) · SMS: +3.24 pp (+56% relative, small base) · Email: +2.40 pp (+28%) — **push responds most; SMS promising but low sample** |

*(Age itself was not a significant predictor in the regression (p = 0.947) once segment is controlled for — the segment variable is doing the real work of capturing age-related response differences, not raw age.)*

## Business recommendation

**Roll out the green nudge broadly.** The nudge produced a statistically significant 2.79 pp lift in paperless adoption (a 32% relative increase, p < 0.001), the experiment was well-powered to detect this effect (6x the minimum required sample), and both guardrail metrics held steady. No evidence the nudge increases unsubscribes or complaints. This is a clean "ship it" result: the effect is real, it's large enough to matter, and it doesn't come at a customer-experience cost.

**Prioritize rollout sequencing by segment and channel.** The effect is not uniform, youth customers (+54% relative) and push-notification users (+38%) respond meaningfully more than the affluent/mass segments or email channel. If rollout needs to be phased (e.g. for infrastructure or monitoring reasons), start with youth and push-notification customers for the fastest measurable impact, then expand to the broader base.

**the SMS channel** shows the largest *relative* lift (+56%) but represents only ~5% of the customer base in this simulation, so that subgroup estimate is the least precise of the group which is worth a dedicated, adequately powered follow-up test on SMS specifically before treating it as a confirmed high performer, rather than reading too much into a promising but noisy small-sample result.

## How to run

1. Clone this repository.
2. Open the Jupyter notebook (`AB_Sustainability_Testing_Notebook.ipynb`).
3. Run all cells in order — the notebook is fully self-contained and uses only synthetic, generated data.

## Repo structure
```
├── data/                       # (synthetic data is generated in-notebook, no external file needed)
├── notebooks/
│   └── AB_Sustainability_Testing_Notebook.ipynb
└── README.md
```
