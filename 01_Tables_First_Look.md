# 01. Raw Tables: First Look & Initial Data Audit

## 📋 Overview & Objective
Before implementing any data cleaning models or structural modifications, a comprehensive raw audit was performed across Zen City's database. The objective of this "first look" phase is to inspect the foundational schema, confirm data types, validate primary key integrity, map out missing or corrupted information, and surface initial behavioral anomalies directly within the raw datasets. 

By establishing a clear understanding of the uncleaned baseline data, we ensure our downstream data-cleaning filters do not inadvertently distort our Q2 business predictions.

---

## 🗂️ Table Inventory & Profiles
[cite_start]The analysis relies on three core relational tables hosted within BigQuery:
1. `bqproj-488319.zen_city.station_info`: Hardware and logistical metadata for all bike stations.
2. `bqproj-488319.zen_city.rentals`: Granular transactional log of individual bike trips.
3. `bqproj-488319.zen_city.customers`: Demographic breakdown of registered bike-sharing users.

### **Raw Row Counts & Unique Identifiers**
To evaluate table scale and structural dimensions, the total volume of entries was cross-referenced against unique business keys:

| Table Name | Assigned Unique Key | Raw Row Count | Non-Duplicate Row Count | Duplicate Count |
| :--- | :--- | :--- | :--- | :--- |
| `zen_city.station_info` | `station_id` (INTEGER) | 16,585  | 16,585  | 0  |
| `zen_city.rentals` | `trip_id` (INTEGER) | 102  | 102  | 0  |
| `zen_city.customers` | `customer_id` (STRING) | 586  | 586  | 0  |

### **Baseline SQL Verification Snippet**
```sql
-- Validating uniqueness of primary keys across tables
SELECT COUNT(1) AS raw_rows, COUNT(DISTINCT station_id) AS unique_keys FROM `bqproj-488319.zen_city.station_info`;
SELECT COUNT(1) AS raw_rows, COUNT(DISTINCT trip_id) AS unique_keys FROM `bqproj-488319.zen_city.rentals`;
SELECT COUNT(1) AS raw_rows, COUNT(DISTINCT customer_id) AS unique_keys FROM `bqproj-488319.zen_city.customers`;
```
---

### **3. Attribute-Level Duplicate & Name Consistency Check**
Moving beyond primary keys, a secondary audit was conducted on descriptive fields (`name` attributes) to find hidden data redundancies:
* **`customers.name` Check**: Evaluated for name redundancy. The result showed 0 duplicate customer names, meaning each registered user is associated with a singular, distinct account.
* **`station_info.name` Check**: Uncovered critical logical duplicate vulnerabilities. Multiple rows share the exact same station name but are assigned completely different `station_id` markers (e.g., "Lavaca & 6th" split between ID `1007` and `3294`). 

**SQL Snippet to detect descriptive name overlaps:**
```sql
SELECT name, COUNT(*) as occurrence_count 
FROM `bqproj-488319.zen_city.station_info` 
GROUP BY name 
HAVING COUNT(*) > 1;
```

### 4. Deep-Dive Anomaly: "Lavaca & 6th" Station Identifier Split
* **The Mismatch:** The station name **"Lavaca & 6th"** is mapped to two completely distinct identification codes: `1007` and `3294`.
* **The Operational Cause:** An audit of the operational `status` column revealed that station `1007` is explicitly marked as **Closed**, while station `3294` is marked as **Active**. This confirms that they represent the exact same physical location over different operational windows (likely due to a hardware swap, kiosk upgrade, or system re-indexing).
* **The Analytical Impact:** If left uncorrected, any historical rental logs tied to the older ID `1007` would be isolated from the current station's metrics, resulting in an artificial underreporting of long-term traffic patterns at this high-density intersection.
* **The Data Preservation Strategy:** To maintain data continuity and accurately map network volume, we will implement conditional remapping logic during the wrangling phase. All transactional logs under ID `1007` will be combined into ID `3294`. Although the absolute volume of trips originating from ID `1007` is relatively small, this step ensures perfect alignment for our downstream spatial aggregation models.

