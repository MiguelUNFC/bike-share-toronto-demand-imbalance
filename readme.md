# Predicting Demand and Station Imbalance in Bike Share Toronto

This repository contains the code and documentation for a Capstone Project focused on predicting station imbalance risk in the Bike Share Toronto system. The project uses temporal, spatial, weather, and large-scale event factors to forecast when stations are likely to become overfull or empty, especially under normal conditions and extreme demand scenarios.

\---

## Table of Contents

* [Project Overview](#project-overview)
* [Expected Outcomes](#expected-outcomes)
* [Data Sources \& Ingestion](#data-sources--ingestion)
* [Project Structure](#project-structure)
* [Data Pipeline Architecture](#data-pipeline-architecture)
* [SILVER → GOLD Integration Layer](#silver--gold-integration-layer)
* [Exploratory Data Analysis](#exploratory-data-analysis)
* [Feature Engineering \& Modeling Preparation](#feature-engineering--modeling-preparation)
* [Modeling Approach](#modeling-approach)
* [Results \& Model Performance](#results--model-performance)
* [Financial Analysis \& Simulation](#financial-analysis--simulation)
* [Dashboards \& Visualizations](#dashboards--visualizations)
* [Limitations](#limitations)
* [How to Run](#how-to-run)
* [Team Members](#team-members)

\---

## Project Overview

Bike-sharing systems are a vital part of sustainable urban mobility, but they face significant operational challenges from station imbalance, for example, when stations run out of bikes (preventing rentals) or become completely full (preventing returns). This leads to poor user experience for users and high rebalancing costs.
This project develops predictive models to identify high-risk stations and time periods by integrating:

* Historical and real-time bike-share data
* Weather conditions
* Public events
* Temporal and spatial patterns

**Target variable:** Net Flow = Arrivals − Departures per station per hour.

\- Positive net\_flow → station is gaining bikes (risk of becoming full)

\- Negative net\_flow → station is losing bikes (risk of becoming empty)

\---

## Project Objective

The goal of this project is to develop a predictive system that anticipates station-level imbalance (surplus or shortage of bikes) and enables proactive operational decisions to reduce service disruption and rebalancing costs.

\---

## Expected Outcomes

* Identification of key imbalance drivers
* Accurate predictive models for normal and high-demand conditions
* Scenario-based insights for events.
* Interactive dashboards highlighting high-risk stations and periods

\---

## Why this Matters

Station imbalance directly impacts user satisfaction and operational efficiency. Accurate predictions allow operators to anticipate demand, optimize rebalancing logistics, and improve overall system reliability.

\---

## Key Contributions

* End-to-end data pipeline (Bronze → Gold)
* Spatiotemporal event feature engineering
* Robust rolling time-series validation framework
* Translation of model performance into financial impact

## Data Sources \& Ingestion

### Bike Share – Bronze Dataset

* **Time window:** October 2022 – September 2024 (24 months)
* **Total records:** 12,055,519
* **Format:** Parquet
* **Partitioning:** year / month
* **Storage:** Unity Catalog Volume (Databricks)
* **Path:** `dbfs:/Volumes/workspace/default/dbfs/Projects/Capstone/data/bronze/bikeshare\\\\\\\\\\\\\\\_ridership`

### GBFS Real-Time Data (US06)

* **Source:** Bike Share Toronto GBFS (real-time feeds)
* **Endpoints:** station\_information, station\_status
* **Access mode:** On-demand (no data persistence)
* **Format:** JSON parsed into Spark DataFrames
* **Usage:** Live querying, validation, and exploratory analysis
* **Output:** Spark DataFrames enriched with retrieval timestamp and source metadata

### Bike Share – Silver Dataset (US09)

* **Source:** Bronze Bike Share Trips
* **Processing scope:**

  * Standardized column names (snake\_case)
  * Cleaned null and invalid records
  * Converted timestamps to structured date fields
  * Generated temporal features (day, hour buckets)
  * Filtered unrealistic trip durations (> 240 minutes)

&#x09;> Trips longer than 4 hours were considered operational anomalies (e.g., abandoned bikes, system errors) and were removed to improve data quality and model reliability.

* **Granularity:** Trip-level
* **Format:** Parquet
* **Partitioning:** year / month
* **Path:** `dbfs:/Volumes/workspace/default/dbfs/Projects/Capstone/data/silver/bikeshare\\\\\\\\\\\\\\\_trips`

### Bike Share – Silver Aggregated Dataset (Station Hour Flow)

* **Source:** Silver Bike Share Trips
* **Aggregation level:** Station – Hour
* **Processing scope:**

  * Calculated hourly departures and arrivals per station
  * Generated net\_flow metric (arrivals – departures)
  * Created hourly time buckets
  * Data quality validation on key fields
* **Granularity:** station\_id × year × month × day × hour
* **Format:** Parquet
* **Partitioning:** year / month
* **Path:** `dbfs:/Volumes/workspace/default/dbfs/Projects/Capstone/data/silver\\\\\\\\\\\\\\\_agg/station\\\\\\\\\\\\\\\_hour\\\\\\\\\\\\\\\_flow`

**Usage:**
Feature base for demand prediction and station imbalance modeling.

\---

## Project Structure

```text
project/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── docs/
│   ├── images/
│
├── src/
│   ├── 1.ingestion/
│   ├── 2.preprocessing/
│   ├── 3.aggregation/
│   ├── 4.integration/
│   ├── 5.eda/
│   ├── 6.modeling/
│   ├── 7.finances/
│   ├── 8.evaluation/
│   └── history/
│
├── dashboards/
│
├── requirements.txt
│
├── README.md
│
└── CONTRIBUTING.md
```

\---

## Data Pipeline Architecture

```
Raw (CSV / ZIP)
       ↓
Bronze (Trip-level Parquet)
       ↓
Silver (Clean Trips)
       ↓
Silver\_Agg (Station Hour Flow)
       ↓
Gold (Integrated Dataset)
```

\---

## SILVER → GOLD Integration Layer

The integration layer consolidates all SILVER datasets into analytically ready GOLD datasets at **station-hour granularity**, which serves as the modeling backbone for station imbalance prediction.

### Objective

To integrate:

* Station-hour flow data (departures, arrivals, net\_flow)
* Hourly weather data
* Station geolocation (lat/lon)
* Public events data (daily and spatiotemporal)

while preserving strict uniqueness at:

`station_id × year × month × day × hour`



### GOLD Datasets

#### GOLD\_V1 – Daily Event Integration (Baseline)

Includes:

* Flow variables
* Weather variables
* Station metadata
* Daily event indicators

Use case:
Baseline modeling using calendar-level event effects.



#### GOLD\_V2 – Spatiotemporal Event Integration (Enhanced)

Extends GOLD\_V1 by incorporating:

* Event-hour filtering (only during active event hours)
* Spatial filtering using Haversine distance
* Nearby event indicators
* Event attendance aggregation per station-hour
* Distance-weighted event intensity
* Operational event impact categories

Use case:
Advanced imbalance prediction under localized demand shocks.

> *Recommended dataset for modeling:* GOLD_V2, as it captures temporal, weather, spatial, and event intensity effects in a unified dataset.

\---

## Exploratory Data Analysis

The full exploratory data analysis for the Bike Share Toronto demand imbalance project can be found in:

`reports/eda/EDA.ipynb`

This notebook explores:

* Trip-level system behavior
* Station-hour operational flows
* Station imbalance patterns
* Extreme imbalance scenarios
* Weather impact on system dynamics
* Public event impact on system dynamics

\---

## Feature Engineering \& Modeling Preparation

Feature engineering and target definition were implemented to prepare the dataset for predictive modeling.

This stage includes:

* Definition of the imbalance prediction target
* Net flow metric construction
* Station-level filtering and preparation of the modeling dataset

Notebook available in:

`src/features/Modeling Preparation & Feature Engineering.ipynb`

\---

## Initial Modeling

This phase includes the initial benchmarking of multiple models:

* Baseline (lag-based)
* Linear Regression
* Random Forest (arrivals/departures → derived net flow)
* LightGBM (initial runs)
* XGBoost (early validation)

The objective was to compare performance (MAE, RMSE) and evaluate modeling strategies before transitioning to direct net flow prediction.

Notebook available in:

`src/features/modeling/initial`

\---

## Modeling Approach

* The modeling problem is framed as a regression task where net\_flow is predicted at station-hour level to anticipate imbalance conditions.
* **Target variable:** Net Flow (Arrivals − Departures) per station per hour
* **Models evaluated:** XGBoost, LightGBM
* **Baseline:** Lag-1 persistence model (assumes next hour demand equals previous hour)
* **Validation strategy:** Rolling time-series cross-validation (23 iterations)

  * Training window: 90 days
  * Test window: 1 month (next month prediction)
* **Evaluation metrics:** MAE, RMSE
* The pipeline is designed to be production-ready, supporting scheduled retraining and integration with real-time data sources (GBFS, weather, events).

This rolling strategy simulates real forecasting conditions and prevents data leakage.

![23 Rolling Time-Based Training & Testing Strategy](https://raw.githubusercontent.com/MiguelUNFC/bike-share-toronto-demand-imbalance/dev/docs/images/fig_rolling_bar.png)

\---

## Predictive System for Station Imbalance

The end-to-end pipeline integrates raw data ingestion, feature engineering, model scoring, and multi-channel output delivery across the Bronze → Silver → Gold → Modeling → Prediction → Consumption layers.

![Predictive System for Station Imbalance](https://raw.githubusercontent.com/MiguelUNFC/bike-share-toronto-demand-imbalance/dev/docs/images/fig_pipeline.png)

\---

## Results \& Model Performance

Models were evaluated across 23 rolling time-series iterations.

|Model|MAE|Improvement vs. Baseline|
|-|-|-|
|**XGBoost**|\~1.50|\~30.60%|
|LightGBM|\~1.52|\~30.43%|
|Baseline (Lag-1)|\~2.20|—|

The \~30% improvement over the baseline is primarily driven by:

\- Incorporation of temporal seasonality (hour/day patterns)

\- Weather sensitivity (temperature-driven demand)

\- Event-based localized demand shocks

![MAE Improvements vs Naive Baseline (23 Rolling Iterations)](https://raw.githubusercontent.com/MiguelUNFC/bike-share-toronto-demand-imbalance/dev/docs/images/fig_mae_improvement.png)

![Rolling Backtesting Performance Across 23 Iterations MAE](https://raw.githubusercontent.com/MiguelUNFC/bike-share-toronto-demand-imbalance/dev/docs/images/fig_rolling_backtest.png)

**Key findings:**

* XGBoost achieved the best overall performance across all rolling iterations.
* External features (weather + events) consistently improved prediction accuracy over the baseline.
* Temporal patterns (hour/day cycles) are the strongest drivers of demand.
* Weather influences system usage intensity.
* Events introduce localized and short-term demand spikes.

&#x20;> \*\*Conclusion:\*\* Models consistently outperform the Lag-1 baseline, confirming that temporal, spatial, weather, and event features significantly improve station imbalance prediction. The models outperform the baseline in 100% of rolling iterations, demonstrating strong consistency and robustness across different time periods.

\---

## Financial Analysis \& Simulation

The financial analysis translates model prediction accuracy into operational and business impact, using data sourced from the **Bike Share Toronto 2024 Business Review** (Toronto Parking Authority, Nov 29, 2024).

> \\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\*Note:\\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\* V3 predicts net\\\\\\\\\\\\\\\_flow (arrivals − departures), not departures directly. Revenue is computed from actual departures; the model's primary financial value is \\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\*operational\\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\* (rebalancing cost reduction), not revenue generation.

### Key Financial Figures

|Metric|Value|
|-|-|
|Total annual expenses|$16.07M CAD|
|Total rides analyzed|6.9M trips|
|Variable cost per trip|$1.98 CAD|
|Rebalancing budget|\~$11M CAD/yr|
|Estimated truck dispatches|\~41,600/yr|
|Cost per dispatch|$263.76 CAD|

### Financial Components

|Component|Description|
|-|-|
|**Revenue \& Cost Engine**|Computes revenue from actual departures and estimates revenue at risk when model errors lead to stock-outs or overflows|
|**Daily P\&L Table**|System-wide daily profit \& loss aggregating revenue, fixed costs, and variable costs across downtown stations|
|**Rebalancing Cost Layer**|Quantifies truck dispatch savings driven by accurate net flow predictions, comparing XGBoost vs LightGBM|
|**Model Dollar Value**|Expresses MAE improvement in financial terms: rebalancing savings and revenue at risk reduction|
|**Gamification Simulation**|Estimates annual savings from a points-based incentive app that rewards annual members for returning bikes to critical/low-stock stations, reducing truck dispatches|

### Gamification Simulation – Base Case Results

A points-based app rewards the 76% annual membership base for returning bikes to low-stock stations:

|Scenario|Effectiveness|Adoption|Net Annual Saving|ROI|
|-|-|-|-|-|
|**Base Case**|10%|40%|\~$118K CAD|74% on points investment|

Notebook available in:

`src/financial/Financial\\\\\\\\\\\\\\\_Analysis\\\\\\\\\\\\\\\_\\\\\\\\\\\\\\\_\\\\\\\\\\\\\\\_Simulation.ipynb`

\---

## Dashboards \& Visualizations

This project includes three complementary visualization layers:

|Tool|Description|
|-|-|
|**Databricks (Python)**|Exploratory and pipeline visualizations embedded in analysis notebooks|
|**Power BI**|Operational dashboard for station imbalance monitoring and reporting|
|**Web App (HTML/CSS/JS)**|Interactive frontend for real-time station risk display|

Screenshots for all dashboards are available in the [`dashboards/`](./dashboards/) folder.

\---

## Limitations

* Limited cost data availability for operational optimization.
* Model performance depends on data quality (weather \& events).
* No real-time retraining implemented.
* External disruptions (e.g., infrastructure changes, outages) are not fully captured.
* Event data represents a relatively small portion of total observations, which may reduce its statistical impact in model training.

\---

## How to Run

> \\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\*Note:\\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\* The full pipeline runs on \\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\*Databricks\\\\\\\\\\\\\\\*\\\\\\\\\\\\\\\*. The steps below assume access to a Databricks workspace with Unity Catalog enabled.

### 1\. Clone the repository

```bash
git clone https://github.com/MiguelUNFC/bike-share-toronto-demand-imbalance.git
```

### 2\. Install dependencies

```bash
pip install -r requirements.txt
```

### 3\. Run the pipeline in order

|Step|Location|
|-|-|
|Data ingestion|`src/ingestion/`|
|Preprocessing|`src/preprocessing/`|
|Aggregation|`src/aggregation/`|
|EDA|`reports/eda/EDA.ipynb`|
|Feature engineering|`src/features/Modeling Preparation & Feature Engineering.ipynb`|
|Modeling|`src/modeling/`|
|Financial analysis|`src/financial/Financial_Analysis_Simulation.ipynb`|

### 4\. View dashboards

Open screenshots in [`dashboards/`](./dashboards/) or run the web app locally by opening `dashboards/index.html` in a browser.

\---

## Team Members

|Name|
|-|
|Jesus Ricardo Vizcarra Vargas|
|Jose Miguel Osorio Davila|
|Liliana Marcela Camargo Mojica|
|Teddy Fabrizio Baeny Vargas|

\---

*DAMO-699-9: Capstone Project — Group 3 | Winter 2026 | Professor: Hany Osman | University of Niagara Falls*

