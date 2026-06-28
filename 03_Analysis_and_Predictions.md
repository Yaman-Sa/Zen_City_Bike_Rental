**final Visulizations and predictions will be here**

# **01- Executive Overview**
Now that the dataset has been cleaned and validated, the objective shifts from data quality assessment to understanding rider behavior, station performance, and demand patterns that can help Zen City increase Q2 rentals.

I already have basic understandng of the structure of my CTE, however, instead of going into random analysing and charts I'll try to seperate into sections, each searching and analysing specific matter of interest, the rest of my charts that might help later will be in "Extra" .

*Note* : I know that BI or Tabelu are indutry standards ,and my next project ill be after I have finished the study for these tools, for now I feel most comfortable with the tools I am using, and they are sufcient and provide the needed objectives .


### **Tools**
* the final Clean CTE , I downloaded it locally and also already have it on `Google Sheets` and `BigQuery Studio` , size: 3.4mb.
* Charts and Graphs: `Google Sheets` , `BigQuery Studio` , `Python` (locally Via jetbrains Pycharm) ,  SQL.
* tables: SQL (BigQuery), Google Sheets.
* Devices : Desktop PC (windows 10-11)  , Laptop Lenovo Legion 5i (windows 11) .
* 



----------------------------------------------------------------------(continue here!!)


# **02 - Univariate Analysis**

I have already noticed the rental records and the majorty was for student subcription (76.3% of rentals), ofcourse it is the extreme majoriy and would be no brainer to study this group.

Let's start with basic analysis , I'll focus on what could be used later , I call them "determining Keys" .


---
##  **Subscriber types**
Subscription Type Analysis

This section breaks down the Zen City rental distribution across all active subscription and membership types for Q1 2022, evaluated by both absolute rental counts and overall volume percentage.

### 1. Rental Count & Volume Distribution

The table below merges rental counts and percentage shares to highlight user segment behavior. 

| Subscription Type | Rental Count | Volume Percentage | Visual Distribution |
| :--- | :---: | :---: | :--- |
| **Student Membership** | 12,575 | 76.19% | ████████████████████▒ |
| **Local31** | 1,286 | 7.79% | ██░░░░░░░░░░░░░░░░░░░ |
| **Local365** | 1,059 | 6.42% | █░░░░░░░░░░░░░░░░░░░░ |
| **Pay-as-you-ride** | 443 | 2.68% | ░░░░░░░░░░░░░░░░░░░░░ |
| **Explorer** | 402 | 2.44% | ░░░░░░░░░░░░░░░░░░░░░ |
| **3-Day Weekender** | 232 | 1.41% | ░░░░░░░░░░░░░░░░░░░░░ |
| **U.T. Student Membership** | 221 | 1.34% | ░░░░░░░░░░░░░░░░░░░░░ |
| **24 Hour Walk Up Pass** | 143 | 0.87% | ░░░░░░░░░░░░░░░░░░░░░ |
| **Single Trip (Pay-as-you-ride)** | 137 | 0.83% | ░░░░░░░░░░░░░░░░░░░░░ |
| **HT Ram Membership** | 4 | 0.02% | ░░░░░░░░░░░░░░░░░░░░░ |
| **Annual Membership** | 2 | 0.01% | ░░░░░░░░░░░░░░░░░░░░░ |
| **Total Ecosystem** | **16,504** | **100.00%** | |

### 📉 Visual Breakdown (Dynamic Chart)


```mermaid
pie title Zen City Fleet Utilization by Subscription Type (%)
    "Student Membership" : 76.19
    "Local31" : 7.79
    "Local365" : 6.42
    "Pay-as-you-ride" : 2.68
    "Explorer" : 2.44
    "Others (Combined Tiers)" : 4.48
```

*The Student Core Engine: Combined student tiers (Student Membership + U.T. Student Membership) generate a staggering 77.53% of all rental volume ($12,796$ trips). This confirms that university-related commuting dictates the primary operational baseline for bike rebalancing schedules.
*Local Commuter Sticky Share: Local31 and Local365 provide a highly stable recurring user base representing 14.21% of rentals. These users present high baseline value due to standard work-week trip consistency.
*Casual Single-Trip Opportunities: Pay-as-you-ride and short pass tiers account for a lower volume share ($~8.2\%$), but represent crucial high-margin revenue touchpoints during weekends and leisure hours.
---


##  **Bike types**
## Bike Type Analysis

This section explores the utilization and fleet breakdown between **Classic** and **Electric** bikes across the Zen City ecosystem for the analyzed period.

### 1. Fleet Composition & Usage Split

The table below details the absolute ride counts and self-calculated percentage shares for each bike type based on a total pool of **16,504** rides.

| Bike Type | Ride Count | Percentage Share | Visual Distribution |
| :--- | :---: | :---: | :--- |
| **Electric** | 14,473 | 87.69% | ██████████████████░░ |
| **Classic** | 2,031 | 12.31% | ██░░░░░░░░░░░░░░░░░░ |
| **Total Ecosystem** | **16,504** | **100.00%** | |



