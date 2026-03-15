# Selective Dutch EPL Framework

**Companion code for:**
> *Exploiting the Joint Outcome Space of Paired Football Fixtures: An Analytical Dutch Framework for the English Premier League*
> Prince David Marcells
>
> **Status:** Submitted for peer review. Preprint submitted to SportRxiv.
> **DOI:** *(to be added upon preprint publication)*

---

## Overview

This repository provides the full Python implementation of a selective Dutch betting framework applied to same-day English Premier League fixture pairs. The framework operates over the **3×3 joint outcome space** formed by two paired fixtures — nine possible joint outcomes (HH, HD, HA, DH, DD, DA, AH, AD, AA) — and applies a combinatorial optimisation strategy to identify which outcome cells to exclude, then allocates Dutch stakes across the remaining cells.

The approach is entirely **frequentist and analytically transparent**: marginal outcome probabilities are derived from rolling head-to-head (H2H) frequency distributions, joint probabilities are computed by assuming fixture independence, and win rates are calculated exactly — with no simulation, no machine learning, and no variance. To the best of the author's knowledge, the systematic exploitation of the 3×3 joint outcome space for paired same-day EPL fixtures via combinatorial exclusion enumeration is novel in the academic sports analytics literature.

---

## Repository Structure

```
Selective-Dutch-EPL-Framework/
│
├── EPL_Selective_Dutch_Framework_Implementation.ipynb   # Main notebook (Google Colab)
├── data/                                                # Downloaded EPL season CSVs
├── figures/                                             # Output visualisations
├── LICENSE                                              # MIT
└── README.md
```

---

## Methodology

The pipeline proceeds in seven logical sections, all contained within the single notebook:

**1. Data Download & Load**
Pulls EPL match data for seasons 2010/11–2025/26 from football-data.co.uk. Retains match date, teams, full-time result (FTR), and bookmaker odds (Bet365, Pinnacle, William Hill).

**2. H2H Probability Estimation**
For each fixture, a rolling pre-match H2H record is extracted — only matches strictly before the fixture date are used, preventing any look-ahead bias. Marginal probabilities for Home win (H), Draw (D), and Away win (A) are derived from normalised H2H frequency counts. A minimum H2H game threshold (default: 10) is enforced as a selection filter.

**3. Exclusion Configuration Generation**
All C(9, 2) = 36 configurations for excluding exactly 2 of the 9 joint outcome cells are enumerated via `itertools.combinations`. Each configuration specifies which 7 cells remain covered.

**4. Analytical Win Rate Computation**
For each configuration, the exact joint win probability is computed as:

```
W(cfg) = Σ pA[r1] × pB[r2]  for all (r1, r2) ∈ remaining(cfg)
```

This is deterministic and requires no Monte Carlo simulation.

**5. Dutch Stake Allocation**
For the highest-ranked configuration, parlay odds are computed for each covered outcome pair by multiplying the independent bookmaker odds. Stakes are allocated via inverse-odds weighting. A configuration is classified as a profitable Dutch book when the sum of implied probabilities across covered cells is less than 1.0.

**6. Main Evaluation Pipeline**
All same-day fixture pairs in the target window (2020/21–2025/26) satisfying the H2H threshold are processed. Each pair records: configuration rank, excluded cells, analytical win rate, actual outcome, Dutch profitability, and margin. Full results are saved to `epl_analytical_results.csv`.

**7. Visualisations**
A suite of dark-theme diagnostic charts is produced and saved to `figures/`, including:
- Actual win rate over time (rolling and cumulative)
- Analytical win rate distribution
- Dutch margin distribution
- Exclusion frequency heatmap over the 3×3 joint outcome space
- MIN_H2H_GAMES sensitivity sweep

---

## Key Results (from embedded notebook output)

| MIN_H2H | Pairs | Actual Win Rate | Model Win Rate | Overconfidence | Profitable Dutch | Avg Margin |
|---------|-------|----------------|----------------|----------------|-----------------|------------|
| 10      | 169   | 88.17%         | 96.01%         | +7.85 pp       | 117 / 169       | 9.71%      |
| 12      | 59    | 88.14%         | 95.40%         | +7.26 pp       | 42 / 59         | 8.81%      |
| 15      | 2     | 100.00%        | 92.89%         | −7.11 pp       | 2 / 2           | 8.98%      |

---

## Running the Notebook

The notebook is designed to run in **Google Colab** with no local setup required.

1. Open `EPL_Selective_Dutch_Framework_Implementation.ipynb` in Google Colab.
2. Run all cells sequentially. Section 1 will automatically download all required season data from football-data.co.uk.
3. Visualisations (Section 7) require the pipeline (Section 6) to have completed and `epl_analytical_results.csv` to be present.

**Dependencies** (all available in the standard Colab environment):
`pandas`, `numpy`, `matplotlib`, `itertools`, `requests`, `collections`

---

## Configuration

Key parameters at the top of the notebook:

| Parameter        | Default    | Description                                      |
|------------------|------------|--------------------------------------------------|
| `MIN_H2H_GAMES`  | `10`       | Minimum prior H2H meetings required per fixture  |
| `NUM_EXCLUSIONS` | `2`        | Number of joint outcome cells to exclude         |
| `TOTAL_STAKE`    | `100.0`    | Total stake per Dutch book (arbitrary units)     |
| `MIN_STAKE`      | `0.20`     | Minimum stake per outcome cell                   |
| `TARGET_START`   | 2020-08-01 | Start of evaluation window                       |
| `TARGET_END`     | 2026-07-31 | End of evaluation window                         |

---

## Data Source

Match data is sourced from [football-data.co.uk](https://www.football-data.co.uk/), which provides freely available historical EPL fixture records including full-time results and bookmaker closing odds.

---

## Citation

If you use this code or the associated methodology in your work, please cite the accompanying paper (DOI to be added upon preprint publication):

```
Marcells, P.D. (2025). Exploiting the Joint Outcome Space of Paired Football Fixtures:
An Analytical Dutch Framework for the English Premier League.
[Preprint submitted to SportRxiv; under journal review.]
DOI: [to be added]
```

---

## License

This project is licensed under the **MIT License** — see [LICENSE](LICENSE) for details.
