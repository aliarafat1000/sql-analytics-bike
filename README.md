# 🚀 Bike Sales: Data Warehouse and Analytics Project

Welcome to the **Data Warehouse and Analytics fro Bike Sales Project** repository!  
This project demonstrates a complete data warehousing and analytics solution, from designing a data warehouse to generating actionable insights through SQL-based analysis. It is designed as a portfolio project to showcase practical skills in data engineering, modeling, and analytics.

---

## 📖 Project Overview

This project includes the following components:

1. **Data Architecture**: Implementation of a modern data warehouse using the Medallion Architecture — Bronze, Silver, and Gold layers.
2. **ETL Pipelines**: Building Extract-Transform-Load (ETL) pipelines to move and refine raw data into business-ready formats.
3. **Data Modeling**: Creating a star schema with fact and dimension tables optimized for querying.
4. **Analytics & Reporting**: Using SQL to develop insights into customer behavior, product performance, and sales trends.

🎯 This project is suitable for those looking to demonstrate skills in:
- SQL Development
- Data Engineering
- ETL Design
- Data Modeling
- Business Intelligence & Analytics

---

## 🚧 Project Requirements

### 🔹 Data Warehouse Build (Data Engineering)

**Objective**:  
Construct a modern SQL-based data warehouse for unified sales data analysis.

**Specifications**:
- **Data Sources**: Two CSV-based sources simulating ERP and CRM systems.
- **Data Quality**: Address inconsistencies and ensure clean, structured data.
- **Data Integration**: Merge the sources into a single analytical model.
- **Scope**: Focus on the most recent dataset; no historization required.
- **Documentation**: Include clear model definitions to support analysts and business users.

---

### 🔹 Analytics & Reporting (Data Analysis)

**Objective**:  
Develop SQL queries to extract key insights from the data warehouse on:
- Customer segmentation and behavior
- Product-level sales performance
- Seasonal and regional sales trends

---

## 🏗️ Data Architecture

This project follows the **Medallion Architecture**, consisting of three data layers:

<img width="772" alt="data_architecture" src="https://github.com/user-attachments/assets/23887581-5fd7-4a18-8b99-6f771bd0aaed" />

1. **Bronze Layer**: Raw data ingested directly from CSV files into SQL Server.
2. **Silver Layer**: Cleansed and standardized data ready for transformation.
3. **Gold Layer**: Star-schema modeled data designed for analytics and reporting.



---

## 🧱 Bronze Layer: Raw Data Ingestion

The first step in our Medallion Architecture implementation is constructing the **Bronze Layer**, where raw data from ERP and CRM systems is ingested into SQL Server for initial staging.

### ✅ What We Did

* **Database & Schema Setup**:
  Created a dedicated `DataWarehouse` database and defined three schemas:

  * `bronze`: Raw, unprocessed data
  * `silver`: Cleansed and structured data
  * `gold`: Star schema for analytics and reporting

* **Table Creation**:
  Six tables were created under the `bronze` schema to reflect the original structure of CSV source files:

  * `crm_cust_info`
  * `crm_prd_info`
  * `crm_sales_details`
  * `erp_loc_a101`
  * `erp_cust_az12`
  * `erp_px_cat_g1v2`

* **Data Loading via Stored Procedure**:
  A custom stored procedure `bronze.load_bronze` was built to:

  * Truncate existing data to ensure freshness
  * Use `BULK INSERT` to load CSV files directly into the respective tables
  * Log processing times for performance tracking
  * Handle errors gracefully using `TRY...CATCH`

This forms the foundation for further transformations in the **Silver** and **Gold** layers.


### 📂 Key Scripts

* [💾 Database and Schema Setup](https://github.com/aliarafat1000/sql-analytics-bike/blob/main/scripts/init_database.sql)
* [📄 Bronze Tables DDL](https://github.com/aliarafat1000/sql-analytics-bike/blob/main/scripts/bronze/ddl_bronze.sql)
* [⚙️ Load Bronze Layer Procedure](https://github.com/aliarafat1000/sql-analytics-bike/blob/main/scripts/bronze/proc_load_bronze.sql)


---


## ⚙️ Silver Layer – Data Transformation & Standardization

The **Silver Layer** is responsible for transforming raw data from the Bronze Layer into a clean, structured, and standardized format that’s ready for business logic and analytics in the Gold Layer.

### 🔧 Objectives

* Clean inconsistent and invalid values
* Standardize formats across systems
* Derive meaningful columns for downstream use
* Ensure relational integrity for joining across datasets

### 🛠️ Key Activities

1. **Table Setup**:
   Created tables in the `silver` schema mirroring Bronze structure, with added metadata and derived columns.

2. **Data Cleaning & Profiling**:

   * Removed duplicate records using `RANK() OVER (PARTITION BY ...)`
   * Eliminated null or placeholder keys like `'NAS'` and empty strings
   * Checked for and removed out-of-range dates (e.g., >2050 or <1900)

3. **Standardization**:

   * Trimmed white spaces using `LTRIM(RTRIM())`
   * Converted abbreviations to readable values using `CASE WHEN`
   * Fixed inconsistent formats (e.g., country codes to full names, gender codes to labels)

4. **Derived Columns**:

   * Extracted segments of strings using `SUBSTRING()` (e.g., category ID)
   * Replaced special characters using `REPLACE()` to prepare join keys
   * Created end dates using:

     ```sql
     CAST(DATEADD(DAY, -1, LEAD(start_date, 1) OVER (PARTITION BY key ORDER BY start_date)) AS DATE)
     ```
   * Fixed pricing and sales logic using business rules:

     * `sales = quantity * price`
     * Handled missing or invalid values using `ISNULL()`, `NULLIF()`, `ABS()`

5. **Validation & Loading**:

   * Verified joins between tables using cleaned keys
   * Checked date logic (e.g., order date < shipping date)
   * Created a stored procedure to truncate and insert data into Silver tables
   * Wrapped logic in `TRY...CATCH` for robust error handling

---


### 📂 Key Scripts


* [📄 Silver Tables DDL](https://github.com/aliarafat1000/sql-analytics-bike/blob/main/scripts/bronze/ddl_bronze.sql)
* [⚙️ Load Silver Layer Procedure](https://github.com/aliarafat1000/sql-analytics-bike/blob/main/scripts/bronze/proc_load_bronze.sql)



