# SPML Demand Forecasting — Emirates CHML Project

## Overview

Predicting **Child Meal (CHML)** demand for Emirates flights departing Scotland (GLA and EDI to DXB). The goal is to build a forecasting model that outputs recommended order quantities, reducing waste and stockouts.

## Problem Context

- Airlines must pre-order special meals based on expected demand
- CHML demand is driven primarily by **school holidays** (families travelling)
- Over-ordering = waste and cost; under-ordering = poor customer experience
- Current process: manual estimation from stock sheets (Excel-based workflow)

## Data

### Real Data (not included)
- Source: Work systems (cannot be shared due to IP concerns)
- Coverage: 1+ year, two flights, two cities
- Used for: Understanding patterns, informing synthetic data design

### Synthetic Data (this project)
- Generated in `Project.ipynb`
- **Routes:** GLA-DXB (Glasgow), EDI-DXB (Edinburgh)
- **Period:** 2024-01-01 to 2025-12-31 (2 years, 1,462 records)
- **Target variable:** `CHML_Ordered` (count)

### Key Features
| Feature | Description |
|---------|-------------|
| `Flight_Date` | Date of departure |
| `Route` | GLA-DXB or EDI-DXB |
| `Aircraft_Capacity` | 427 (GLA) or 259 (EDI) |
| `Load_Factor` | Proportion of seats filled (0.80–1.0) |
| `Actual_Pax_Count` | Passengers on flight |
| `Is_Weekend` | Saturday/Sunday flag |
| `Is_Holiday_Period` | Scottish school holiday flag |
| `CHML_Ordered` | **Target** — number of child meals ordered |

### Data Generation Logic
- Base demand (λ): 21 for GLA, 15 for EDI
- Holiday uplift: +19 for GLA, +13 for EDI
- Daily noise: uniform ±12
- Distribution: Poisson with dynamic λ
- Constraints: min 1, max 20% of pax

## Modelling Plan

### Phase 1: Baseline (NEXT STEP)
- [ ] Linear regression on synthetic data
- [ ] Features: `Is_Holiday_Period`, `Is_Weekend`, `Route`, `Load_Factor`
- [ ] Metrics: RMSE, MAE

### Phase 2: Appropriate Model
- [ ] Poisson regression (GLM with log link)
- [ ] Compare to linear baseline

### Phase 3: Evaluation
- [ ] Residual analysis (especially holiday vs non-holiday)
- [ ] Feature importance
- [ ] Train/test split or time-based validation

### Phase 4: Tool/App
- [ ] Excel-based output (matches existing workflow)
- [ ] Input: flight details → Output: recommended CHML quantity

## Files

| File | Purpose |
|------|---------|
| `Project.ipynb` | Synthetic data generation, exploration, modelling |
| `CHML_Data.ipynb` | Exploratory analysis of real data (reference only) |
| `README.md` | This file |

## Next Concrete Step

**Build the linear regression baseline in `Project.ipynb`:**
1. Regenerate the synthetic dataset (or save to CSV for reproducibility)
2. Train/test split (80/20 or time-based)
3. Fit `sklearn.linear_model.LinearRegression`
4. Evaluate on test set (RMSE, MAE)
5. Plot predicted vs actual

---

*Last updated: 2025-01-28*
*Status: Synthetic data complete. Modelling not started.*