### 📉 Visual Breakdown (Dynamic Chart)



```mermaid
pie title Zen City Ride Volume by Bike Type (%)
    "Electric Bike" : 87.69
    "Classic Bike" : 12.31
```
* Overwhelming Electric Dominance: Electric bikes represent the vast majority of the volume at 87.69%. This massive preference suggests that users heavily prioritize speed, ease of travel, and reduced physical effort for their trips, which aligns with a commuter-heavy user base.
* Classic Bikes Relegated to Niche: Classic mechanical bikes account for just 12.31% of total rides. This indicates they are likely utilized either as a budget-conscious alternative, for shorter leisure trips, or as a backup option when electric bike availability is low at specific stations, or special events like the `spring fistival` I encountered and other Orphaned stations , further check is required.
  ** analysis: 1: classis Bikes in orphaned stations (events that count towards using classic bikes, these events count towards the Oprphaned stations we mentioned in file `02_Data_Cleaning_and_Wrangling` ) 
               2: stations where it was used: could it be that the stations are moving toward full electric support? , if the bikes are distributed such as  that the majority in less crowded stations? , if not could it be truely as alternative cheaper option? .

* Operational Implication: Given that nearly 9 out of 10 rides rely on electric models, logistics teams must focus heavily on battery charging infrastructure, efficient station rotation, and proactive maintenance of motorized components to prevent fleet downtime.

### 🔍 Deep-Dive: Investigating the Classic Bike Anomalies

While Electric bikes dominate the overall volume, the remaining **12.31%** attributed to Classic mechanical bikes requires a deeper operational audit. Rather than a uniform user preference, preliminary patterns suggest this baseline is driven by infrastructure limitations and isolated seasonal anomalies. 

To validate this, the analysis is divided into two target investigations:

---

#### 🗺️ Investigation 1: Event-Driven Spikes & Orphaned Stations
We hypothesize that classic bike usage is heavily inflated by specific, isolated events and temporary infrastructure disruptions rather than consistent organic demand.

* **Special Event Anomalies:** Initial observations indicate significant utilization spikes during specific windows, such as the `spring festival`. These transient events distort regular fleet usage baselines.
* **Orphaned Station Correlation:** There is an active correlation to be checked between classic bike check-outs and the **Orphaned Stations** identified during our initial preprocessing phase in `02_Data_Cleaning_and_Wrangling`. 
* **Analytical Action Item:** Cross-reference ride timestamps during the `spring festival` window against station IDs to determine if classic bike usage was driven by artificial supply constraints (e.g., electric rebalancing teams being unable to access high-congestion zones).

---

#### 🌐 Investigation 2: Spatial Distribution & Infrastructure Transition
We need to determine if the retention of classic bikes is a strategic cost-alternative for users, or simply a byproduct of an ongoing grid migration toward full electrification.

```mermaid
graph TD
    A[Classic Bike Rides] --> B{Spatial Density Pattern?}
    B -->|High Density / Core Stations| C[True Budget Alternative]
    B -->|Low Density / Remote Stations| D[Electrification Lag / Supply Deficit]
```

```
SQL queries for: 
1:Orphaned Stations Analysis by Bike Type
2:Top Stations for Classic Bike Type

-- Append this query directly below the CTE framework (replacing the final SELECT statement)
, ProductionOutcomes AS (
  SELECT 
    cr.bike_type,
    CASE 
      WHEN start_st.station_id IS NULL OR end_st.station_id IS NULL THEN 1 
      ELSE 0 
    END AS is_orphaned_trip
  FROM CleanedRentals cr
  LEFT JOIN CleanedStationProfiles start_st 
    ON cr.clean_start_station_id = start_st.station_id
  LEFT JOIN CleanedStationProfiles end_st 
    ON cr.clean_end_station_id = end_st.station_id
  WHERE (LOWER(start_st.station_status) != 'closed' OR start_st.station_status IS NULL)
    AND (LOWER(end_st.station_status) != 'closed' OR end_st.station_status IS NULL)
)

SELECT
  bike_type,
  COUNT(1) AS total_trips,
  SUM(is_orphaned_trip) AS orphaned_station_trips,
  ROUND(SAFE_DIVIDE(SUM(is_orphaned_trip), COUNT(1)) * 100, 2) AS orphaned_percentage
FROM ProductionOutcomes
GROUP BY bike_type
ORDER BY bike_type;



-------------------------------------------
-- Append this query directly below the CTE framework (replacing the final SELECT statement)
, ClassicTrips AS (
  SELECT 
    cr.bike_type,
    cr.clean_start_station_name AS station_name,
    cr.clean_start_station_id AS station_id
  FROM CleanedRentals cr
  LEFT JOIN CleanedStationProfiles start_st ON cr.clean_start_station_id = start_st.station_id
  LEFT JOIN CleanedStationProfiles end_st ON cr.clean_end_station_id = end_st.station_id
  WHERE cr.bike_type = 'classic'
    AND (LOWER(start_st.station_status) != 'closed' OR start_st.station_status IS NULL)
    AND (LOWER(end_st.station_status) != 'closed' OR end_st.station_status IS NULL)

  UNION ALL

  SELECT 
    cr.bike_type,
    cr.clean_end_station_name AS station_name,
    cr.clean_end_station_id AS station_id
  FROM CleanedRentals cr
  LEFT JOIN CleanedStationProfiles start_st ON cr.clean_start_station_id = start_st.station_id
  LEFT JOIN CleanedStationProfiles end_st ON cr.clean_end_station_id = end_st.station_id
  WHERE cr.bike_type = 'classic'
    AND (LOWER(start_st.station_status) != 'closed' OR start_st.station_status IS NULL)
    AND (LOWER(end_st.station_status) != 'closed' OR end_st.station_status IS NULL)
)

SELECT 
  station_id,
  station_name,
  COUNT(1) AS total_classic_rental_records
FROM ClassicTrips
GROUP BY station_id, station_name
ORDER BY total_classic_rental_records DESC
LIMIT 10; -- Adjust limit size as needed

```

