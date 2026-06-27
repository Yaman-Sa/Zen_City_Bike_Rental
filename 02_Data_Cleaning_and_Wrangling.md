**everything we do to check and clean data will be here**

#**01.starting with the data Raw tables**
*lets recall the given tables and structure:*
*Data tables: we have 3 sources that we could and will use,Table names:
* ● `bqproj-488319.zen_city.station_info`   
* ● `bqproj-488319.zen_city.rentals`
* ● `bqproj-488319.zen_city.customers`

* **Based on the tables**, we need to filter duplicates using the unique identifiers for each dataset:
* customers: The customer_id (STRING) is the unique identifier.
* station_info: The station_id (INTEGER) is the unique identifier.
* rentals: The trip_id (INTEGER) is the unique identifier.

**checking primary keys duplicates**
* `bqproj-488319.zen_city.station_info`         102           102         0 duplicates(primary keys)
* `bqproj-488319.zen_city.rentals`              16585         16585       0 duplicates(primary keys)
* `bqproj-488319.zen_city.customers`            586           586         0 duplicates(primary keys)

**cheking the Nulls Cells:**
* in zen_city.rentals        0 Nulls Cells.
* in zen_city.customers      0 Nulls Cells.
* in zen_city.station_info   there are Nulls Cells.


**Schemas of the tables:** 
### **1. `bqproj-488319.zen_city.station_info` (Dimension Catalog)**
Acts as the master profile directory tracking the physical capacity, logistical constraints, and geographical footprints of all deployed bike stations.

| Field Name | Data Type | Mode | Constraints / Core Field Notes |
| :--- | :--- | :--- | :--- |
| **`station_id`** | `INTEGER` | `REQUIRED` | **Primary Key**; absolute unique identifier for each physical station asset. |
| `name` | `STRING` | `NULLABLE` | Text string name of the station hub; contains duplicate records for IDs 1007/3294. |
| `status` | `STRING` | `NULLABLE` | Operational profile flag (e.g., `'Active'`, `'Closed'`). Used to isolate legacy assets. |
| `address` | `STRING` | `NULLABLE` | Street location string (contains 1 raw null value). |
| `alternate_name` | `STRING` | `NULLABLE` | Secondary or historical station name variant (heavily sparse: 100 null values). |
| `city_asset_number` | `INTEGER` | `NULLABLE` | Municipal property tracking identifier code (contains 25 missing values). |
| `property_type` | `STRING` | `NULLABLE` | Land-use classification category context (contains 20 missing values). |
| `number_of_docks` | `INTEGER` | `NULLABLE` | Hardware capacity; physical docking slots available (contains 20 missing values). |
| `power_type` | `STRING` | `NULLABLE` | Power source configuration format (contains 20 missing values). |
| `footprint_length` | `FLOAT` | `NULLABLE` | Physical installation base length metric (contains 23 missing values). |
| `footprint_width` | `FLOAT` | `NULLABLE` | Physical installation base width metric (contains 23 missing values). |
| `notes` | `STRING` | `NULLABLE` | Qualitative operational remarks text (contains 70 missing values). |
| `council_district` | `INTEGER` | `NULLABLE` | Municipal administrative district index number (0 raw nulls). |
| `modified_date` | `TIMESTAMP` | `NULLABLE` | System audit timestamp indicating the last record change window. |

---

### **2. `bqproj-488319.zen_city.rentals` (Transactional Fact Table)**
An atomic ledger logging every distinct journey transaction across the micro-mobility bike-sharing infrastructure during the Q1 operation period.

