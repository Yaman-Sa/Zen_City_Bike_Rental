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
| `zen_city.station_info` | `station_id` (INTEGER) | 102  | 102  | 0  |
| `zen_city.rentals` | `trip_id` (INTEGER) | 16,585  | 16,585  | 0  |
| `zen_city.customers` | `customer_id` (STRING) | 586  | 586  | 0  |

### **Baseline SQL Verification Snippet**
```sql
-- Validating uniqueness of primary keys across tables
SELECT COUNT(1) AS raw_rows, COUNT(DISTINCT station_id) AS unique_keys FROM `bqproj-488319.zen_city.station_info`;
SELECT COUNT(1) AS raw_rows, COUNT(DISTINCT trip_id) AS unique_keys FROM `bqproj-488319.zen_city.rentals`;
SELECT COUNT(1) AS raw_rows, COUNT(DISTINCT customer_id) AS unique_keys FROM `bqproj-488319.zen_city.customers`;
```
---

# 2. Data Completeness & Missingness (Null Analysis)
Before proceeding to granular attribute validation, a comprehensive null-value scan was executed across all columns in the three core datasets. Identifying fields with missing data early prevents unexpected runtime failures, biased aggregations, and distortion during Q2 business predictions.

### **Completeness Profiles**
* **`zen_city.rentals` & `zen_city.customers`**: Both datasets are 100% complete. Every transactional log and customer profile contains fully populated dimensions with zero operational logging gaps or missing structural IDs.
* **`zen_city.station_info`**: The master dimension table is highly reliable but contains isolated `NULL` cells in non-critical attributes (such as specific address fields or metadata tracking dates). These sparse gaps will require minor imputation downstream but do not compromise primary key integrity.

### **Data Completeness & Null-Audit SQL Snippet**
```sql
-- nulls in tables counter 
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
-- Nulls Exist

```



# **3. Attribute-Level Duplicate & Name Consistency Check**
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

# 4. Deep-Dive Anomaly: "Lavaca & 6th" Station Identifier Split ( in rental table name: "6th/Lavaca")
* **The Mismatch:** The station name **"Lavaca & 6th"** is mapped to two completely distinct identification codes: `1007` and `3294`.
* **The Operational Cause:** An audit of the operational `status` column revealed that station `1007` is explicitly marked as **Closed**, while station `3294` is marked as **Active**. This confirms that they represent the exact same physical location over different operational windows (likely due to a hardware swap, kiosk upgrade, or system re-indexing).
* **The Analytical Impact:** If left uncorrected, any historical rental logs tied to the older ID `1007` would be isolated from the current station's metrics, resulting in an artificial underreporting of long-term traffic patterns at this high-density intersection.
* **The Data Preservation Strategy:** To maintain data continuity and accurately map network volume, we will implement conditional remapping logic during the wrangling phase. All transactional logs under ID `1007` will be combined into ID `3294`. Although the absolute volume of trips originating from ID `1007` is relatively small, this step ensures perfect alignment for our downstream spatial aggregation models.

/*
=============================================================================
Script: eda_isolate_lavaca_station.sql
Phase: 01 - Exploratory Data Analysis & Raw Table Audit
Objective: Isolate the "6th/Lavaca" anomaly by matching by name or explicit 
           IDs to confirm the operational status conflict (Closed vs. Active).
=============================================================================

*/
```
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
  TRIM(name) = '6th/Lavaca' 
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

SELECT*
FROM 
  `bqproj-488319.zen_city.rentals`
WHERE 

 end_station_id IN (1007)
 or start_station_id IN (1007)
 ;

-- empty record

```  
---
** Same Table!** 
* in both queries I got the exact same table, suggesting that the count of trips for each distinct ID is **1** in the station_info tale.
* filtiring the 1007 ID would be filtering 0 trips from the record with 16585 trips in total, which doesnt affect the total trips count.
* however , for join operation for our final CTE I decided to filter and use left join to prevent data loss.


--- 

**Checking the rental table and the two stations ID's (1007, 3294)**
*on chcking the stations ID's , in this table we have two: start_stion_id and end_station_id.
*first I checked the two statios id (1007, 3294) for both start and end stations.(noticed that the total of 26 trips were end stastions id and all refered to the 3294 ID) 
*for confirmaion I checked if the 1007 station is indeed with no records.(resulted in confirmation of empty record)



# 4. Summary of Initial Observations & Data Integrity

Following an exhaustive first look at the schema and contents of the core tables, here are the foundational baselines and anomalies discovered:

* **Primary Key Integrity:** Verified that the primary keys across all relational tables contain zero duplicate records, establishing a reliable baseline for entity resolution.
* **Data Missingness (Null Analysis):** The transactional `rentals` table and the `customers` profile table are completely clean with no `NULL` values. The dimension table `station_info` contains a few isolated `NULL` cells that will require minor imputation downstream.
* **Structural Anomaly ("Lavaca & 6th"):** Identified a critical data redundancy where a single physical location name, *"Lavaca & 6th"* or *"6th/Lavaca"*, maps to two distinct identification codes: `1007` and `3294`.
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
