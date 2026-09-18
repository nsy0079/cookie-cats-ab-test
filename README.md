# Cookie Cats A/B Test

Mobile puzzle game experiment: should the first gate sit at level 30 or level 40?

**Decision:** keep `gate_30`. 7-day retention is significantly lower under `gate_40` (−0.82pp, p = 0.0016). Do not ship the change.

## Why this project

This is a portfolio analysis of the public Cookie Cats experiment (Tactile Entertainment / DataCamp). It follows a product-analytics workflow: metric design, retrospective sample-size check, SRM, two-proportion z-test, bootstrap CIs, and a skewed engagement metric via Mann–Whitney U.

Primary metric is pre-specified as 7-day retention. 1-day retention and rounds played are secondary and do not decide launch.

## Data source（公开数据说明）

`cookie_cats.csv` is a **public dataset**, not internal company data.

- Source: [Mobile Games A/B Testing - Cookie Cats (Kaggle)](https://www.kaggle.com/datasets/mursideyarkin/mobile-games-ab-testing-cookie-cats)
- Game: Cookie Cats by Tactile Entertainment. Players who installed during the experiment were randomly assigned to `gate_30` or `gate_40` (the first gate at **level** 30 vs 40). A gate makes the player wait or make an in-app purchase.
- This repo is an independent portfolio analysis. I do not own the dataset or the game; see the Kaggle page for license and terms.

## Data dictionary

| Field | Meaning (from the dataset docs) |
| --- | --- |
| `userid` | Unique player id |
| `version` | `gate_30` = control (gate at level 30); `gate_40` = treatment (gate moved to level 40) |
| `sum_gamerounds` | Game **rounds** played in the first **14 days** after install (not the level reached) |
| `retention_1` | Came back **and played** 1 day after install |
| `retention_7` | Came back **and played** 7 days after install |

File: [`data/cookie_cats.csv`](data/cookie_cats.csv) (90,189 rows before dropping one extreme outlier).

## How to run

```bash
python -m pip install -r requirements.txt
jupyter notebook cookie_cats_ab_test.ipynb
```

Run cells from top to bottom. Data is loaded from `data/cookie_cats.csv`.

## Results (ITT)

After dropping one extreme outlier (49,854 rounds):

| Metric | gate_30 | gate_40 | Diff | p / note |
| --- | --- | --- | --- | --- |
| 1-day retention | 44.82% | 44.23% | −0.59pp | p = 0.074; CI still allows ~1pp drop |
| 7-day retention | 19.02% | 18.20% | −0.82pp | p = 0.0016; 95% CI excludes 0 |
| 14-day rounds | median 17 | median 16 | −1 round | MWU p = 0.051; CLES ≈ 0.50 |

Bootstrap percentile CIs match the z-test. Mild SRM (p = 0.0085, split 49.6% / 50.4%) is flagged as a risk, not treated as a broken experiment.

## Limitations

- Assignment is at install; many users never play enough rounds to see a gate. The reported effect is ITT (effect of assignment), not “effect among those who saw the gate.”
- Sample size is a **retrospective** check using the observed control baseline, not a pre-experiment calculation.
- Pay conversion and uninstalls are not in this dataset (missing guardrails).
- `sum_gamerounds` is a proxy for reaching a level, not exact stage progress.

## Stack

Python, pandas, statsmodels, scipy, matplotlib.