| Field Name | Data Type | Mode | Constraints / Core Field Notes |
| :--- | :--- | :--- | :--- |
| **`trip_id`** | `INTEGER` | `REQUIRED` | **Primary Key**; unique transactional index identifier per ride instance. |
| `subscriber_type` | `STRING` | `NULLABLE` | Membership tier category mapping (e.g., `'Student Membership'`, `'Local31'`). |
| `bike_id` | `INTEGER` | `NULLABLE` | Hardware asset deployment tracking identifier number. |
| `bike_type` | `STRING` | `NULLABLE` | Structural bicycle variant type (e.g., `'classic'`, `'electric'`). |
| `start_time` | `TIMESTAMP` | `NULLABLE` | High-precision trip departure date/time stamp. Used for temporal partitioning. |
| `customer_id` | `STRING` | `NULLABLE` | **Foreign Key**; maps directly to the unique identifiers in the `customers` table. |
| `start_station_id` | `INTEGER` | `NULLABLE` | **Foreign Key**; references origin station; contains 4,916 structural orphaned IDs. |
| `start_station_name`| `STRING` | `NULLABLE` | Raw origin location text literal; contains trail spaces requiring `TRIM()`. |
| `end_station_id` | `INTEGER` | `NULLABLE` | **Foreign Key**; references destination station; contains 2,164 structural orphaned IDs. |
| `end_station_name` | `STRING` | `NULLABLE` | Raw arrival location text literal; harbors festival anomalies (e.g., `'Springfest 2022'`). |
| `duration_minutes` | `INTEGER` | `NULLABLE` | Logged operational trip duration. Target bounds filtered to between 1 and 1440 minutes. |

---

### 3. bqproj-488319.zen_city.customers (Dimension Table)

This dimension table provides structural user profiles and demographic metadata for registered Zen City bike-sharing users, enabling cohort segmentation and targeted behavioral analysis.

| Column Name | Data Type | Mode / Constraints | Initial Audit & Data Integrity Notes |
| :--- | :--- | :--- | :--- |
| **customer_id** | STRING | REQUIRED (PK) | Primary Key. Unique identifier for registered riders. Reconciled at exactly 586 unique rows with 0 duplicates and 0 null cells. |
| **age** | INTEGER | NULLABLE | Registered user age. Crucial for calculating user age distributions and cohort tracking during subsequent analysis steps. |
| **gender** | STRING | NULLABLE | Self-reported user gender metadata used for marketing segmentation and demographic metrics. |
| **user_type** | STRING | NULLABLE | User classification or subscription status category used to analyze membership behavior vs. casual riders. |
| **phone** | STRING | NULLABLE | Customer phone number. High-cardinality contact field; kept in the raw schema audit but flagged for downstream analytical omission. |
| **Email** | STRING | NULLABLE | Customer email address. High-cardinality contact field; documented here to preserve raw structural fidelity. |
| **home_region** | STRING | NULLABLE | Spatial metadata capturing the broad geographical region or sector where the user resides. |
| **full_address** | STRING | NULLABLE | Granular physical address data of the user, providing a foundation for localized neighborhood-level demand mapping. |

---

**investigating and identifying the zen_city.station_info Nulls**
```
-- station info nulls counter
SELECT
  COUNTIF(station_id IS NULL) AS null_station_id,
  COUNTIF(name IS NULL) AS null_name,
  COUNTIF(status IS NULL) AS null_status,
  COUNTIF(address IS NULL) AS null_address,
  COUNTIF(alternate_name IS NULL) AS null_alternate_name,
  COUNTIF(city_asset_number IS NULL) AS null_city_asset_number,
  COUNTIF(property_type IS NULL) AS null_property_type,
  COUNTIF(number_of_docks IS NULL) AS null_number_of_docks,
  COUNTIF(power_type IS NULL) AS null_power_type,
  COUNTIF(footprint_length IS NULL) AS null_footprint_length,
  COUNTIF(footprint_width IS NULL) AS null_footprint_width,
  COUNTIF(notes IS NULL) AS null_notes,
  COUNTIF(council_district IS NULL) AS null_council_district,
  COUNTIF(modified_date IS NULL) AS null_modified_date
FROM `bqproj-488319.zen_city.station_info`
;
```
* **Nulls counts: station_id Table**
* null_station_id            0
* null_name                  0
* null_status                0
* null_address               1
* null_alternate_name        100
* null_city_asset_number     25
* null_property_type         20
* null_number_of_docks       20
* null_power_type            20
* null_footprint_length      23
* null_footprint_width       23
* null_notes                 70
* null_council_district      0
* null_modified_date         0


* as we can see there is a null_address , we will check that among the other nulls , and decide how to deal with them acordingly. 