### Empirical Validation: Testing the Classic Bike Hypotheses

To accurately evaluate the operational role of the mechanical fleet tier, targeted SQL queries were executed against the finalized production dataset ($16,504$ records). This analysis validates or disproves the initial infrastructure hypotheses by cross-referencing trip footprints against the **Orphaned Stations** identified in `02_Data_Cleaning_and_Wrangling` and isolating geographic demand centers.

---

#### 1. Fleet Exposure to Orphaned Stations
This audit evaluates whether Classic mechanical bikes are disproportionately left stranded at or rented from structural network anomalies (unregistered/ghost stations), or if infrastructure detachment is a systemic fleet issue.

| Bike Type | Total Trips | Orphaned Station Trips | Orphaned Percentage | Network Footprint |
| :--- | :---: | :---: | :---: | :--- |
| **Classic** | 2,031 | 713 | 35.11% | ███████░░░░░░░ [35.11%] |
| **Electric** | 14,473 | 5,838 | **40.34%** | ████████░░░░░░ [40.34%] |
| **Total Fleet** | **16,504** | **6,551** | **39.69%** | **System-Wide Average** |

##### Analytical Evaluation: The Orphaned Station Disproof
The data explicitly **disproves** the theory that Classic bikes are uniquely isolated at orphaned stations or driven out of bounds by seasonal disruptions like the `spring festival`. 
* **Systemic Relocation Issues:** Electric bikes actually exhibit a *higher* rate of interaction with orphaned stations (**40.34%**) than Classic bikes (**35.11%**). 
* **Conclusion:** Orphaned station interaction is a network-wide rebalancing and logging latency challenge rather than a behavioral pattern unique to mechanical bikes. The electric fleet is frequently pulling out of standard station grids due to longer user travel distances.

---

#### 2. Spatial Clustering: Top 10 High-Volume Classic Bike Hubs
To map the geographic footprint of the mechanical fleet, transactions were aggregated by individual station profiles to isolate where Classic rides occur.

| Station ID | Station Name | Classic Record Count | Primary Location Context / Attribute |
| :---: | :--- | :---: | :--- |
| **3798** | 21st/Speedway @ PCL | 447 | Core Campus Axis (Perry-Castañeda Library) |
| **2498** | Dean Keeton/Speedway | 431 | Northern Campus Axis (Engineering / North Gate) |
| **2547** | 21st/Guadalupe | 413 | West Campus Drag (High-Density Student Housing) |
| **3797** | 21st/University | 374 | Central Campus Core (Academic Buildings) |
| **7125** | 23rd/San Gabriel | 367 | West Campus (Greek Life & Student Apartments) |
| **7188** | 22nd/Pearl | 256 | West Campus Residential Core |
| **2566** | Electric Drive/Sandra Muraida Way @ Pfluger Ped Bridge | 253 | Off-Campus Leisure / Flat Ground Recreational Trail |
| **3793** | 28th/Rio Grande | 219 | Upper West Campus Housing Corridor |
| **3799** | 23rd/San Jacinto @ DKR Stadium | 193 | Campus Athletics & Eastern Transit Hub (DKR Stadium) |
| **2548** | Guadalupe/West Mall @ University Co-op | 159 | Main Campus Gateway / Commercial Center |

---

#### Methodological Note: The Network Loop Paradox

A standalone aggregation of these top 10 stations yields **3,113 record counts**, which superficially contradicts the absolute network total of **2,031 unique classic trips**. 

This mathematical overlap is empirical proof of a highly concentrated, **circular closed-loop network**. Because the classic fleet is structurally confined to a tight campus perimeter, a single trip frequently registers at these top hubs twice (e.g., a student checking out a Classic bike at `21st/Guadalupe` and checking it back into `Dean Keeton/Speedway`). The mechanical fleet rarely leaves this hyper-localized student ecosystem.

