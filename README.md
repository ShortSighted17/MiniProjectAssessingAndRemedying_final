# Assessing and Remedying Coverage for Datasets

A Python implementation of the algorithms and experiments from the paper  
**"Assessing and Remedying Coverage for a Given Dataset"**.

The project identifies *Maximal Uncovered Patterns* (MUPs) in a dataset and proposes minimal tuple additions to restore coverage, then reproduces four figures from the paper across three real-world datasets.

---

## Background

A dataset **covers** a pattern if the number of rows matching that pattern meets a minimum threshold τ.  
A **Maximal Uncovered Pattern (MUP)** is a pattern whose coverage falls below τ while every one of its generalizations is sufficiently covered — it is the most general description of an under-represented subgroup.

The system:
1. **Assesses** coverage gaps by enumerating MUPs.
2. **Remedies** them by suggesting the smallest set of new tuples that would bring every under-covered subgroup up to threshold.

---

## Project Structure

```
AssessingAndRemedyingCoverageForDatasets/
├── main.py                          # Interactive entry point
├── Algorithms/
│   ├── Mups/
│   │   ├── MutualFuncs.py           # Core pattern primitives + CoverageOracle + MupDominanceIndex
│   │   ├── TopDown.py               # pattern_breaker  — BFS top-down MUP finder
│   │   ├── BottomUp.py              # pattern_combiner — BFS bottom-up MUP finder
│   │   └── DeepDiver.py             # pattern_diver    — DFS MUP finder with level cap
│   └── Greedy/
│       ├── Greedy.py                # greedy_coverage_enhancement
│       └── GreedyHelper.py          # Inverted-index helpers + uncovered_patterns_at_level
├── Datasets/
│   ├── AdultIncomeDataSet.csv
│   ├── AirBnbListingsDatasets.csv
│   └── CompasDataset.csv
└── Experiments/
    ├── Figure10/                    # Effect of coverage gaps on classifier accuracy
    ├── Figure15/                    # MUP identification runtime vs. dimensions
    ├── Figure16/                    # Coverage enhancement runtime vs. threshold
    └── Figure17/                    # Coverage enhancement runtime vs. dimensions
```

---

## Algorithms

| Algorithm | Function | Strategy |
|---|---|---|
| **TopDown** | `pattern_breaker` | BFS from the all-wildcard root; expands covered nodes, marks uncovered nodes as MUPs |
| **BottomUp** | `pattern_combiner` | BFS from leaves upward; aggregates child counts to identify MUPs without full dataset scans |
| **DeepDiver** | `pattern_diver` | DFS that, upon finding an uncovered node, climbs to its maximal uncovered ancestor; supports an optional `max_level` cap for speed |
| **Greedy** | `greedy_coverage_enhancement` | Greedy set-cover that iteratively picks the tuple covering the most remaining uncovered patterns until all are hit |

All algorithms represent patterns as tuples where `None` is the wildcard `X`.

---

## Experiments

| Figure | Description |
|---|---|
| **Figure 10** | Shows that a model performs poorly on an under-covered subgroup (a MUP), and that adding more of that subgroup's records to the training set improves performance. |
| **Figure 15** | Runs DeepDiver with varying dimension counts and level caps; plots runtime on a log scale. |
| **Figure 16** | Runs the Greedy enhancer across a sweep of threshold rates; plots runtime vs. threshold. |
| **Figure 17** | Runs the full MUP + Greedy pipeline across varying dimension counts; plots runtime vs. dimensions. |

Output PNG files are saved alongside each experiment script.

---

## Getting Started

### Requirements

```
pandas
matplotlib
scikit-learn
```

Install with:


### Running

```bash
python main.py
```

The interactive menu lets you choose one or more datasets and one or more experiments to run:

```
Available datasets:
  1. AdultIncomeDataSet.csv
  2. AirBnbListingsDatasets.csv
  3. CompasDataset.csv

Pick dataset number(s) (blank for all):

Available experiments:
  10. Effect of lack of coverage on classification
  15. MUP identification - varying dimensions (DeepDiver, level-limited)
  16. Coverage enhancement - varying threshold
  17. Coverage enhancement - varying dimensions

Pick experiment number(s) (blank for all):
```

Separate multiple choices with spaces or commas (e.g. `1 3` or `10, 15`). Press Enter to run all.

---

## Datasets

| Dataset | Description |
|---|---|
| **Adult Income** | US Census income data; predicts whether income exceeds $50K |
| **AirBnB Listings** | Airbnb listing metadata; used with a derived binary high-price label |
| **COMPAS** | Recidivism scores; predicts two-year recidivism |

---

## Reference

> *Assessing and Remedying Coverage for a Given Dataset*  
> (see `Assessing and Remedying Coverage for a Given Dataset.pdf` in the project root)
