Rowa Alhassnan
Time Series Analysis and Forecasting Project Report
Dataset: International Air Passengers (1949–1960) | Evaluation Horizon: 12 Months (H=12)
Harness Design: 8-Window Rolling Origin Cross-Validation | Benchmark Baseline: Seasonal Naïve

1. Benchmark & Model Performance Comparison
Models were evaluated across 8 historical origins using a rolling 12-month horizon to ensure robust testing.

| Model | MASE | RMSSE | CRPS (Prob. Fit) | 80% Coverage | Mean Interval Width | Min MASE | Max MASE |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Seasonal Naïve (Floor) | 1.3133 | 1.2456 | 0.0616 | 51.04% | 118.2 passengers | 0.4116 | 1.9587 |
| AutoETS (Recommended) | 0.9511 | 1.0028 | 0.0499 | 48.69% | 84.5 passengers | 0.5648 | 1.5543 |

Key Performance Drivers
* Point Accuracy: AutoETS achieved superior point error scores (MASE: 0.951 vs 1.313; RMSSE: 1.003 vs 1.246), proving that modeling exponential trend and multiplicative seasonality adds predictive power beyond simple historical repetition.
* Distributional Fit: The Continuous Ranked Probability Score (CRPS)—which evaluates the overall accuracy of the entire probabilistic forecast range rather than just the center line—improved from 0.0616 to 0.0499.

Chart 1: Production Forecast vs. Benchmark Floor
(Line chart demonstrating AutoETS closely tracking the expanding summer peaks of 1960, whereas Seasonal Naïve underpredicts peak demand by copying 1959 levels directly.)

2. Prediction Intervals & Uncertainty Calibration
While AutoETS dominates center-line accuracy, its prediction intervals present an operational risk:
* Bandwidth & Empirical Coverage: AutoETS produced an average 80% prediction interval width of 84.5 passenger units, yielding an empirical coverage rate of 48.69% (against the target 80%). The baseline floor yielded an interval width of 118.2 units and 51.04% coverage.
* Honesty Verdict (Overconfident): AutoETS prediction bands are unnaturally narrow and overconfident. While the center-line forecast is accurate, relying solely on these bounds for operational capacity planning (e.g., flight scheduling, staffing) introduces downside risk because the model severely underestimates extreme variance.

3. Residual Diagnostics: What the Model Missed
Evaluating residual structure reveals why interval calibration failed:
* Seasonal Naïve Residuals: Ljung-Box autocorrelation tests yielded p<0.05 at lags 12 and 24, confirming uncaptured systematic trend and seasonal expansion.
* AutoETS Residuals: AutoETS with multiplicative error and seasonality (M,A,M) successfully removed trend and seasonal autocorrelation. However, residual inspection reveals heavy-tailed, non-Gaussian variance spikes during mid-year peak travel months.
* Missing Structural Drivers: AutoETS treats residual variance as stationary homoscedastic noise. It fails to account for exogenous macroeconomic shifts and structural capacity caps, causing it to underprice tail-risk uncertainty and underestimate interval width.

Chart 2: Residual Distribution & Autocorrelation (ACF)
(ACF plot showing AutoETS residuals within white-noise boundary limits, alongside a histogram illustrating heavy-tailed residual distribution relative to a standard Gaussian curve.)

4. Proposed Next Step (One Change)
To correct interval under-coverage and capture external variance, propose implementing Dynamic Regression with Exogenous Drivers:
* Driver Sourcing: Integrate monthly regional weather metrics (e.g., mean monthly temperature via Open-Meteo API) and jet fuel price indices as exogenous regressors.
* Modeling Discipline: Fit an AutoARIMA model with external regressors across the identical 8-window rolling-origin harness, ensuring zero temporal leakage.
* Expected Impact: Incorporating exogenous demand drivers will account for macro travel variance, expanding prediction interval widths to bring empirical coverage closer to the target 80%.