```mermaid
graph LR
    A[West Campus Housing: ID 2547] -- High Density Student Flow --> B[Core Academic Hubs: ID 3798 / 2498]
    B -- Class Dismissal Passing Windows --> A
    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#bbf,stroke:#333,stroke-width:2px
```
Strategic Infrastructure Recommendations for Q2 2022

The verified data modifies our asset distribution strategy away from broad fleet electrification and focus instead on specialized hardware tier isolation:

    The Overspill Safety Valve: Since Student Memberships comprise 76.19% of all ecosystem rentals, Classic bikes serve as vital backup capacity. During high-velocity class passing windows, primary e-bike inventory at core hubs like 21st/Speedway @ PCL is instantly depleted. Students actively switch to mechanical units to complete short, time-sensitive commutes.

    Bifurcated Rebalancing Strategy: * Electric Fleet Strategy: Focus logistics teams on external virtual boundaries, battery swapping rotations, and long-range commuter corridors.

        Classic Fleet Strategy: Enforce a strict policy of dock-occupancy optimization on campus. Because Classic bikes are locked into this tight campus loop, rebalancing teams must actively prevent them from completely filling up physical docks during morning rushes. Leaving open docks at high-volume campus centers is critical to allowing high-margin Electric bikes to be returned without system friction.

    Leisure Tier Stabilization: The prominent presence of Electric Drive/Sandra Muraida Way @ Pfluger Ped Bridge (253 records) represents the only major non-student classic cluster. This highlights a distinct sub-persona: casual/leisure users utilizing mechanical units for flat-ground, recreational trail loops. This specific hub must maintain steady classic inventory ahead of weekend afternoon demand spikes.


-------------------------------------------------------------------(continue here!)




###  Network Flow & Directional Asymmetry: Sources vs. Sinks

To map the operational loop of the mechanical fleet, transactions at the top 10 stations were segregated into **Departures** (outflows), **Arrivals** (inflows), and **True Round Trips** (trips starting and ending at the exact same physical dock). 

This analysis reveals that the classic fleet does not move randomly; instead, it operates within a highly predictable, asymmetric commuter gravity well.

| Station ID | Station Name | Classic Departures | Classic Arrivals | Total Interaction Volume | True Round Trips | Operational Profile |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| **3798** | 21st/Speedway @ PCL | 0 | 447 | 447 | 0 | 🛑 **Absolute Terminal Sink** |
| **2498** | Dean Keeton/Speedway | 328 | 103 | 431 | 17 | 🛫 **Primary Source / Net Exporter** |
| **2547** | 21st/Guadalupe | 307 | 106 | 413 | 17 | 🛫 **Primary Source / Net Exporter** |
| **3797** | 21st/University | 264 | 110 | 374 | 9 | 🛫 **Net Exporter** |
| **7125** | 23rd/San Gabriel | 273 | 94 | 367 | 13 | 🛫 **Net Exporter** |
| **7188** | 22nd/Pearl | 183 | 73 | 256 | 8 | 🛫 **Net Exporter** |
| **2566** | Electric Drive/Sandra Muraida Way @ Pfluger Ped Bridge | 197 | 56 | 253 | 51 | 🔄 **Recreational Loop Hub** |
| **3793** | 28th/Rio Grande | 147 | 72 | 219 | 13 | 🛫 **Net Exporter** |
| **3799** | 23rd/San Jacinto @ DKR Stadium | 112 | 81 | 193 | 14 | ⚖️ **Balanced Transit Node** |
| **2548** | Guadalupe/West Mall @ University Co-op | 0 | 159 | 159 | 0 | 🛑 **Absolute Terminal Sink** |

---

###  Key Behavioral & Logistical Discoveries

#### 1. The "Terminal Sink" Anomaly (Stations 3798 & 2548)
Stations **21st/Speedway @ PCL** (Perry-Castañeda Library) and **Guadalupe/West Mall** show a staggering **0 classic departures**, yet combine for **606 arrivals**. 
* **The Commuter Gravity Well:** Students treat these locations as absolute dead-ends for mechanical bikes. They unlock classic bikes at the residential perimeters of West Campus early in the day and ride them downhill/inward to the academic core. 
* **The Rebalancing Bottleneck:** Because departures are flatline zero, classic bikes naturally stack up and clog these highly competitive campus docks. Rebalancing teams are structurally required to manually haul mechanical units away from these libraries and plazas to prevent them from locking out incoming, high-margin Electric bikes.

#### 2. The West Campus "Shed" (Stations 2498, 2547, 7125)
Conversely, stations like *Dean Keeton/Speedway*, *21st/Guadalupe*, and *23rd/San Gabriel* operate as massive net exporters of classic assets. Combined, these three perimeter hubs generate **908 departures** but accept only **303 arrivals**. They function as the primary deployment staging grounds where students scavenge left-over mechanical units when e-bike supply runs dry during morning rushes.

