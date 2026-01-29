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
- Generated in `Fake Data Generator.ipynb`
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

### Phase 0: Exploratory Data Analysis
- [ ] Explore synthetic data distribution and patterns
- [ ] Understand feature relationships
- [ ] See `EDA_GUIDE.md` for checklist

### Phase 1: Baseline
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
| `Fake Data Generator.ipynb` | Generates synthetic CHML demand data |
| `Data_Exploration.ipynb` | EDA and analysis of synthetic data |
| `EDA_GUIDE.md` | Step-by-step guide for exploratory data analysis |
| `CHML_Data.ipynb` | Exploratory analysis of real data (reference only, not for public use) |
| `README.md` | This file |

## Next Concrete Step

**Complete the EDA in `Data_Exploration.ipynb`:**
1. Follow the checklist in `EDA_GUIDE.md`
2. Understand the target distribution
3. Identify which features matter most
4. Document findings

**Then:** Build the linear regression baseline.

---

*Last updated: 2026-01-29*  
*Status: Synthetic data complete. EDA in progress.*