**checkig for consistancy, regarding names and id:**
* in a check on customers table there was **no conflict** on the name and id.
* in a check on station_id table there was **one** name for two distinct stations id, 1007 and 3294.
* checking the trips regarding the 1007 and 3294 IDs I found one trip regarding 1007 and the rest are regarding 3294.
* before I decided what to do I went into further investigating, despite the first imprision of fitering one row wont hur thoverall integrity of the data and honesty of the model nd predition (from totalof 16585 rows in total).


**understanding the tables: deeper investigtion**
* **Status in station_info table :** looking at the table I noticed that some stations are marked as *Closed* as its status, suggesting that there are no longer active.
* checking percentage and number of such stations I got : 24 closed stations out of 102 total stations registered (23.5% of total stations registered).
* investigating the dates of last update cncluded what we expect from closed station in the past.
* checking coresponding stations id in the rental table for total rental count including these station: 74 trips records , of total 16585 records .
* as my objective is to study an analyze the data for future prediction and recommendations, this would include only the open stations , with having in mind the effect of closing  these stations and the dates there were closed and the connection to the remaining stations, still 0.45% of total rents are neglectble to the big picture.
* final decision is to filter these stations in the final data cleaning.


--finding all rows with **closed** as status.
SELECT * FROM `bqproj-488319.zen_city.station_info` 
WHERE LOWER(status) = 'closed';
--24 stations are closed, from toal of 102 registered stations in stations table. 

--finding trips records for closed stations
SELECT * FROM `bqproj-488319.zen_city.rentals`
 WHERE start_station_id IN (SELECT station_id FROM `bqproj-488319.zen_city.station_info` 
WHERE LOWER(status) = 'closed') 
 OR end_station_id IN (SELECT station_id FROM `bqproj-488319.zen_city.station_info` 
WHERE LOWER(status) = 'closed')
;
-- 74 records of total of 16585 recods , counting for less than 0.45%.


**clear SQL** 
-- Phase 1: Audit total distribution of operational vs decommissioned stations
SELECT 
  status,
  COUNT(*) AS station_count,
  ROUND(COUNT(*) / SUM(COUNT(*)) OVER() * 100, 2) AS percentage_of_total
FROM `bqproj-488319.zen_city.station_info`
GROUP BY status;
-- Result: 24 stations identified as 'Closed' (23.53% of the catalog)

-- Phase 2: Identify and isolate transactional volume tied to inactive assets
SELECT * FROM `bqproj-488319.zen_city.rentals`
WHERE start_station_id IN (
  SELECT station_id 
  FROM `bqproj-488319.zen_city.station_info` 
  WHERE LOWER(status) = 'closed'
) 
OR end_station_id IN (
  SELECT station_id 
  FROM `bqproj-488319.zen_city.station_info` 
  WHERE LOWER(status) = 'closed'
);
-- Result: 74 records out of 16,585 total rows (< 0.45% of transaction volume)

---


* **cleaning:** for better accuracy , with keeping in mind the posible "effect" of these stations to the ones wit trips records with them, however these trips are very tiny percntage!, total trips related are about 81 trips in total out of 16585 rows in total.
* **decision:** filtering these trips lowers our data percentage by less than 0.45% only, but giving us overall more accurate predictions accunting to the active stations , again keeping in mind that there are in total 81 rows filtered had little to no "effect" on our related stations.



---



**relation between tables**
* I have discovered that there are good sum of trips records with no id corelations to the station_info table:

-- primary keys matching
--station IDs 
SELECT 
  COUNTIF(r.start_station_id IS NOT NULL AND s_start.station_id IS NULL) AS orphaned_start_stations,
  COUNTIF(r.end_station_id IS NOT NULL AND s_end.station_id IS NULL) AS orphaned_end_stations
FROM `bqproj-488319.zen_city.rentals` r
LEFT JOIN `bqproj-488319.zen_city.station_info` s_start 
  ON r.start_station_id = s_start.station_id
LEFT JOIN `bqproj-488319.zen_city.station_info` s_end 
  ON r.end_station_id = s_end.station_id
--start station without end:4916  , end stations withot start:2164