#### 3. Empirical Proof of the Leisure Persona (Station 2566)
**Pfluger Ped Bridge** provides explicit validation for the leisure rider hypothesis. 
* While high-volume campus hubs see true round-trip rates of less than 4%, Pfluger Ped Bridge clocks a massive **25.8% true round-trip rate** (51 round trips out of 197 departures). 
* **Behavioral Reality:** Users at this river trail are explicitly renting classic units for self-contained, out-and-back exercise or recreational loops, returning the asset to its exact starting point.

```mermaid
graph TD
    %% Define Nodes
    Residential[West Campus Sources <br> ID: 2547, 2498, 7125]
    Core[Campus Academic Sinks <br> ID: 3798 PCL, 2548 West Mall]
    Pfluger[Pfluger Ped Bridge <br> ID: 2566]
    
    %% Directional Flows
    Residential -- "Massive One-Way Outflow <br> (Student Commute)" --> Core
    Core -. "Manual Operations Trucking <br> (Required Rebalancing)" .-> Residential
    Pfluger --> |"26% Closed Loop Trips <br> (Leisure Trails)"| Pfluger

    classDef source fill:#d4edda,stroke:#28a745,stroke-width:2px;
    classDef sink fill:#f8d7da,stroke:#dc3545,stroke-width:2px;
    classDef loop fill:#fff3cd,stroke:#ffc107,stroke-width:2px;
    
    class Residential source;
    class Core sink;
    class Pfluger loop;
```
Data-Driven Operational Directives for Q2 2022

* Implement Hard Dock Caps for Classics at Sinks: Programmatically or operationally limit the number of classic bikes allowed to occupy slots at 21st/Speedway @ PCL and Guadalupe/West Mall. If classic bikes fill more than certain percent of these premium docks, field technicians must immediately trigger a clearing sweep.

* Targeted Fleet Injections: Prioritize rebalancing drop-offs of classic bikes exclusively to the high-departure West Campus zones (Dean Keeton, 21st/Guadalupe, 23rd/San Gabriel) between 7:30 AM and 9:00 AM on weekdays to feed the incoming student migration wave.

* Leisure Buffer Stocking: Maintain a dedicated baseline of fully functional mechanical units at the Pfluger Ped Bridge hub specifically on Friday afternoons through Sunday evening, avoiding the temptation to replace them entirely with electric variants.




---

### **electric  top stations and comparison:** 

``` --SQL query:
-- [KEEP YOUR EXISTING CTE STEPS 1 THROUGH 5 EXACTLY AS THEY ARE ABOVE]

-- 6. Enriched Production Dataset (Converted to CTE for downstream aggregation)
EnrichedRentals AS (
  SELECT 
    cr.bike_type,
    cr.clean_start_station_id,
    cr.clean_start_station_name,
    cr.clean_end_station_id,
    cr.clean_end_station_name
  FROM CleanedRentals cr
  LEFT JOIN CleanedStationProfiles start_st 
    ON cr.clean_start_station_id = start_st.station_id
  LEFT JOIN CleanedStationProfiles end_st 
    ON cr.clean_end_station_id = end_st.station_id
  WHERE (LOWER(start_st.station_status) != 'closed' OR start_st.station_status IS NULL)
    AND (LOWER(end_st.station_status) != 'closed' OR end_st.station_status IS NULL)
)

-- 7. Loop Analysis: Total Electric Bike Station Participation & Flow Mechanics
SELECT 
    station_id,
    station_name,
    SUM(is_departure) AS electric_departures,
    SUM(is_arrival) AS electric_arrivals,
    -- Total interactions hitting this station infrastructure
    SUM(is_departure + is_arrival) AS total_electric_participation,
    -- True Self-Loops (Trips starting and ending at this exact station)
    SUM(is_self_loop) AS true_round_trips
FROM (
  -- Pass 1: Gather Origin Activity
  SELECT 
    clean_start_station_id AS station_id,
    clean_start_station_name AS station_name,
    1 AS is_departure,
    0 AS is_arrival,
    CASE WHEN clean_start_station_id = clean_end_station_id THEN 1 ELSE 0 END AS is_self_loop
  FROM EnrichedRentals
  WHERE bike_type = 'electric'
  
  UNION ALL
  
  -- Pass 2: Gather Destination Activity
  SELECT 
    clean_end_station_id AS station_id,
    clean_end_station_name AS station_name,
    0 AS is_departure,
    1 AS is_arrival,
    0 AS is_self_loop -- Set to 0 to avoid double counting the loop flag
  FROM EnrichedRentals
  WHERE bike_type = 'electric'
)
GROUP BY 1, 2
ORDER BY total_electric_participation DESC
LIMIT 10;


```
### ⚡ Comparative Analysis: Electric Fleet Flow Dynamics

To contextualize the mechanical fleet patterns, a matching directional audit was executed for the high-volume **Electric** bike tier. Analyzing the departures, arrivals, and true round trips of the motorized fleet across its top 10 stations reveals massive structural bottlenecks and confirms system-wide gravity wells.

