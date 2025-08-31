# Insurance Payout Modeling for U.S. Federal Flood Claims

---

## Introduction
This project models building-level flood insurance payouts using FEMA’s NFIP Redacted Building Claims dataset to produce interpretable, policy-relevant estimates of how hazard severity, insured values, and policy design translate into compensation. The analysis begins with careful data preparation—cleaning, outlier control, and variance-stabilizing transforms—and then progresses from an OLS baseline to an IV-2SLS specification to address endogeneity in key regressors. Throughout, we place special emphasis on testing, documenting, and remedying assumption failures so that inference remains credible for decision-making by insurers and public agencies.

---

## Background
Flood payouts are shaped by a mix of engineering reality and human choice: deeper inundation drives non-linear repair costs, higher-value structures tend to receive larger settlements, and coverage limits reflect both risk and selection. In such settings, naïve linear models can mislead because they ignore curvature, heteroskedasticity, spatial dependence, and the possibility that insured value and coverage are themselves influenced by unobservables that also affect payouts. Our approach therefore combines transparent feature engineering (log transforms, signed-log water depth) with a rigorous assumption-testing workflow and an IV strategy that isolates the causal content of economic drivers where OLS proves inconsistent.

---

## Problem Statement
We aim to estimate an interpretable payout function for flood-damaged buildings that remains valid when classical linear model assumptions are imperfectly met. Concretely, we seek to quantify elasticities with respect to water depth, building value, and coverage, while correcting for endogeneity in value and coverage; to stabilize inference in the presence of heteroskedasticity; and to diagnose spatial autocorrelation that may call for future spatial extensions. Success is defined by statistically sound, economically sensible coefficients and well-behaved residuals after targeted remedies, not by maximal R² alone.

---

## Dataset
We use the FEMA NFIP Redacted Building Claims v2 dataset, spanning individual U.S. flood loss events from 1978–2017. After excluding records with missing or zero values in building value or payout and trimming extreme leverage points, the working sample contains roughly 234k claims with rich covariates: payout amount, building property value, policy coverage limit, observed water depth (including below-grade readings), post-FIRM status, small-business indicator, coordinates for spatial context, and temporal controls. To mitigate heavy right-skew, we log-transform payout and property value, apply a signed-log to water depth, and derive controls such as coverage-to-value ratios and decade dummies.

---

## Assumption Handling
Linearity in parameters is challenged by RESET, which rejects the null even after applying log and signed-log transformations; although a higher-order model briefly lifts R², it introduces 120+ terms with persistent misspecification risk, so we retain a parsimonious linear form with robust inference rather than overfit curvature.
Exogeneity fails for total building coverage and log property value by Wu–Hausman, motivating IV-2SLS; instruments (building age, small-business flag, year-of-loss) are empirically strong in first-stage tests and pass Hansen’s J, supporting relevance and exogeneity while delivering consistent second-stage estimates.
Heteroskedasticity is pronounced by Breusch–Pagan, so we adopt heteroskedasticity-consistent (HC0) standard errors in both OLS and IV, preserving valid p-values without altering coefficients; WLS/GLS are not used because exogeneity violations make their guarantees inapplicable in this context.
Together, this test-and-repair loop moves the specification from a fragile OLS to a defensible IV model with robust inference, while documenting any remaining risks.

---

## Methodology
We begin with an OLS baseline on log payout using signed-log water depth, log property value, coverage, post-FIRM status, building-age bins, decade dummies, and ancillary controls. Diagnostics guide targeted fixes: transforms and selective outlier removal materially improve fit (R² rising from ~0.38 to ~0.87), but formal tests still uncover endogeneity and heteroskedasticity; consequently we switch to IV-2SLS with the instrument set above, verify first-stage strength (partial-F well above 10), and validate overidentifying restrictions. All inference is conducted with HC standard errors; we retain the parsimonious linear structure for transparency and to avoid overfitting suggested by RESET on higher-order expansions.

---

## Feature Engineering
We transform payout and property value with natural logs to tame right-skew, stabilize variance flagged by Breusch–Pagan, and make elasticities directly interpretable. Water depth is encoded with a signed-log transform sgn(x)log(1+∣x∣) so that below-grade inundation (negative depths) and above-grade flooding map smoothly while reducing the curvature that caused RESET failures under raw levels. Coverage enters as both log(coverage) and a coverage-to-value ratio to capture binding policy limits; we trim extreme leverage points and winsorize narrow tails where coding artifacts create implausible ratios. Temporal structure is absorbed with coarse time controls (e.g., decade dummies) to separate secular price drift from event-level variation, and categorical indicators such as post-FIRM status and small-business flags provide policy and occupancy context. We center and scale continuous regressors, monitor VIF/condition numbers to guard against multicollinearity—especially between log(value) and log(coverage)—and we test selective interactions (e.g., depth × post-FIRM) only as robustness checks, retaining a parsimonious main-effects design in the final IV model to avoid overfitting while keeping coefficients stable, interpretable, and assumption-conformant.

---

## Results & Discussion
IV estimates yield economically coherent elasticities: payouts rise with flood severity and insured value, though the latter’s effect attenuates relative to OLS once selection bias is corrected; coverage remains positive but smaller under IV, consistent with policy limit choices being endogenous in OLS. Post-FIRM structures are associated with lower payouts, aligning with stronger codes and mitigation, while decade controls capture secular inflation and cost drift. The specification does not fully eliminate spatial dependence, reinforcing our call for spatial extensions; nonetheless, with endogeneity addressed and HC inference applied, coefficient signs and magnitudes are stable and interpretable, supporting cautious use for scenario analysis and policy diagnostics.

---

## Conclusion & Future Scope
This work shows that credible insurance payout modeling hinges as much on assumption stewardship as on raw fit: we document misspecification, correct endogeneity with IV-2SLS, stabilize inference under heteroskedasticity, and flag spatial dependence for future modeling. Immediate next steps include adding spatial fixed effects or spatial error structures, exploring flexible semi-parametrics to capture remaining curvature without overfitting, and enriching instruments and covariates with geophysical features (elevation, floodplain class) to further isolate causal channels. The resulting toolkit is a transparent, extensible baseline for actuaries and public risk managers seeking defensible estimates rather than fragile correlations.

---

## Contributors
- Anirudh A
- Balaji M
- Hari Srijaa
- Sai Krishna
- Sankalp Davi

---
