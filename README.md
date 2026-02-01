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


## Project Structure

```text
project/
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── external/
│
├── notebooks/
│   ├── 01_data_ingestion.ipynb
│   ├── 02_cleaning_preprocessing.ipynb
│   ├── 03_eda.ipynb
│   ├── 04_feature_engineering.ipynb
│   ├── 05_modeling.ipynb
│   └── 06_validation_scenarios.ipynb
│
├── src/
│   ├── ingestion/
│   ├── cleaning/
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
└── README.md
```


## Team Members
- Jesus Ricardo Vizcarra Vargas
- Liliana Marcela Camargo Mojica
- Teddy Fabrizio Baeny Vargas
- Jose Miguel Osorio Davila