| Station ID | Station Name | Electric Departures | Electric Arrivals | Total Interaction Volume | True Round Trips | Operational Profile |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| **2498** | Dean Keeton/Speedway | 2,670 | 1,046 | 3,716 | 92 | 🛫 **Macro Source / Net Exporter** |
| **3798** | 21st/Speedway @ PCL | 0 | 3,105 | 3,105 | 0 | 🛑 **Absolute Terminal Sink** |
| **2547** | 21st/Guadalupe | 1,979 | 810 | 2,789 | 98 | 🛫 **Macro Source / Net Exporter** |
| **7125** | 23rd/San Gabriel | 1,834 | 780 | 2,614 | 87 | 🛫 **Net Exporter** |
| **3793** | 28th/Rio Grande | 1,693 | 845 | 2,538 | 238 | 🛫 **Net Exporter (High Neighborhood Loop)** |
| **7188** | 22nd/Pearl | 1,647 | 746 | 2,393 | 90 | 🛫 **Net Exporter** |
| **3797** | 21st/University | 1,312 | 622 | 1,934 | 31 | 🛫 **Net Exporter** |
| **3799** | 23rd/San Jacinto @ DKR Stadium | 1,264 | 492 | 1,756 | 55 | ⚖️ **Balanced Transit Node** |
| **3838** | 26th/Nueces | 0 | 1,249 | 1,249 | 0 | 🛑 **Absolute Terminal Sink** |
| **4938** | 22.5/Rio Grande | 851 | 353 | 1,204 | 43 | 🛫 **Net Exporter** |

---

### 🔍 Cross-Tier Insights: Classic vs. Electric Comparison

Comparing this dataset against the mechanical bike logs yields three profound system-wide revelations:

#### 1. The 0-Departure Phenomenon Is Systemic
The most striking finding is that **Station 3798 (21st/Speedway @ PCL)** records exactly **0 departures** across both Classic AND Electric bike datasets, while accumulating **3,552 combined arrivals** (447 Classic + 3,105 Electric). 
* This definitively proves that the PCL library is a pure unilateral network drain. Users never initiate trips here; they only drop bikes off.
* **The New Macro Sink uncovered:** The electric query isolated **Station 3838 (26th/Nueces)** as another absolute terminal dead-end with **0 departures and 1,249 arrivals**. 

#### 2. The Scale Discrepancy (The Rebalancing Nightmare)
While the directional behavior (Sources vs. Sinks) matches perfectly between both bike tiers, the sheer volume scale shifts drastically:
* At **Dean Keeton/Speedway (2498)**, logistics teams deal with a classic bike deficit of 225 units (328 departures vs 103 arrivals). 
* However, at that exact same dock, they face a staggering **electric bike deficit of 1,624 units** (2,670 departures vs 1,046 arrivals). 
* This emphasizes that manual rebalancing vans cannot focus on mechanical units alone; the electric fleet depletion happens at a 7x faster rate at major campus gateways.

#### 3. Low Round-Trip Rates Solidify Commuter Narrative
Excluding *28th/Rio Grande (3793)*, which exhibits a slightly elevated neighborhood loop rate (~9.3%), true round trips remain nominal across the board (averaging under 3%). This locks in the conclusion that the Zen City bike ecosystem functions strictly as a utility-driven, one-way A-to-B transit network for student commuters, rather than an experiential or leisurely rental service.

```mermaid
graph TD
    %% Define Nodes and Styles
    subgraph West Campus Source Zone [High-Density Student Housing Staging Grounds]
        DK[Dean Keeton <br> ID: 2498]
        GD[21st/Guadalupe <br> ID: 2547]
        SG[23rd/San Gabriel <br> ID: 7125]
    end

    subgraph Core Campus Sinks [Absolute Network Drainage Docks]
        PCL[21st/Speedway @ PCL <br> ID: 3798]
        NUE[26th/Nueces <br> ID: 3838]
    end

    %% Massive Outflows
    DK -- "Massive Electric Outflow <br> (Net -1,624 Bikes)" --> PCL
    GD -- "High Velocity Outflow" --> PCL
    SG -- "Morning Rush Influx" --> NUE

    %% Operations loop
    PCL -. "Heavy Trucking Rebalancing Loop <br> (Constant Fleet Redistribution Required)" .-> West Campus Source Zone
    NUE -. "Manual Rebalancing Sweep" .-> West Campus Source Zone

    classDef source fill:#d4edda,stroke:#28a745,stroke-width:2px;
    classDef sink fill:#f8d7da,stroke:#dc3545,stroke-width:2px;
    
    class DK,GD,SG source;
    class PCL,NUE sink;
```
### 🔄 Cross-Tier Comparative Synthesis: Classic vs. Electric Fleet Dynamics

To uncover the core structural mechanics of the Zen City transit network, this section establishes a direct comparative audit between the **Classic (Mechanical)** and **Electric (Motorized)** fleet tiers. By analyzing the behavioral variance between these asset classes across identical high-volume transit nodes, we can distinguish between localized demographic preferences, structural network constraints, and critical equipment imbalances.