/*
=============================================================================
Script: eda_isolate_lavaca_station.sql
Phase: 01 - Exploratory Data Analysis & Raw Table Audit
Objective: Isolate the "Lavaca & 6th" anomaly by matching by name or explicit 
           IDs to confirm the operational status conflict (Closed vs. Active).
=============================================================================
```
*/

SELECT 
  station_id,
  name AS station_name,
  status AS operational_status,
  address,
  number_of_docks,
  modified_date
FROM 
  `bqproj-488319.zen_city.station_info`
WHERE 
  -- Search explicitly for the duplicate text name or suspected IDs
  TRIM(name) = 'Lavaca & 6th' 
  OR station_id IN (1007, 3294)
ORDER BY 
  operational_status DESC;
---

SELECT 
  *
FROM 
  `bqproj-488319.zen_city.station_info`
WHERE 

  station_id IN (1007, 3294)

```  
---
** Same Table!** 
* in both queries I got the exact same table, suggesting that the count of trips for each distinct ID is **1** .
* filtiring the 1007 ID would be removng one trip from the record with 16585 trips in total, which could be the right move.
* however , for future projects with same situation but bigger data percentage Ill keep this row.


--- 

**Checking the rental table and the two stations ID's (1007, 3294)**
*on chcking the stations ID's , in this table we have two: start_stion_id and end_station_id.
*first I checked the two statios id (1007, 3294) for both start and end stations.(noticed that the total of 26 trips were end stastions id and all refered to the 3294 ID) 
*for confirmaion I checked if the 1007 station is indeed with no records.(resulted in confirmation of empty record)



### 4. Summary of Initial Observations & Data Integrity

Following an exhaustive first look at the schema and contents of the core tables, here are the foundational baselines and anomalies discovered:

* **Primary Key Integrity:** Verified that the primary keys across all relational tables contain zero duplicate records, establishing a reliable baseline for entity resolution.
* **Data Missingness (Null Analysis):** The transactional `rentals` table and the `customers` profile table are completely populated with no `NULL` values. The dimension table `station_info` contains a few isolated `NULL` cells that will require minor imputation downstream.
* **Structural Anomaly ("Lavaca & 6th"):** Identified a critical data redundancy where a single physical location name, *"Lavaca & 6th"*, maps to two distinct identification codes: `1007` and `3294`.
* **Deep-Dive Verification:** Cross-referencing these two keys against the transactional `rentals` logs revealed that station ID `1007` is officially categorized as **Closed** and has exactly **0 historical trip records** associated with it (neither as a source nor a destination).
* **Strategic Engineering Action:** Because ID `1007` has zero transactional footprint, it functions purely as a legacy "Ghost Station" row. Rather than over-engineering a complex lookup remap, we can safely drop/filter out ID `1007` from our master dimension catalog during data staging with zero data loss or metric distortion.

---

### 🔍 Investigative SQL Audits

The following exploratory queries were executed in BigQuery to isolate, verify, and resolve the "Ghost Station" edge case before committing to downstream data transformations:

```sql
-- ====================================================================
-- Step 1: Reviewing the duplicate name rows in the station dimension table
-- ====================================================================
SELECT 
  station_id,
  name,
  status,
  number_of_docks
FROM 
  `bqproj-488319.zen_city.station_info`
WHERE 
  station_id IN (1007, 3294);
-- Result: Revealed that Station '1007' is explicitly marked as "Closed".

-- ====================================================================
-- Step 2: Checking total transactional footprint for BOTH station IDs
-- ====================================================================
SELECT 
  COUNT(*) as total_trips
FROM 
  `bqproj-488319.zen_city.rentals`
WHERE 
  start_station_id IN (1007, 3294)
  OR end_station_id IN (1007, 3294);
-- Result: Found a total of 26 trips registered system-wide.

-- ====================================================================
-- Step 3: Isolating the closed ID (1007) to ensure zero active dependencies
-- ====================================================================
SELECT 
  COUNT(*) as total_ghost_trips
FROM 
  `bqproj-488319.zen_city.rentals`
WHERE 
  start_station_id = 1007 
  OR end_station_id = 1007;
-- Result: Exactly 0 trips registered to ID 1007. 
-- Conclusion: Safely confirmed as a non-utilized entity.