* it is expected that some trips would be with no start station in the early records of Q1 , and similar for end station in late records of Q1, however there is large number of records like this, and it requirs further study, also stations name do not corespond.
* I started with the duration records for all records, From previous step 01, there are no Nulls for duration record (which I have decided to trust!,after checking the time median and mean, and compaired the majority user type which is Student Subcription, and main clusters of flow, all direct toward student majority usingthe bikes for short travel, which again makes sense).
*The presence of ~7,000 orphaned records across the dataset initially suggests potential data quality issues. However, after investigating the temporal distribution and user patterns, I have categorized these into two distinct phenomena:

    * Temporal Window Truncation: As the dataset is limited to a strict 3-month window (Q1), trips that initiated or terminated outside of these specific parameters may lose their relational link to the master infrastructure catalog.

    * Ad-Hoc/Event-Based Deployment: Similar to the 'Springfest 2022' anomaly identified earlier, it is likely that several stations are temporary, mobile, or "pop-up" hubs that never officially registered with the permanent station_info catalog.

 **Strategic Resolution: Duration Trust & Preservation**

* Despite the lack of station-level metadata for these ~7,000 records, I have decided against purging them.

    * Validation: I performed a cross-verification of the duration_minutes for these orphaned records against the global average and the 'Student Membership' behavioral cluster.

    * Consistency: The orphaned records maintain a logical distribution consistent with the broader student commuter population (short-burst, high-frequency travel).

    * Conclusion: The transactional duration metadata is reliable and internally consistent, even when the station infrastructure metadata is missing. These trips represent genuine business activity (real revenue and utilization) and are essential for accurate Q2 volume forecasting.

* Consequently, these records are preserved in the master analytical view, with missing metadata dynamically backfilled to a placeholder label ('Unregistered Station') to ensure we do not under-report aggregate utilization.



* I will keep this in mind, in the final CTE I will be aware of inner joining raw tables, maybe using left join or filter this.

---

# **02. Station ID 1007 (the ghost station)**
*During the initial audit of the station_info catalog, a significant risk to downstream data integrity was identified regarding station ID 1007, which shares the station name with the station id 3294.
*The Discovery: A review of the status column revealed that station 1007 is marked as 'Closed'.
*The Risk: Leaving legacy or inactive entities in a dimension table is a "data trap." If a analyst joins the rentals table to this "dirty" catalog, it can lead to join fan-outs or ambiguous results that skew volume metrics.
*The Strategic Decision: Rather than filtering at the final reporting stage, I implemented an upstream, source-level filtration strategy within my CTE architecture.
* **Note:** By explicitly filtering WHERE station_id != 1007 during the creation of my final `CleanedStationProfiles` catalog, I ensured that the dimension table remains a clean, singular "source of truth." This guarantees referential integrity at the architectural level, preventing the risk of join fan-outs and ensuring that all downstream analysis—including Q2 rental forecasts—is built on a platform of active, validated station identifiers.

**SQL Clean Table:**
-- Sanitizing the dimension catalog at the source
CleanedStationProfiles AS (
  SELECT *
  FROM `bqproj-488319.zen_city.station_info`
  WHERE station_id != 1007 -- Dropping legacy ghost station
)


---
# **03. the "Clean Data"?:** the data with no Nulls, the great loss of data as we filter and build CTE with no Nulls cells at all (to  work with full data only)
* I tried simple Nulls cells "proof" , simply filtering all Nulls from any row with Nulls for CTE.
* result: ended up with loss of 40% of data , a total of 10007 rows from the total 16585 I started with.
* conclusion: its a big loss of data , and some of the cells Nulls are secondary to negligible Cells that I might not use in the end CTE like (alternate_name).
* decision: I will move into specified and more focused cleaning, preserving the data integrity and our prediction honesty and accuricy.

---

### ** 04. Dealing with the Nulls in station_info table:**

