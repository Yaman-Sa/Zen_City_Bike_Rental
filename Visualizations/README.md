# Visualizations — Zen City Q1 2022 Analytics

Charts and export-ready data supporting the analysis pipeline (`03_Analysis_and_Predictions.md`) and Q2 forecast (`04_Predictive_Modeling.md`).

## Quick Start

| Asset | Use |
| :--- | :--- |
| **[`index.html`](index.html)** | Open in any browser — interactive dashboard for screenshots or print-to-PDF |
| **`data/*.csv`** | Import into Google Slides, PowerPoint, Excel, or Looker Studio |
| **`mermaid/*.mmd`** | Paste into [Mermaid Live Editor](https://mermaid.live) for SVG/PNG export |

## Chart Index

| # | Chart | Source Section | Data File |
| :---: | :--- | :--- | :--- |
| 1 | Subscription tier mix | 03 §01 | `data/subscription_mix.csv` |
| 2 | Bike type split | 03 §01 | `data/bike_type.csv` |
| 3 | Trips by day of week | 03 §02 | `data/dow_trips.csv` |
| 4 | Hourly volume (7 AM–10 PM) | 03 §07 | `data/hourly_trips.csv` |
| 5 | Top station departures | 03 §05 | `data/top_stations_departures.csv` |
| 6 | Q2 forecast scenarios | 04 §10 | `data/q2_scenarios.csv` |
| 7 | Q2 monthly S2-Adj split | 04 §10 | `data/q2_monthly_s2.csv` |

## Mermaid Sources

Reusable diagram definitions for presentation export:

| File | Diagram |
| :--- | :--- |
| `mermaid/subscription_mix.mmd` | Pie — subscription tiers |
| `mermaid/bike_type.mmd` | Pie — electric vs classic |
| `mermaid/classic_campus_loop.mmd` | Flow — classic bike closed loop |
| `mermaid/q2_scenarios.mmd` | Flow — forecast scenario ladder |
| `mermaid/pcl_sink.mmd` | Flow — PCL terminal sink |

## Export Tips

**For slides:** Open `index.html` → use browser print (Ctrl+P) → "Save as PDF" per chart section, or screenshot individual canvases.

**For GitHub:** The same charts are embedded as Mermaid blocks in the markdown analysis files; GitHub renders them natively in the repo view.

**Dataset:** All values derive from `Final_Clean_Table_CTE.csv` (16,504 production rows) unless noted as raw-audit-only (e.g., Springfest single record excluded from CTE).
