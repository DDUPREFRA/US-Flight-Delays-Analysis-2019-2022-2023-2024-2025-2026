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
