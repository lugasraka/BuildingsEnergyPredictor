# EcoProphet MVP: Building Heating Load Regression

## Background
- Commercial and residential buildings drive a large share of energy consumption; better early-stage design decisions can cut heating demand and costs.
- This MVP explores fast, interpretable prediction of heating load using a lightweight linear model, suitable for embedding into a product workflow before investing in heavier models.

## Objective
- Predict building heating load (kWh/m²) from basic envelope and design parameters.
- Deliver a transparent baseline model plus visual diagnostics that quantify which design levers matter most.

## Data Source
- UCI Energy Efficiency dataset (ENB2012): https://archive.ics.uci.edu/ml/machine-learning-databases/00242/ENB2012_data.xlsx
- Local fallback copy: ENB2012_data.xlsx (kept alongside the notebook for offline runs).
- Target used in this MVP: `Heating_Load` (cooling load is dropped for focus).

## Methodology
- Environment: Python 3.10, key libs: pandas, numpy, seaborn, matplotlib, tensorflow/keras, scikit-learn.
- Features renamed for clarity: Relative_Compactness, Surface_Area, Wall_Area, Roof_Area, Overall_Height, Orientation, Glazing_Area, Glazing_Area_Distribution.
- Data split: 80% train / 20% test with random_state=0 for reproducibility.
- Normalization: keras `Normalization` layer fitted on training features to handle mixed units.
- Model: single-layer Dense regression (equivalent to multi-linear regression) trained with RMSprop; objective is MSE with MAE/MSE metrics. The learned mapping follows $y = w^T x + b$.
- Training: up to 50 epochs, batch_size=32, `EarlyStopping` on validation loss with patience=5 and best-weight restore.
- Diagnostics: correlation heatmap, training/validation loss curves, predicted vs. actual scatter plot, and a feature-weight bar chart derived from the fitted coefficients.

## Expected Outputs (once cells are run)
- Train/validation loss chart showing convergence under early stopping.
- Test metrics: MAE, RMSE, and R² on the 20% hold-out set (printed in the evaluation cell).
- Plots: correlation matrix; predicted vs. actual scatter with identity line; feature influence bar chart (absolute coefficient magnitudes).
- Coefficients and bias printed for interpretability.

## Business / Product Implications
- Fast feedback loop: the linear model is transparent and quick to score, ideal for early design iterations or embedding in UX where explanations matter.
- Feature influence highlights which levers (e.g., overall height, roof area, glazing choices) most affect heating demand, supporting design guidance and what-if tools.
- Baseline benchmark: establishes an interpretable yardstick before exploring richer models (e.g., gradient boosting or deep nets) or adding cost/CO₂ objectives.
- Deployment friendliness: low-latency scoring and minimal compute make it suitable for client-facing experiences and batch portfolio analysis.

## How to Reproduce
1. Open BuildingEnergyRegression-AI-MVP.ipynb in Colab or VS Code; ensure Python 3.10 kernel.
2. Install dependencies if needed: pandas, numpy, seaborn, matplotlib, tensorflow, scikit-learn (or simply `pip install -r requirements.txt`).
3. Run cells in order. If the remote URL fails, the notebook automatically falls back to ENB2012_data.xlsx in the repo.
4. Review printed metrics and generated plots at the end of the run.

## Quick Start (clone and run)
- Clone: `git clone <your-fork-url> && cd SideProject-EcoProphet`
- Install deps: `pip install -r requirements.txt`
- Open the notebook in Colab or VS Code and run all cells (see How to Reproduce above).

## Pre-commit / Pre-push Checklist
- Notebook runs top-to-bottom without errors; metrics and plots are generated.
- Outputs are intentional: clear or reset noisy cell outputs if not desired in git.
- Data file ENB2012_data.xlsx is present if you need offline runs (note size considerations for git).
- README.md is up to date; adjust text if you change modeling choices or metrics.
- Dependencies captured (requirements.txt/poetry/conda) if this leaves notebook-only usage.

## Next Ideas (optional)
- Add cooling-load target as a second head or a multitask variant.
- Benchmark against tree-based models (RandomForest, XGBoost, LightGBM) and compare MAE/R².
- Incorporate cost/CO₂ estimates per kWh for business-facing KPIs and UI messaging.
