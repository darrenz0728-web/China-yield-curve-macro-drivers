# China Government Bond Yield Curve: Macro Drivers Analysis

**Author**: Darren Zhao | Duke University, Master of Art in Economics  
**Advisor**: Prof. Lawrence Kreicher | 2025.12 – 2026.03

---

## Overview

This project investigates the macro drivers of China's government bond yield curve using weekly data from January 2018 to February 2026 (426 observations). The analysis proceeds in two stages:

1. **Nelson-Siegel Kalman Filter** — Extract three latent factors (level, slope, curvature) from 10-maturity yield data
2. **VECM + IRF** — Identify long-run cointegration relationships and monetary policy transmission paths

**Key finding**: China's yield curve structure is primarily anchored by monetary policy (OMO7D and DR001), not inflation — a structurally different regime from the US and Europe.

---

## Main Results

| Metric | Value |
|--------|-------|
| Out-of-sample directional accuracy | **81.8%** (benchmark: 53.8%) |
| In-sample RMSE | **0.048%** |
| Cointegration rank (Johansen) | **r = 3** |
| Sample period | Jan 2018 – Feb 2026, weekly |

### Monetary Policy Transmission (IRF, 16-week horizon)

- **DR001 → slope factor**: Peak impulse response of **+0.038%**, confidence band never crosses zero — the strongest and most persistent effect in the system
- **OMO7D → level factor**: Statistically significant but roughly **8× smaller** than DR001's effect on slope
- **CPI → level factor**: Negative and significant — reflecting China's supply-side inflation structure, not demand-driven
- **US10Y**: Enters short-run dynamics but does **not** appear in any long-run cointegrating equation, confirming domestic policy dominance

### Long-Run Cointegrating Relationships

Three equilibria identified by Johansen trace test:
- **EC1**: Yield curve level (β₁) anchored to OMO7D in the long run — policy rate as the primary level anchor
- **EC2**: Slope (β₂) co-moves with DR001 and inflation cycle — short-end liquidity drives term spread
- **EC3**: Domestic-external interest rate linkage via US10Y

---

## Robustness Checks

- **Sub-sample split** (2018–2021 vs 2022–2026): β₂ error-correction coefficient sign consistent across both windows
- **Monthly frequency** (no forward-fill interpolation): α coefficients 6/6 sign-consistent with weekly baseline — core conclusions hold

---

## Repository Structure

```
├── NS_Kalman.ipynb          # Stage 1: Factor extraction via Kalman filter
├── VECM_IRF.ipynb           # Stage 2: Cointegration, IRF, robustness tests
├── report.pdf               # Full research report (Chinese)
├── data/
│   └── data_weekly.xlsx     # Weekly yield + macro data (iFinD)
└── output/
    ├── ns_factors.csv       # Extracted β₁, β₂, β₃ time series
    └── figures/             # IRF plots, factor time series, fit quality
```

---

## Methodology

### Stage 1 — NS-Kalman Dynamic Factor Model

The Nelson-Siegel (1987) model decomposes the yield curve as:

$$y(\tau) = \beta_1 + \beta_2 \cdot \frac{1 - e^{-\lambda\tau}}{\lambda\tau} + \beta_3 \cdot \left(\frac{1 - e^{-\lambda\tau}}{\lambda\tau} - e^{-\lambda\tau}\right)$$

Factors are estimated via Kalman filter in state-space form, with noise parameters (σ_η, σ_ε) estimated by MLE. The decay parameter λ = 0.0609 (peak loading at ~16.4 years) is fixed at the MLE optimum.

### Stage 2 — VECM with Structural IRF

Seven I(1) variables: β₁, β₂, CPI, PPI, OMO7D, DR001, US10Y  
Two I(0) external variables: β₃ (curvature), PMI

Cholesky ordering reflects policy-to-market transmission timing:  
`OMO7D → DR001 → CPI → PPI → US10Y → β₁ → β₂`

Bootstrap confidence bands: 500 replications, 68% level (Sims & Zha convention)

---

## Policy Implications

1. **OMO7D remains the primary long-run level anchor** — rate cut cycles should be tracked as the dominant signal for duration positioning
2. **DR001 dominates slope dynamics** — liquidity conditions in the interbank market are a stronger near-term predictor of the yield curve shape than policy rate movements
3. **Inflation is not a binding constraint on rates** in the current regime — supply-side CPI dynamics push yields *down*, not up, unlike standard demand-pull transmission
4. **Global spillovers are short-run only** — US Treasury moves affect Chinese yields tactically but do not shift the long-run equilibrium

---

## Data Sources

- Government bond yields (3M–30Y, weekly close): iFinD
- OMO7D, DR001: People's Bank of China / iFinD
- CPI, PPI, PMI: National Bureau of Statistics
- US 10Y Treasury yield: Federal Reserve / iFinD

---

## Dependencies

```
pandas numpy scipy statsmodels matplotlib seaborn
```

```bash
pip install pandas numpy scipy statsmodels matplotlib seaborn openpyxl
```