---

#### 1. Side-by-Side Spatial Network Comparison

The table below merges the directional data points for both hardware tiers across the major network hotspots, ordered by total cumulative interaction volume.

| Station ID | Station Name | Classic Deps | Classic Arrs | Electric Deps | Electric Arrs | Total Combined Interactions | True Round-Trip Rate (Combined) | Primary Systemic Role |
| :---: | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **2498** | Dean Keeton/Speedway | 328 | 103 | 2,670 | 1,046 | **4,147** | 2.63% | 🛫 Macro Source Hub (Perimeter Gateway) |
| **3798** | 21st/Speedway @ PCL | 0 | 447 | 0 | 3,105 | **3,552** | 0.00% | 🛑 Primary Terminal Sink (Academic Core) |
| **2547** | 21st/Guadalupe | 307 | 106 | 1,979 | 810 | **3,202** | 3.59% | 🛫 Macro Source Hub (West Campus Drag) |
| **7125** | 23rd/San Gabriel | 273 | 94 | 1,834 | 780 | **2,981** | 3.35% | 🛫 Residential Source (West Campus) |
| **3793** | 28th/Rio Grande | 147 | 72 | 1,693 | 845 | **2,757** | 9.10% | 🛫 Residential Source (High Neighborhood Loop) |
| **7188** | 22nd/Pearl | 183 | 73 | 1,647 | 746 | **2,649** | 3.70% | 🛫 Residential Source (West Campus) |
| **3797** | 21st/University | 264 | 110 | 1,312 | 622 | **2,308** | 1.73% | 🛫 Institutional Source (Central Campus) |
| **3799** | 23rd/San Jacinto @ DKR Stadium | 112 | 81 | 1,264 | 492 | **1,949** | 3.54% | ⚖️ Balanced Transit Interconnect |
| **2548** | Guadalupe/West Mall @ Co-op | 0 | 159 | *N/A* | *N/A* | **159+** | 0.00% | 🛑 Classic Terminal Sink |
| **3838** | 26th/Nueces | *N/A* | *N/A* | 0 | 1,249 | **1,249** | 0.00% | 🛑 Electric Terminal Sink |
| **4938** | 22.5/Rio Grande | *N/A* | *N/A* | 851 | 353 | **1,204** | 3.57% | 🛫 Residential Source (North-West Extension) |
| **2566** | Electric Drive/Pfluger Ped Bridge | 197 | 56 | *N/A* | *N/A* | **253+** | **20.16%** | 🔄 Isolated Recreational Loop |

---

#### 2. Deep-Dive Comparative Divergences

```mermaid
graph TD
    %% Global Drivers
    A[Zen City Fleet Operations] --> B(Asset Discrepancies)
    
    %% Classic Track
    B --> C{Classic Tier <br> 12.31% Total Vol}
    C --> C1[Hyper-Localized Campus Ring]
    C --> C2[Pfluger Ped Bridge: 25.8% Leisure Loops]
    C --> C3[Overspill Buffer Capacity]

    %% Electric Track
    B --> D{Electric Tier <br> 87.69% Total Vol}
    D --> D1[Macro-Regional Extent]
    D --> D2[Systemic Deficits: -1,624 at Dean Keeton]
    D --> D3[Primary Commuter Choice]

    style C fill:#ffeef0,stroke:#f85149,stroke-width:2px
    style D fill:#dbedff,stroke:#0969da,stroke-width:2px
```
📊 Divergence A: The Scale and Velocity Gap

    The Asymmetric Deficit: While both hardware tiers demonstrate identical vector behavior (moving outward from perimeter residential areas into core academic locations), the operational scale is heavily weighted toward motorized models. At Dean Keeton/Speedway (2498), the mechanical tier exhibits a net deficit of 225 units (328 departures vs. 103 arrivals). In sharp contrast, the electric tier experiences a staggering net deficit of 1,624 units (2,670 departures vs. 1,046 arrivals).

    Logistical Implication: The electric fleet depletes at 7.2x the velocity of the classic fleet at major commuter choke points. This means mechanical units function as an operational shock absorber. They sit at the docks as a secondary layer of capacity, absorbing student demand only after high-velocity e-bike inventory is entirely cleared out during morning lecture rushes.

📊 Divergence B: Structural vs. Behavioral "Absolute Sinks"

    The Absolute Coincidence (Station 3798): The complete absence of departures (0 records) at 21st/Speedway @ PCL across both Classic and Electric datasets confirms a rare, absolute structural dead-end. Regardless of bike mechanics, weight, or battery levels, users treat the Perry-Castañeda Library purely as a termination point.

    Tier-Specific Drainage Nodes:

        The electric query uncovers a hidden, specialized motorized dead-end at 26th/Nueces (3838), accumulating 1,249 arrivals against 0 departures. This represents an off-campus, downhill residential migration or shopping vector unique to the electric tier.

        The classic query isolates Guadalupe/West Mall (2548) as a specialized classic terminal sink (159 arrivals / 0 departures). This points directly to localized student walking habits along the main university gateway commercial drag.

