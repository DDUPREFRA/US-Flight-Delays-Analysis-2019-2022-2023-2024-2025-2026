# US-Flight-Delays-Analysis (2019, 2022-2026)
An end-to-end data analytics project analyzing over 36 million US domestic flight records from the Bureau of Transportation Statistics (BTS) to understand, visualize, forecast, and predict flight delay rates. The pipeline spans exploratory data analysis, SQL analytics, interactive dashboards, time-series forecasting, and machine-learning classification.

# Overview
Using six years of Bureau of Transportation Statistics (BTS) data, this project investigates the share of delayed flights among total flights, when and where US flights are delayed, the severity of delays, and how well delays can be predicted. The analysis covers 2019 and 2022–2026, deliberately excluding 2020–2021 due to COVID-19 travel anomalies that would distort delay patterns.
The project moves through five stages:
1. Data engineering — combining and cleaning 63 monthly BTS files into a single 36M-row dataset
2. Exploratory data analysis — initial profiling of delay patterns in Python to inform what to investigate further
3. SQL analytics — 24 analytical queries across airlines, airports, routes, time patterns, and delay causes
4. Visualization — four interactive Tableau dashboards
5. Modeling — time series forecasting of delay rates, and machine learning classification of individual flight delays

# Tools & Technologies
SQL (PostgreSQL), Python (Pandas, NumPy, scikit-learn, XGBoost, LightGBM, SHAP, statsmodels, Prophet, pmdarima, seaborn, matplotlib, SQLAlchemy), Tableau Public

# Business Problem
Flight delays cost airlines, airports, and passengers billions of dollars annually. Understanding the structural patterns behind delays, that is, which carriers, routes, times of day, and seasons are most affected, helps airlines plan staffing and scheduling, helps airports anticipate congestion, and helps travelers make more informed decisions. This project asks three questions: What does the historical delay landscape look like? Can aggregate delay rates be forecasted? Can an individual flight's delay be predicted in advance from basic schedule information alone?

# Structure
Key Findings – Headline results across the whole project
Interactive Dashboards – Links to the four Tableau Public dashboards
Data Architecture – Source data, pipeline, engineered features, database schema
Exploratory Data Analysis - Initial profiling that shaped the SQL and dashboard work
SQL Analysis – The 24 analytical queries and what they answer
Time Series Forecasting – Stationarity/ACF-PACF testing, then SARIMA vs Prophet comparison
Machine Learning: Delay Classification – Baseline models, threshold tuning, SHAP explainability, honest limitations
Repository Structure – How the codebase is organized
Data Sources - Where the raw data comes from
How to Run – Steps to reproduce the project end to end
Future Work – What's next if extended further

# Key Findings

# Interactive Dashboards 
Overview – Healine KPIs and high-level delay trends [link placeholder]
Airlines– Carrier–level delay rate comparison [link placeholder]
Airports & Routes – Origin/destination and route-level analysis [link placeholder]
Causes – Breakdown of delay causes over time [link placeholder]

# Data Architecture

Source: Bureau of Transportation Statistics (BTS) monthly on-time performance data, 63 files combined (2019, 2022–2026)

Pipeline: Raw CSVs → Python cleaning and feature engineering → PostgreSQL analytics → Tableau + Python modeling

Engineered features: IS_DELAYED, DELAY_CATEGORY, DEP_HOUR, IS_PEAK_HOUR, IS_WEEKEND, ROUTE, MAIN_DELAY_CAUSE, AIRLINE_NAME

Database: PostgreSQL, single flat flights table, 36,000,275 rows, zero missing values after cleaning

# Exploratory Data Analysis

Before writing SQL queries or building dashboards, an initial EDA pass in Python was used to profile the cleaned dataset and surface which patterns were worth pursuing further. This included checking the overall delay rate, distribution of delays by hour and month, and spot-checking the engineered features (DEP_HOUR, IS_PEAK_HOUR, IS_WEEKEND, ROUTE, MAIN_DELAY_CAUSE) for consistency before relying on them downstream. The findings from this stage directly shaped which questions were prioritized in the SQL analysis and which dimensions (carrier, route, time of day, season) became the focus of the Tableau dashboards.

💻 SQL Analysis

24 analytical queries organized into six sections:


Airline analysis
Airport analysis
Route analysis
Time analysis
Delay cause analysis
Year-over-year analysis


Queries use multi-table joins, aggregations with HAVING filters, CASE WHEN logic, and date/time functions. See /sql for the full query set. 