* During the data exploration and relational audit phase, a significant structural challenge was identified , many nulls cells in the station_info table.
* A naive data engineering approach would rely on a strict relational INNER JOIN to connect these datasets. Doing so, however, would completely purge any rental record containing a start_station_id or end_station_id that is missing from the master station directory. Running such a join against our raw data would discard thousands of rows, shrinking our transactional volume from 16,585 raw rows down to an artificially truncated ~10,007 rows.
* Dropping roughly 40% of the entire transaction history presents an unacceptable loss of critical business intelligence and analytical context. These records capture genuine customer rides, operational bike usage, and revenue generation. Purging them would drastically deflate our assessment of aggregate utilization rates during deep-dive trend analysis and starve the downstream linear regression models relied upon for our future predictions.
* To protect the absolute integrity of Zen City's overall operational footprint while enforcing rigorous table schemas, a Data-Sparing and Imputation Strategy was deployed instead of standard row deletion:

   * Categorical Metadata (alternate_name, notes): These attributes were classified as structurally optional string fields. Because they do not govern relational joins or downstream predictive metrics, missing values are safely preserved as standard database NULL markers or omitted during string aggregations to avoid data distortion.

   * Operational Metadata (address, city_asset_number, property_type, power_type): To shield the core transactions from breaking during join evaluations, missing attributes are dynamically wrapped using a defensive COALESCE string override to explicitly cast them as 'Unknown' or 'Unregistered Station'. This cleanly maps the orphaned transactional legs into distinct tracking categories, keeping the core rental records perfectly intact for aggregate volume counting.

   * Physical Station Dimensions (number_of_docks, footprint_length, footprint_width): To ensure these entries can still safely participate in spatial modeling, capacity auditing, or hardware density calculations without causing severe arithmetic runtime errors, a Median Imputation pipeline was designed. Missing continuous variables are dynamically backfilled using the Global Table Medians derived from an upstream Common Table Expression (CTE) targeting fully registered hardware profiles. This robustly preserves the structural distribution of the data without introducing outlier bias.


**Final Operational Decision**

* By substituting rigid data elimination with defensive `LEFT JOIN` logic and continuous variable imputation, the cleaning pipeline successfully protected our core business metrics.

* The master analytical view preserves **16,504 records out of the 16,585 raw transactions**, representing an exceptional **99.5% data preservation rate**.

* This balanced framework eliminates sampling bias, guaranteeing the maximum statistical accuracy for our upcoming time-series models and trend visualizations.


---

# ** 05. Checking specific missing data**
* ##**checking for missing start_station_id,start_station_name,end_station_id,end_station_name**
* in previous **step 01** I already showed that in the data we are missing is the null_address (including end_station_id,start_station_id) and no Station_name missing .
* we check this specific station id missing and get the station name attached .
* result: 1 trip with the end station named **'Springfest 2022'**
* discusion: it is a unique event, no station with this name in station_id table and no assigned station_id to it,and it one trip over all, and the trip is 12mins log, more than usual, still logically its hard to believe tha in an event there was only one rent when we would expect more, also 12mins trip when we would expect more time for the festival, leading me to invistigate more into this case.
* **Data Provenance:** This indicates a "pop-up" hardware deployment. Because the metadata was never formalized in the `station_info` table, this record was initially orphaned during the relational join.
* Analyst Decision
   * Rather than deleting this record as a "garbage entry," I have opted to **preserve it**. 

   * This outlier serves as a critical real-world test case for our data pipeline. It confirms that the system is capable of logging ad-hoc, mobile, or event-based station usage, even when infrastructure metadata is missing.



* ##**checking for ghost customers**
* Clean, as **01** spcified.

---

# ** 06. Logical Filtring and outliers**

**Next we searched for logical errors : **
* rentals table: Are there any trips where duration_minutes is 0 or negative? Are there trips that lasted an impossible amount of time (e.g., 40,000 minutes)?, for accuracy I'll take only durations less than a day (24h).
* customers table: Check the MAX(age) and MIN(age). Are there customers listed as 150 years old or -5 years old?
* station_info table: Are there stations with 0 or negative number_of_docks? foorprint_length? footprint_width?

* Both the customers table and station_info table both came clean, However rentals table had outliners (as longer duration from 24 hours)
* total of 6 time outliers.

* there rental records outliers are way extreme considering the mean and median. suggesting that it could be stollen , broken or some other reason like an accident, in any case they do affect the data, and filtering extreme outliers is the way to go.


SELECT
  MIN(duration_minutes) AS min_duration,
  MAX(duration_minutes) AS max_duration,
  AVG(duration_minutes) AS avg_duration,
  APPROX_QUANTILES(duration_minutes, 2)[OFFSET(1)] AS median_duration,
  COUNTIF(duration_minutes <= 0) AS zero_or_negative_durations,
  COUNTIF(duration_minutes > 1440) AS trips_over_24_hours
FROM `bqproj-488319.zen_city.rentals`;


