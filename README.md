# Mini-Project 1: UBOS District Population Forecaster

OOP with Python, Assignment, Advent 2026 (MSCS & MSDS). This repository covers **Mini-Project 1** only.

Forecasts 2025-2029 population for five Ugandan districts (Kampala, Wakiso, Gulu, Mbarara, Jinja), selects the best model by validation on a held-out period, and converts the forecast into primary-school classroom needs.

## Setup and run

```bash
python -m venv .venv && source .venv/bin/activate     # Windows: .venv\Scripts\activate
pip install -r requirements.txt

pytest                                   # 28 unit tests
jupyter notebook project1_population.ipynb   # then Kernel > Restart & Run All
# or headless:
jupyter nbconvert --to notebook --execute --inplace project1_population.ipynb
```

Python 3.10+ is required. The only random step (the bootstrap) is seeded with `np.random.default_rng(42)`, so results are reproducible.

## Repository layout

| Path | Purpose |
|---|---|
| `project1_population.ipynb` | The analysis: every core task, both extensions, and the Findings & Limitations section |
| `src/district.py` | `DistrictPopulation`: validated arrays, `__repr__`, `__len__`, stats, growth, CAGR, train/test split |
| `src/forecasters.py` | Abstract `Forecaster` + `LinearTrendForecaster`, `CAGRForecaster`, `FibonacciRatioForecaster` |
| `src/metrics.py` | MAE, RMSE, MAPE, `compare_models`, `select_best` |
| `src/bootstrap.py` | Residual-bootstrap 95 % prediction intervals (extension) |
| `src/planning.py` | Classroom calculations |
| `src/plotting.py` | The multi-panel forecast figure |
| `src/data.py` | The population series |
| `tests/` | pytest suite (28 tests: normal cases and edge cases) |
| `figures/` | Saved output figure |

## Summary of findings

- **Best model:** CAGR in all five districts (hold-out MAPE 0.4-2.5 %), about 1.5-7x more accurate than a linear trend. The Fibonacci-ratio model is off by 36-40 % and is not usable.
- **Fastest growing:** Wakiso (CAGR 6.47 %). Kampala and Gulu tie exactly at 4.61 % because both grew by a factor of 1.5.
- **2029 forecast (thousands):** Kampala 2,255; Wakiso 2,285 (overtakes Kampala); Gulu 601; Mbarara 854; Jinja 769.
- **Classrooms needed by 2029** (18 % primary-age, 53 pupils per classroom): Wakiso 2,088; Kampala 1,544; Mbarara 539; Jinja 421; Gulu 412; about 5,000 in total.
- **Forecast variance** is 41-59 % of the historical variance only because the forecast is shorter and has no noise, not because the future is more certain.

## Assumptions

- Populations are in thousands. Kampala, Wakiso and Gulu are from the brief; **Mbarara and Jinja are illustrative (synthetic) series I added**. No real data was substituted, so no extra credit is claimed for that.
- Train 2015-2021, test 2022-2024, selection by lowest MAPE (the same winner results under MAE and RMSE).
- "Additional classrooms" = classrooms needed in 2029 minus classrooms needed in 2024, each rounded up, assuming current classrooms exactly meet current need.
- The Fibonacci-ratio model applies each ratio F(k+1)/F(k) to the same last value by default (`compound=False`); a compounded variant is also implemented and discussed.

## Extensions attempted

1. Residual-bootstrap 95 % prediction intervals (2,000 resamples), shaded on the figure.
2. Critical assessment of the Fibonacci-ratio model.

## Limitations

See the *Findings & Limitations* section at the end of the notebook. In short: illustrative smooth data, a three-point test set, one constant growth rate, bootstrap intervals that are too narrow, and classroom figures that ignore any existing backlog.

## AI-use declaration

I used an AI assistant (Claude, by Anthropic) while working on this project: to compare two of my draft notebooks, to restructure the draft code into the `src/` modules, to write the pytest tests, and to help draft the written analysis. I checked the results against independent calculations (the "Sanity checks" section of the notebook uses `scipy.stats.linregress` and hand calculations) and I can explain any line of the code in a viva.
