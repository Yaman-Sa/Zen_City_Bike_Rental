# 🚴‍♂️ Zen City's Journey: Optimizing Urban Mobility Through Bike Rental Data

An end-to-end data analytics and predictive modeling project designed to maximize fleet utilization and drive rental growth for **Zen City** ahead of Q2 2022. 

---

## 🎓 Program Origin & Scope

This repository houses the final capstone project developed during the **Google-Reichman Data Analyst Program**. 
* **The Challenge:** Zen City accumulated massive transactional telemetry during Q1 2022 (January–March). The business mandate was to leverage this historical baseline to engineer strategies that directly increase total bike rentals in the upcoming quarter (**Q2 2022**).
* **The Portfolio Expansion:** While academic benchmarks required baseline descriptive statistics and a single-station predictive analysis, this repository represents an expanded, independent scope. It features a unified data preparation pipeline using Common Table Expressions (CTEs), targeted customer segmentation, hardware bottleneck analysis, and data-driven demand forecasting frameworks.

---

## 📊 The Real-World Dataset

The analysis utilizes real, messy operational logs spanning three core relational tables hosted on Google BigQuery:
* `bqproj-488319.zen_city.rentals` — Transactional ride history, timestamps, and trip durations.
* `bqproj-488319.zen_city.customers` — User demographic profiles and membership types.
* `bqproj-488319.zen_city.station_info` — Physical dock specifications, operational statuses, and spatial markers.

---

## 🛠️ Analytical Rigor: The Imputation Strategy

A standard relational inner join filtering out missing station profiles would have triggered a catastrophic **40% drop in dataset volume** (truncating the sample from 16,585 rows down to 10,007). Dropping these records would erase genuine consumer rides, skew temporal volume trends, and starve downstream predictive models.

To defend data volume without compromising mathematical integrity, a **Data Sparing and Imputation CTE** was constructed:
* **Categorical String Preservation:** Missing values in continuous metadata fields (`property_type`, `power_type`, `address`) were safely preserved using explicit `'Unknown'` descriptors to protect downstream relational queries.
* **Physical Dimension Imputation:** Continuous missing values (`number_of_docks`, `footprint_length`, `footprint_width`) were backfilled using localized median imputation to maintain natural distributions.
* **Inconsistency Resolution:** Resolved critical structural anomalies, such as isolating identical physical stations with divergent IDs (e.g., isolating the "Ghost Station" anomaly for the *Lavaca & 6th* hub to prevent join fan-outs).
* **Outlier Filtration:** Purged severe system errors and operational anomalies (trips lasting under 1 minute or exceeding 24 hours).

**Final Pipeline Efficiency:** Preserved **99.51%** of total transaction records (16,504 out of 16,585), securing complete statistical power for forecasting models.

---

## 📈 Key Business Insights & Analytical Discoveries

### 1. The Student Commuter Engine
* **The Data:** Student Memberships constitute an overwhelming **75.2% majority** of total rental records.
* **The Insight:** There is a stark inverse relationship between rental count and ride duration. Students execute high volumes of incredibly brief trips clustering tightly around the **6-minute mark**. 
* **Business Translation:** Zen City operates primarily as a **"last-mile" point-to-point micro-mobility engine** for students rather than a leisure or long-term rental service.

### 2. Physical Hardware & Demand Bottlenecks
* **The Data:** High-traffic transit nodes show extreme turnover rates. Stations like `Dean Keeton/Speedway` and `21st/Speedway @ PCL` generate thousands of unique rides despite constrained localized dock availability.
* **The Insight:** Hourly peak demand routinely outpaces localized hardware capacity. A station with only 15 physical docks can process over 60 transactions during rush hour. 
* **Business Translation:** Without data-driven, intra-day fleet rebalancing, high-traffic corridors face guaranteed zero-bike stockouts, mathematically capping potential Q2 revenue growth.

---

## 🧠 Strategic Growth Hypotheses for Q2 2022

Based on the exploratory data analysis, four high-impact directives were proposed:
1. **Double Down on the Core:** Prioritize optimization, capacity increases, and marketing outreach along high-volume student clusters (centered on the `21st/Speedway @ PCL` axis).
2. **Dynamic Fleet Rebalancing:** Deploy systematic bike redistribution schedules during peak periods to alleviate high-turnover shortages before stockouts happen.
3. **Asset Relocation:** Decommission or relocate the bottom 10 least-utilized network stations (e.g., `Rio Grande/12th`, `East 6th/Robert T. Martinez`) to high-demand commuter zones to mitigate structural losses.
4. **Tailored Monetization Tiers:** Introduce hyper-specific pricing structures—such as optimized per-trip flat fees for last-mile student users versus per-minute tier rates for secondary customer demographics who take longer leisure rides.

---

## 🗂️ Repository Navigation

* 📂 **`Raw_Tables/`** — Original datasets (`station_info`, `rentals`, `customers`).
* 📂 **`SQL/`** — Complete, raw `.sql` scripts (e.g., Master Data Cleaning CTE).
* 📂 **`Visualizations/`** — Charts and graphs generated via BQ Studio and Python.
* 📄 **[`README.md`](README.md)** — Executive summary, project goals, and navigation map.
* 📄 **[`01_Tables_First_Look.md`](01_Tables_First_Look.md)** — Initial data exploration, primary key validation, and anomaly checks.
* 📄 **[`02_Data_Cleaning_and_Wrangling.md`](02_Data_Cleaning_and_Wrangling.md)** — Data imputation strategy, outlier handling, and the unified pipeline.
* 📄 **[`03_Analysis_and_Predictions.md`](03_Analysis_and_Predictions.md)** — Demographic analysis, infrastructure bottlenecks, and modeling.
* 📄 **[`Final_Clean_Table_CTE.csv`](Final_Clean_Table_CTE.csv)** — Final CTE, Clean Data ready to analyze .