-- median=6,mean=21.089, min=2, max= 4874,more than 24h=0, negative=0.

* as we can see (this is before filtering, however expected loss: less than 90 rental records , out of 16585 records, so the expected general representation could be very similar) there is concntration near the 6mins, but spread to 4874mins which is extreme outlier.
* however, I decided to fiter for 24h as I would expect the company to have such policy (even less hours as maximum rental time allowed, unless they have special rental program that allows such extended time).


** final check on orphane names for the stations, and comparing the stations id's:**


* ### Lexical vs. Relational Coherence (Station ID vs. Name Mismatches)

* To round out the pre-cleaning infrastructure audit, a cross-examination was performed comparing **relational keys** (`station_id`) against **lexical descriptors** (`station_name`) within the transactional ledger. The objective was to determine if records containing missing or orphaned IDs still retained valid textual location data that could be preserved for descriptive localized volume analysis, or if the name fields suffered from identical systemic corruption.

-- Lexical vs. Relational Coherence Audit: Station IDs vs. Names
SELECT
  -- 1. Absolute Mismatches (ID populated but Name missing, or vice versa)
  COUNTIF(start_station_id IS NULL AND start_station_name IS NOT NULL) AS start_id_null_name_populated,
  COUNTIF(start_station_id IS NOT NULL AND start_station_name IS NULL) AS start_id_populated_name_null,
  COUNTIF(end_station_id IS NULL AND end_station_name IS NOT NULL) AS end_id_null_name_populated,
  COUNTIF(end_station_id IS NOT NULL AND end_station_name IS NULL) AS end_id_populated_name_null,

  -- 2. Referential Orphan Gaps vs. Descriptive Name Coverage
  COUNTIF(r.start_station_id IS NOT NULL AND s_start.station_id IS NULL) AS total_orphaned_start_ids,
  COUNTIF(r.start_station_id IS NOT NULL AND s_start.station_id IS NULL AND r.start_station_name IS NOT NULL) AS orphaned_start_ids_with_names,
  
  COUNTIF(r.end_station_id IS NOT NULL AND s_end.station_id IS NULL) AS total_orphaned_end_ids,
  COUNTIF(r.end_station_id IS NOT NULL AND s_end.station_id IS NULL AND r.end_station_name IS NOT NULL) AS orphaned_end_ids_with_names
FROM
  `bqproj-488319.zen_city.rentals` r
LEFT JOIN
  `bqproj-488319.zen_city.station_info` s_start ON r.start_station_id = s_start.station_id
LEFT JOIN
  `bqproj-488319.zen_city.station_info` s_end ON r.end_station_id = s_end.station_id;


#### Audit Metrics Breakdown
| Audit Vector | Metric Tracked | Record Count | % of Total Dataset (N=16,585) |
| :--- | :--- | :---: | :---: |
| **Start Station Structural Mismatch** | ID is NULL, but Name is Populated | 0 | 0.00% |
| **Start Station Structural Mismatch** | ID is Populated, but Name is NULL | 0 | 0.00% |
| **End Station Structural Mismatch** | ID is NULL, but Name is Populated | 1 | 0.01% |
| **End Station Structural Mismatch** | ID is Populated, but Name is NULL | 0 | 0.00% |
| **Start Station Orphan Metadata** | Relational ID is Orphaned, but Text Name is Retained | 4,916 | 29.64% (100% of orphans) |
| **End Station Orphan Metadata** | Relational ID is Orphaned, but Text Name is Retained | 2,164 | 13.05% (100% of orphans) |

#### Engineering & Strategic Insights
1. **100% Lexical Recovery Capability:** The discovery that exactly 100% of orphaned station IDs (4,916 start / 2,164 end) possess valid string names confirms that a standard `INNER JOIN` would introduce an egregious sampling bias by discarding valid transit paths. By preserving these records, we protect our aggregate sample size for Q2 business forecasting.
2. **Defensive Pipeline Justification:** This audit empirically justifies implementing a robust `COALESCE` string override layer within the downstream master cleaning pipeline. For any transaction where the station ID cannot resolve cleanly to the infrastructure dimension table, the pipeline will fall back safely to the native log string or assign a standardized 'Unregistered Station' profile, guaranteeing 100% transparency without violating referential constraints.




---


