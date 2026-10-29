
# Experimentation and Causal Inference Platform

Checks whether a change really caused a result or whether it happened by luck. Covers A/B testing, multiple-testing correction, power analysis, sequential testing, Bayesian testing, propensity matching, difference-in-differences, and causal estimates with DoWhy and EconML. Results are on simulated data where the true effect is known, so each method can be checked against the right answer.

**Live app:** [Open the A/B test checker](https://ab-frontend-ojtz.vercel.app)
**Backend API docs:** [Open the Swagger page](https://ab-backend-15wf.onrender.com/docs)

## Methods and results

| Topic | Method | Result | True value |
|---|---|---|---|
| A/B test | Two-proportion z-test | Gap 1.94 pts, p = 0.0018, 95% CI 0.72 to 3.16 | 2 pts |
| Multiple tests | Bonferroni, Benjamini-Hochberg | With one real effect among 20 tests, both corrections kept it and removed the false win | n/a |
| Power | Sample size calculator | 10% to 12%: 3,835 per group. 10% to 11%: 14,744 per group | n/a |
| Peeking | Checks at every 500 visitors | 188/1000 false wins when peeking. 49/1000 with one look. 19/1000 with a stricter rule (p < 0.005 at each look) | 5% target |
| Bayesian | Beta-Bernoulli model (PyMC) | P(B beats A) = 0.999, average lift 1.95 pts | 2 pts |
| Propensity matching | Nearest-neighbour matching | Naive gap 0.128, matched gap 0.047 | 0.05 |
| Difference-in-differences | Before/after, treated vs control | 3.84 | 4 |
| Causal estimates | DoWhy (backdoor regression), EconML (DML) | Both 0.06 | 0.05 |

## Key takeaways

- A raw comparison can be badly biased. In the confounded simulation, the naive gap was 0.128, and adjusting for engagement brought it close to the true 0.05.
- Looking at results repeatedly inflates false wins. Checking at every step turned a 5% error rate into about 19%.
- Smaller effects need far more visitors. A 1-point change needs about 3.8 times the sample of a 2-point change.

## Project structure

- `Experimentation_Platform.ipynb`: all analysis, with markdown notes for each method
- `ab_api.py`: FastAPI backend that runs the A/B test
- `ab-frontend`: React page that calls the backend (see the [frontend repo](https://github.com/yourusername/ab-frontend))

## Setup

```
pip install fastapi uvicorn scipy numpy
uvicorn ab_api:app --reload
```

## Limitations

- All results come from simulated data. The methods were checked against known true effects, not real customer data.
- The p-value doesn't measure how big an effect is. Report the confidence interval alongside it.
- Propensity matching only adjusts for variables we measured. A hidden confounder would still bias the result.
- Difference-in-differences assumes both groups would have followed the same trend without the change.
- The Bayesian result depends on the flat prior.
- The sequential rule is a simple stricter threshold, not a full sequential boundary method.
