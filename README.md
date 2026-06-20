# US-Flight-Delays-Analysis (2019, 2022-2026)
An end-to-end data analytics project analyzing over 36 million US domestic flight records from the Bureau of Transportation Statistics (BTS) to understand, visualize, forecast, and predict flight delay rates. The pipeline spans exploratory data analysis, SQL analytics, interactive dashboards, time-series forecasting, and machine-learning classification.

# Overview
Using six years of Bureau of Transportation Statistics (BTS) data, this project investigates the share of delayed flights among total flights, when and where US flights are delayed, the severity of delays, and how well delays can be predicted. The analysis covers 2019 and 2022–2026, deliberately excluding 2020–2021 due to COVID-19 travel anomalies that would distort delay patterns.
The project moves through five stages:
1. **Data engineering** — combining and cleaning 63 monthly BTS files into a single 36M-row dataset
2. **Exploratory data analysis** — initial profiling of delay patterns in Python to inform what to investigate further
3. **SQL analytics** — 24 analytical queries across airlines, airports, routes, time patterns, delay and distance causes
4. **Visualization** — four interactive Tableau dashboards
5. **Modeling** — time series forecasting of delay rates, and machine learning classification of individual flight delays

# Tools & Technologies
SQL (PostgreSQL), Python (Pandas, NumPy, scikit-learn, XGBoost, LightGBM, SHAP, statsmodels, Prophet, pmdarima, seaborn, matplotlib, SQLAlchemy), Tableau Public

# Business Problem
Flight delays cost airlines, airports, and passengers billions of dollars annually. Understanding the structural patterns behind delays, that is, which carriers, routes, times of day, and seasons are most affected, helps airlines plan staffing and scheduling, helps airports anticipate congestion, and helps travelers make more informed decisions. This project asks three questions: What does the historical delay landscape look like? Can aggregate delay rates be forecasted? Can an individual flight's delay be predicted in advance from basic schedule information alone?

# Structure
Key Findings 
Interactive Dashboards 
Data Architecture 
Exploratory Data Analysis 
SQL Analysis 
Time Series Forecasting 
Machine Learning: Delay Classification 
Repository Structure 
Data Sources 
How to Run 
Future Work

# Key Findings