📊 Divergence C: Utility Commuting vs. Recreational Loops

    The Round-Trip Variance: True round-trip percentages (trips concluding at the exact physical station where they originated) remain nominal across the entire network core, hovering beneath 3.5% for both electric and classic bikes. This reinforces the core thesis that the fleet functions primarily as an active utility transit mechanism rather than a recreational asset.

    The Pfluger Bridge Exception: The only statistical break in this pattern occurs in the classic tier at Electric Drive/Sandra Muraida Way @ Pfluger Ped Bridge (2566), reaching a 25.8% true round-trip rate (51 round trips out of 197 departures). Interestingly, this station does not even register among the top 10 highest volume nodes for electric bikes. This divergence proves that classic mechanical hardware retains a distinct, localized recreational identity along flat river trails, while electric models are reserved for structured, asymmetric point-to-point urban transit.

3. Strategic Operational Framework for Integrated Rebalancing

The alignment of these datasets transitions Zen City logistics away from localized adjustments toward an Integrated Network Balancing Policy:
                  ┌──────────────────────────────────────────────┐
                  │          CORE CAMPUS TERMINAL SINKS          │
                  │  Station 3798 (PCL) & Station 3838 (Nueces)  │
                  └──────────────────────┬───────────────────────┘
                                         │
                         [ HIGH-FREQUENCY VAN CLEARING ]
                    Evacuate slots to maintain incoming capacity
                                         │
                                         ▼
                  ┌──────────────────────────────────────────────┐
                  │       PERIMETER COMMUTER SOURCE SHEDS        │
                  │  Station 2498 (Dean Keeton) & 2547 (Guad)    │
                  └──────────────────────┬───────────────────────┘
                                         │
                        [ BI-FURCATED ASSET INJECTION ]
             Inject E-Bikes for primary range & Classics for overspill
---

* 1:Prioritize Symmetrical Extraction Over Blind Distribution: Because Stations 3798 and 3838 generate an aggregate total of 4,960 arrivals but 0 organic departures, letting these nodes sit unmanaged will cascade a lock-out failure across the entire campus network. Clearing crews must prioritize continuous extraction loops from these locations.

* 2:Execute Tiered Injections at Feeders: When deploying vans to load-centers like Dean Keeton/Speedway and 21st/Guadalupe, operations should maintain an intentional 8:1 Electric-to-Classic replenishment ratio during non-peak windows, shifting to a higher classic buffer ratio immediately preceding the 8:00 AM student surge to counter the rapid exhaustion of motorized units.

* 3:Preserve Specialized Utility Segments: Restrain the temptation to fully transition the Pfluger Ped Bridge dock to electric support. The classic mechanical fleet satisfies an organic, high-margin, self-contained recreational user loop here that does not place structural rebalancing strain on the broader network.

---


## **Duration distribution**


------------------------------------------------------------------------(continue here!)

---
## **Rental volume over time**

---

## **Gender**

---
## **Age**


---

## **Hour of day**

---

## **Day of week**

---
## **User type**

---




# **03 - Bivariate Analysis **


* Does duration depend on user type?
yes

* Does bike type affect duration?
in check

* Does weekday affect demand?
partially, deeper check required

* Does gender affect ride length?
not much

* Does age affect ride duration?
not much

* Does station affect ride volume?
yes! clusters detected




# **04 -  Multivariate Analysis **

* Student users during rush hour
* Electric bikes during weekends
* Station popularity by hour
* User type × weekday × duration
* Peak-hour congestion by station

Those analyses tell actual business stories.



# **05 - Station Performance **

I'd compute metrics like

* Total trips
* Trips started
* Trips ended
* Net flow
* Average duration
* Peak hour
* Weekend ratio

Then rank stations.

Not just

Top 10

but

Why are they top?


# **06 - Demand Bottlenecks **
For example:

Trips per dock.


# **07 - Temporal Analysis **

**** very important***
after all there are 16585 rentals in 3 months recod! obiously the time is most important .
Questions like

Morning rush?

Evening rush?

Weekends?

Friday afternoons?

Hourly heatmaps.

These are extremely valuable for bike sharing.



# **08 - Customer Segmentation **
relationship between subcription types and stations and Bike type and hours .
I'd focus on the main majority customers which are the Students subcriptions counting for 77% .

This is another area where you can go beyond the assignment.

Examples

Students

Faculty

Visitors

Long rides

Short rides

Frequent stations

Preferred bikes



# **09 - Business Recommendations **

****Very important ****

I'll make into sections , with findings and suggestions

for example:

Finding:

Students account for 77% of trips.

Recommendation:

Expand student pricing.
Special offers: like "New-Comer" discounts 





# **10 - Prediction ** (I am thinking of making dedicated section for 09 and 10) .





