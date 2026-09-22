# AMES Mawārith Validation — Mathematical Proof of Engine Correctness

**Project 4 of AMES (Algorithmic Mawārith Execution System)** — a formal validation suite that proves, using number theory and linear algebra rather than spot-checking, that the Fiqh al-Mawārith engine satisfies five correctness constraints across all 20 PCIED cases and 5,000 simulated cases from [Project 2](../ames-mawarith-simulation). This wasn't a formality: the process caught two real bugs that had been silently producing wrong distributions.

## The five validation pillars

| Pillar | Check | Purpose |
|---|---|---|
| **1. Sum constraint** | `sum(shares) == 1` exactly, via `Fraction` arithmetic | No estate fraction lost or invented |
| **2. Non-negativity** | every share ≥ 0 | No heir receives a negative inheritance |
| **3. Asl al-Masala minimality** | reported base == true `lcm(denominators)` | Confirms the classical Fiqh convention of using the smallest whole-number base |
| **4. Matrix system A·x = b** | identity matrix + sum-row, solved exactly in ℚ | Confirms the distribution is an exact, solvable linear system |
| **5. Qur'anic fraction membership** | base Furūḍ fractions ⊆ {1/2, 1/3, 1/4, 1/6, 1/8, 2/3} | Confirms no invented fraction outside Surah An-Nisā' (4:11–12, 176) |

## Bugs this validation found and fixed

**Bug 1 — Son-only ʿAṣaba branch missing.** When a son was present with no daughter, the residue-assignment logic had no matching branch, so the son silently received 0 instead of the full residue. Affected 363/5,000 simulated cases (7.3%).

**Bug 2 — Spouse-only Radd fallback missing.** When a spouse was the sole heir (no children, parents, or siblings), the engine correctly excluded them from sharing surplus with *other* Furūḍ heirs — but had no fallback for when no other heir exists at all, leaving the remainder unassigned. Affected 163/5,000 cases (3.3%).

| Stage | Failures (of 5,000) | Failure rate |
|---|---|---|
| Initial engine v2 | 526 | 10.52% |
| After Fix 1 (son ʿAṣaba) | 163 | 3.26% |
| After Fix 2 (spouse Radd) | 0 | 0.00% |

Neither bug showed up in the 20 curated PCIED cases — they only surfaced once the engine was required to satisfy `sum(shares) == 1` across thousands of randomly generated family structures. That's the core finding: hand-picked test cases, however carefully chosen, can't guarantee edge-case coverage the way exhaustive constraint-checking at scale can.

## Contents

| File | Description |
|---|---|
| `Project4_validation_engine.py` | Runs all 5 pillars against both datasets; builds the A·x=b matrix system |
| `mawaarith_engine_v2.py` | The engine under test — **includes both bug fixes** described above |
| `notebooks/Project4_Validation.ipynb` | Full validation walkthrough, bug write-ups, matrix example (Case 5) |
| `Project4_Dashboard.html` | Interactive results dashboard |
| `images/Project4_fig1_validation_summary.png` | Bug discovery & resolution — failure rate by fix stage, root-cause breakdown, final pass rates |
| `images/Project4_fig2_asl_matrix.png` | Asl al-Masala per case vs. verified LCM, and exact sum verification (zero floating-point error) |
| `data/PCIED_2023_cases.md` | Source dataset (shared across AMES projects) |
| `reports/Project4_Validation_Report.docx` | Full written report |

## Result

All 20 PCIED cases pass all 5 pillars. All 5,000 simulated cases pass post-fix, at 0.00% failure.

![Bug discovery and resolution](images/Project4_fig1_validation_summary.png)

## Running it

```python
from validation_engine import run_full_validation
results = run_full_validation()
```

Requires `mawaarith_engine_v2` (included, with fixes) and `simulation_results.json` from Project 2 in the working directory.

## Author

Abdulbasit A. Adedeji (Data Ustadh)
