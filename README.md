# Predicting Demand and Station Imbalance in Bike Share Toronto

This repository contains the code and documentation for a Capstone Project focused on predicting station imbalance risk in the Bike Share Toronto system. The project uses temporal, spatial, weather, and large-scale event factors to forecast when stations are likely to become overfull or empty, especially under normal conditions and extreme demand scenarios (e.g., FIFA World Cup 2026).


## Project Overview

Bike-sharing systems are a vital part of sustainable urban mobility, but they face significant operational challenges from station imbalance, for example, when stations run out of bikes (preventing rentals) or become completely full (preventing returns). This leads to poor user experience for users and high rebalancing costs.
This project develops predictive models to identify high-risk stations and time periods by integrating:
- Historical and real-time bike-share data
- Weather conditions
- Large-scale public events
- Temporal and spatial patterns
Special emphasis is placed on scenario analysis for extreme events, using historical large-scale events as analogs for future high-impact scenarios like the FIFA World Cup 2026 in Toronto.


## Expected Outcomes

- Identification of key imbalance drivers
- Accurate predictive models for normal and high-demand conditions
- Scenario-based insights for events like FIFA World Cup 2026
- Interactive dashboards highlighting high-risk stations and periods

## Data Sources & Ingestion

### Bike Share – Bronze Dataset
- **Time window:** October 2022 – September 2024 (24 months)
- **Total records:** 12,055,519
- **Format:** Parquet
- **Partitioning:** year / month
- **Storage:** Unity Catalog Volume (Databricks)
- **Path:** `dbfs:/Volumes/workspace/default/dbfs/Projects/Capstone/data/bronze/bikeshare_ridership`

### GBFS Real-Time Data (US06)

- **Source:** Bike Share Toronto GBFS (real-time feeds)
- **Endpoints:** station_information, station_status
- **Access mode:** On-demand (no data persistence)
- **Format:** JSON parsed into Spark DataFrames
- **Usage:** Live querying, validation, and exploratory analysis
- **Output:** Spark DataFrames enriched with retrieval timestamp and source metadata

### Bike Share – Silver Dataset (US09)

- **Source:** Bronze Bike Share Trips
- **Processing scope:**
  - Standardized column names (snake_case)
  - Cleaned null and invalid records
  - Converted timestamps to structured date fields
  - Generated temporal features (day, hour buckets)
  - Filtered duration outliers (≤ 4 hours)
- **Granularity:** Trip-level
- **Format:** Parquet
- **Partitioning:** year / month
- **Path:** `dbfs:/Volumes/workspace/default/dbfs/Projects/Capstone/data/silver/bikeshare_trips`

### Bike Share – Silver Aggregated Dataset (Station Hour Flow)

- **Source:** Silver Bike Share Trips
- **Aggregation level:** Station – Hour
- **Processing scope:**
  - Calculated hourly departures and arrivals per station
  - Generated net_flow metric (arrivals – departures)
  - Created hourly time buckets
  - Data quality validation on key fields
- **Granularity:** station_id × year × month × day × hour
- **Format:** Parquet
- **Partitioning:** year / month
- **Path:** `dbfs:/Volumes/workspace/default/dbfs/Projects/Capstone/data/silver_agg/station_hour_flow`

**Usage:**
Feature base for demand prediction and station imbalance modeling.

## Project Structure

```text
project/
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── external/
│
├── src/
│   ├── aggregation/
│   ├── ingestion/
│   ├── preprocessing/
│   ├── features/
│   ├── modeling/
│   └── utils/
│
├── dashboards/
│
├── reports/
│   ├── proposal.pdf
│   ├── eda_report.pdf
│   └── final_report.pdf
│
├── requirements.txt
│
├── README.md
│
└── CONTRIBUTING.md

```

## Data Pipeline Architecture

Raw (CSV / ZIP)
        ↓
Bronze (Trip-level Parquet)
        ↓
Silver (Clean Trips)
        ↓
Silver_Agg (Station Hour Flow)
        ↓
Gold (Integrated Dataset)

## SILVER → GOLD Integration Layer

The integration layer consolidates all SILVER datasets into analytically ready GOLD datasets at **station-hour granularity**, which serves as the modeling backbone for station imbalance prediction.

### Objective

To integrate:

- Station-hour flow data (departures, arrivals, net_flow)
- Hourly weather data
- Station geolocation (lat/lon)
- Large-scale public event data (daily and spatiotemporal)

while preserving strict uniqueness at:

`station_id × year × month × day × hour`

---

### GOLD Datasets

#### GOLD_V1 – Daily Event Integration (Baseline)

Includes:
- Flow variables
- Weather variables
- Station metadata
- Daily event indicators

Use case:  
Baseline modeling using calendar-level event effects.

---

#### GOLD_V2 – Spatiotemporal Event Integration (Enhanced)

Extends GOLD_V1 by incorporating:

- Event-hour filtering (only during active event hours)
- Spatial filtering using Haversine distance
- Nearby event indicators
- Event attendance aggregation per station-hour
- Distance-weighted event intensity
- Operational event impact categories

Use case:  
Advanced imbalance prediction under localized demand shocks (e.g., FIFA World Cup 2026 scenarios).

---
## Exploratory Data Analysis

The full exploratory data analysis for the Bike Share Toronto demand imbalance project can be found in:

`reports/eda/EDA.ipynb`

This notebook explores:
- Trip-level system behavior
- Station-hour operational flows
- Station imbalance patterns
- Extreme imbalance scenarios
- Weather impact on system dynamics
- Public event impact on system dynamics
---
### Recommended Dataset for Modeling

**GOLD_V2** should be used for predictive modeling, as it captures temporal, weather, spatial, and event intensity effects in a unified dataset.


## Team Members
- Jesus Ricardo Vizcarra Vargas
- Liliana Marcela Camargo Mojica
- Teddy Fabrizio Baeny Vargas
- Jose Miguel Osorio Davila