# Interactive Dashboards 
Overview – Delay rate trends over time [https://public.tableau.com/app/profile/david.dupre7494/viz/USFlightDelayRateAnalysisDelayRateTrendsOverTime/OverviewDelayRateTrendsOverTime]
Airlines– Carrier–level delay rate comparison [https://public.tableau.com/app/profile/david.dupre7494/viz/USFlightDelayRateAnalysisAirlinePerformance/AirlinePerformance]
Airports & Routes – Origin/destination and route-level analysis [https://public.tableau.com/app/profile/david.dupre7494/viz/USFlightDelayRateAnalysisAirportsRoutes/AiportsRoutes]
Causes – Breakdown of delay causes over time [https://public.tableau.com/app/profile/david.dupre7494/viz/USFlightDelayRateAnalysisDelayCausesSeverity/DelayCausesSeverity]

# Data Architecture

Source: Bureau of Transportation Statistics (BTS) monthly on-time performance data, 63 files combined (2019, 2022–2026)

Pipeline: Raw CSVs → Python merging, cleaning, and feature engineering → PostgreSQL analytics → Tableau + Python modeling

Engineered features: `IS_DELAYED`, `DELAY_CATEGORY`, `DEP_HOUR`, `IS_PEAK_HOUR`, `IS_WEEKEN`, `ROUTE`, `MAIN_DELAY_CAUSE`, `AIRLINE_NAME`

Database: PostgreSQL, single flat flights table, 36,000,275 rows, zero missing values after cleaning

# Exploratory Data Analysis

Before writing SQL queries or building dashboards, an initial EDA was conducted in Python to profile the cleaned dataset and surface which patterns were worth pursuing further. This included checking the overall delay rate, distribution of delays by hour and month, and spot-checking the engineered features (`DEP_HOUR`, `IS_PEAK_HOUR`, `IS_WEEKEND`, `ROUTE`, `MAIN_DELAY_CAUSE`) for consistency before relying on them downstream. 

**Distribution of arrival and departure delays**
![delay and feature distributions](plots/02_distributions.png)
Delay minutes are heavily right-skewed: the mean arrival delay (6.93 min) sits well above the median (-6 min), meaning more than half of all flights actually arrive early, while a long tail of severe delays pulls the average up. Only 0.45% of flights (163,614) were delayed 300+ minutes, but that tail includes some extreme cases and isolated flights with recorded delays extending past a full day.

**Delay rate by hour of day**
![hourly delay rate](plots/06_delay_rate_by_hour.png)
Delays climb steadily through the day, peaking in the evening. This pattern directly motivated engineering `DEP_HOUR` and `IS_PEAK_HOUR` as features.

**Delay rate by month**
![monthly delay rate](plots/05_delay_rate_by_month.png)
Summer months have the highest delay rates, while October, November, and December show the lowest.

**Delay rate by airline**
![delay rate by carrier](plots/04_delay_rate_by_airline.png)
Delay rates vary meaningfully by carrier – Frontier Airlines has the highest delay rate while Delta Air Lines has one of the lowest.

**Delay rate by year**
![delay rate by year](plots/09_delay_rate_by_year.png)
2020–2021 are excluded from this dataset due to COVID-19's disruption to flight volumes.

# SQL Analysis

24 analytical queries organized into six sections:

**Airline analysis
Airport analysis
Route analysis
Time analysis
Delay cause & distance analysis**


Queries use multi-table joins, aggregations with HAVING filters, CASE WHEN logic, and date/time functions. See /sql for the full query set. 

# Time Series Forecasting

The daily delay rate series (2022-01-01 to 2026-03-31, 1,551 observations) was tested for stationarity before model fitting.

**Stationarity (ADF Test)**
- Daily series: ADF statistic = -5.3724, p-value = 0.000004 → **Stationary**

![daily decomposition](plots/11_decomposition_daily.png)
![daily ACF/PACF](plots/12_acf_pacf_daily.png)
Weekly seasonality is clearly visible in the decomposition, informing a weekly seasonal order (m=7) in the SARIMA specification.

**Model Comparison**

Models were evaluated on two held-out quarters: Q1 2026 (90 days) and Q3 2025 (92 days), each trained only on data prior to the test window. `auto_arima` selected the same order — SARIMA(1,0,0)(2,0,1,7) — for both periods.

| Model | Q1 2026 RMSE | Q1 2026 MAPE | Q3 2025 RMSE | Q3 2025 MAPE |
|---|---|---|---|---|
| Manual SARIMA(1,0,0)(1,0,0,7) | 20.50% | 79.10% | 18.25% | 80.45% |
| Manual SARIMA(2,0,0)(1,0,0,7) | 19.59% | 74.27% | 16.51% | 73.01% |
| Auto SARIMA(1,0,0)(2,0,1,7) | **8.27%** | 33.97% | 6.68% | 26.22% |
| Prophet | 9.34% | 43.47% | **4.38%** | **16.81%** |
| SARIMAX + Holiday Flag | 10.17% | **33.26%** | 5.46% | 19.38% |

![forecast comparison](plots/25_forecast_comparison_both_periods.png)

**Findings**

- Manually-specified SARIMA orders performed poorly (RMSE 16-20%), underscoring the value of `auto_arima`'s stepwise AIC search over guessing parameters by hand.
- No single model won both holdouts: Auto SARIMA was strongest on Q1 2026 (RMSE 8.27%), while Prophet was strongest on Q3 2025 (RMSE 4.38%) — suggesting the two models capture different aspects of the underlying structure, and neither is uniformly superior.
- Adding a US holiday flag as an exogenous SARIMAX regressor gave **mixed results**: it improved Q3 2025 RMSE over plain Auto SARIMA (5.46% vs. 6.68%) but made Q1 2026 *worse* (10.17% vs. 8.27%), despite a small MAPE improvement that quarter. Holidays are not a clean, uniform win — a more honest finding than reporting only the period where it helped.
- A Ljung-Box test on the Auto SARIMA residuals shows whiteness holds at lag 7 (p > 0.05) but breaks down at longer lags (14, 21, 28; all p < 0.05) in both holdouts — meaning some autocorrelation structure remains unexplained even by the best-performing model. This is consistent with the models' inability to capture sharp, event-driven delay spikes, which motivated testing whether external weather and flight-volume data could close that gap.
