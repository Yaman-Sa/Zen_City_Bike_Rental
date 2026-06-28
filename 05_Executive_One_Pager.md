# Zen City — Executive One-Pager
**Q2 2022 Rental Growth Strategy · Expanded Portfolio Capstone**

---

## Situation
Zen City logged **16,504 validated trips** in Q1 2022 (Jan–Mar). The business mandate: increase total rentals in **Q2 (Apr–Jun)** using data, not guesswork.

## Diagnosis
| Finding | Evidence |
| :--- | :--- |
| **Student-driven network** | 76% of trips · 6-minute median duration |
| **Afternoon-dominant demand** | 3–6 PM = 35% of all trips; 3 PM is peak hour |
| **Binding constraint = PCL corridor** | Terminal sink at 145% dock capacity during rush |
| **Demand exists** | Peak day: 412 trips (Feb 10) — infrastructure lags, not riders |

## Recommendation (Top 3)
1. **Install PCL overflow docks** (+10–15 slots) — removes the #1 throughput blocker  
2. **Deploy continuous PCL clearing** 1–6 PM + staggered rebalancing Tue–Thu  
3. **Retain 568 power users** — top 20 users alone = 40% of all trip volume  

## Q2 Forecast
| Scenario | Trips | vs Q1 |
| :--- | :---: | :---: |
| No action (S0) | 17,090 | +3.6% |
| **Seasonal floor (S2-Adj)** | **17,859** | **+8.2%** |
| **Stretch target (S2)** | **19,141** | **+16.0%** |

Use **17,859** for staffing/fleet planning; **19,141** as stretch if PCL docks + leisure campaigns offset June student departure.

## Success Metrics (Weekly)
- Total trips ≥ **1,472/week** (S2 pace)  
- PCL peak arrivals **< 320/hour** post-expansion  
- Active power users ≥ **540**  

## Data & Reproducibility
Full pipeline: `01` audit → `02` CTE (99.5% retention) → `03` analysis → `04` recommendations + forecast · [`Final_Clean_Table_CTE.csv`](Final_Clean_Table_CTE.csv) · [`SQL/Final project all.sql`](SQL/Final%20project%20all.sql)

---
*Google-Reichman Data Analyst Program capstone · independently expanded scope · [Full deck](05_Final_Presentation.md)*