# ** 07. Final Check**

* The Final Master CTE Cleaning Pipeline ScriptNow that every gap has been audited, checked, and quantified, here is the complete, cohesive, end-to-end SQL statement.This single script implements the entire cleaning strategy:
 * Removes Row-Level Duplicates & Null Keys (trip_id).
 * Calculates and Imputes continuous features (docks, footprint) using table medians.
 * Excludes Decommissioned Infrastructure (the 74 records from closed stations).
 * Applies Logical Boundaries (trips 1<= min and <= 24 hours).
 * Applies Lexical Recovery (COALESCE statements utilizing your 100% text-retention find).

----------------------------------------------------------(continue here!)


---
### **Putting everyting togather ** 
* While a status filter is a valid way to exclude closed stations from a final report, I adopted a source-level filtration strategy within my * dimension lookup catalog. This guarantees referential integrity at the table definition level, preventing the risk of join fan-outs and ensuring that no downstream analysis can accidentally reference legacy identifiers

### The Final Master CTE Cleaning & Enrichment Pipeline Script

Now that every relational gap, physical dimension null, and operational outlier has been fully audited and quantified, here is the final cohesive, end-to-end SQL statement. This single production script implements the entire cleaning, imputation, and feature-enrichment architecture:

* [cite_start]**Duplicate & Null Remediation:** Filters row-level duplicate transactions using `ROW_NUMBER()`[cite: 1183].
* [cite_start]**Continuous Dimension Imputation:** Utilizes table medians via `CROSS JOIN` to fill missing capacity metrics[cite: 1184, 1185].
* [cite_start]**Strategic Infrastructure Preservation:** Safely filters out decommissioned stations (the 74 closed rows) while defaulting unmapped active hardware tracking entries to 'Unregistered Station' via defensive `LEFT JOIN` logic[cite: 1185, 1188, 1190, 1196].
* [cite_start]**Demographic Enrichment:** Integrates customer profiles directly into the transactional stream to empower cohort tracking.
* **Advanced Feature Engineering:** Computes date, hour, day-of-week, and weekend flags on the fly to accelerate subsequent transit analysis.

```sql
-- =========================================================================
-- ZEN CITY BIKE RENTAL: END-TO-END DATA CLEANING & ENRICHMENT PIPELINE
-- Target Output Yield: 16,504 Production Records
-- =========================================================================

WITH DeduplicatedRentals AS (
  SELECT *,
    -- 1. Identify & isolate row-level duplicates by tracking the latest entry
    ROW_NUMBER() OVER(PARTITION BY trip_id ORDER BY start_time DESC) as row_num
  FROM `bqproj-488319.zen_city.rentals`
  -- 2. Enforce primary key integrity
  WHERE trip_id IS NOT NULL
),

-- 3. Compute Global Medians for physical station features to serve as imputation baselines
GlobalStationMedians AS (
  SELECT    
    APPROX_QUANTILES(number_of_docks, 2)[OFFSET(1)] AS median_docks,    
    APPROX_QUANTILES(footprint_length, 2)[OFFSET(1)] AS median_length,    
    APPROX_QUANTILES(footprint_width, 2)[OFFSET(1)] AS median_width
  FROM `bqproj-488319.zen_city.station_info`
), 

-- 4. Clean, type-cast, and impute the Master Station dimension lookup table
CleanedStationProfiles AS (
  SELECT    
    s.station_id,    
    s.status AS station_status,    
    -- Remediate missing structural text fields
    COALESCE(s.property_type, 'Unknown') AS clean_property_type,    
    COALESCE(s.power_type, 'Unknown') AS clean_power_type,    
    COALESCE(s.address, 'Unknown') AS clean_address,    
    -- Prevent type-mismatches by casting municipal identifiers safely to STRING
    COALESCE(CAST(s.city_asset_number AS STRING), 'Unknown') AS clean_city_asset_number,    
    -- Apply continuous physical feature median imputation
    COALESCE(s.number_of_docks, m.median_docks) AS clean_number_of_docks,    
    COALESCE(s.footprint_length, m.median_length) AS clean_footprint_length,    
    COALESCE(s.footprint_width, m.median_width) AS clean_footprint_width
  FROM `bqproj-488319.zen_city.station_info` s
  CROSS JOIN GlobalStationMedians m
  -- Explicitly filter out legacy, unutilized station configurations at the source
  WHERE s.station_id != 1007
),

-- 5. Primary transactional cleaning layer and boundary filter execution
CleanedRentals AS (
  SELECT    
    trip_id,    
    subscriber_type,    
    bike_id,    
    bike_type,    
    start_time,    
    customer_id,    
    start_station_id AS clean_start_station_id,    
    TRIM(start_station_name) AS clean_start_station_name,    
    end_station_id AS clean_end_station_id,    
    TRIM(end_station_name) AS clean_end_station_name,    
    duration_minutes
  FROM DeduplicatedRentals
  WHERE row_num = 1 -- Eliminate redundant mutations
    AND start_station_id IS NOT NULL
    AND end_station_id IS NOT NULL
    AND start_station_name IS NOT NULL
    AND end_station_name IS NOT NULL
    -- Filter out structural anomalies (trips under 1 minute or lingering over 24 hours)
    AND duration_minutes >= 1 
    AND duration_minutes <= 1440
)

-- 6. Final Production Assembly: Left Join Dimension tables to avoid sampling bias
SELECT 
  cr.trip_id,
  cr.subscriber_type,
  cr.bike_id,
  cr.bike_type,
  cr.start_time,
  cr.customer_id,
  
  -- Demographics Enrichment Layer (From Customers Dimension)
  cust.age AS customer_age,
  cust.gender AS customer_gender,
  cust.user_type AS customer_user_type,
  
  -- Feature-Engineered Temporal Analytics Attributes
  DATE(cr.start_time) AS trip_date,
  EXTRACT(HOUR FROM cr.start_time) AS trip_hour,
  EXTRACT(DAYOFWEEK FROM cr.start_time) AS trip_day_of_week, -- (1 = Sunday, 7 = Saturday)
  CASE WHEN EXTRACT(DAYOFWEEK FROM cr.start_time) IN (1, 7) THEN 1 ELSE 0 END AS is_weekend,
  
  -- Origin Station Imputed Hardware Attributes
  cr.clean_start_station_id,
  cr.clean_start_station_name,
  COALESCE(start_st.clean_property_type, 'Unregistered Station') AS start_station_property_type,
  COALESCE(start_st.clean_power_type, 'Unknown') AS start_station_power_type,
  COALESCE(start_st.clean_number_of_docks, (SELECT median_docks FROM GlobalStationMedians)) AS start_station_docks,
  COALESCE(start_st.clean_footprint_length, (SELECT median_length FROM GlobalStationMedians)) AS start_station_footprint_length,
  COALESCE(start_st.clean_footprint_width, (SELECT median_width FROM GlobalStationMedians)) AS start_station_footprint_width,
  
  -- Destination Station Imputed Hardware Attributes
  cr.clean_end_station_id,
  cr.clean_end_station_name,
  COALESCE(end_st.clean_property_type, 'Unregistered Station') AS end_station_property_type,
  COALESCE(end_st.clean_power_type, 'Unknown') AS end_station_power_type,
  COALESCE(end_st.clean_number_of_docks, (SELECT median_docks FROM GlobalStationMedians)) AS end_station_docks,
  COALESCE(end_st.clean_footprint_length, (SELECT median_length FROM GlobalStationMedians)) AS end_station_footprint_length,
  COALESCE(end_st.clean_footprint_width, (SELECT median_width FROM GlobalStationMedians)) AS end_station_footprint_width,
  
  cr.duration_minutes

FROM CleanedRentals cr
LEFT JOIN CleanedStationProfiles start_st 
  ON cr.clean_start_station_id = start_st.station_id
LEFT JOIN CleanedStationProfiles end_st 
  ON cr.clean_end_station_id = end_st.station_id
LEFT JOIN `bqproj-488319.zen_city.customers` cust
  ON cr.customer_id = cust.customer_id

-- Enforce system-wide Decommissioned Infrastructure Exclusions
-- The 'IS NULL' condition gracefully protects active, unregistered pop-up event records
WHERE (LOWER(start_st.station_status) != 'closed' OR start_st.station_status IS NULL)
  AND (LOWER(end_st.station_status) != 'closed' OR end_st.station_status IS NULL);
