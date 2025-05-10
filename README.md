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


* [📄 Silver Tables DDL](https://github.com/aliarafat1000/sql-analytics-bike/blob/main/scripts/silver/ddl_silver.sql)
* [⚙️ Load Silver Layer Procedure](https://github.com/aliarafat1000/sql-analytics-bike/blob/main/scripts/silver/proc_load_silver.sql)



---

## 🟡 Gold Layer – Star Schema for Analytics

The **Gold Layer** represents the final business-ready schema in the data warehouse. It follows the **Star Schema** model, with fully enriched and transformed dimension and fact views that serve as the foundation for analytics and reporting.

---

### 🎯 Objectives

* Deliver a **clean**, **enriched**, and **query-optimized** data model
* Combine Silver Layer tables to form **dimensional views** (Dimensions & Facts)
* Apply **business logic**, filtering, and **data enrichment**
* Make data easy to consume for downstream tools like Power BI, Tableau, or Excel

---

### 🧱 Components

#### 📘 `gold.dim_customers`

A dimension view combining CRM and ERP customer data:

* Generates a **surrogate key** using `ROW_NUMBER()`
* Resolves customer gender from primary (CRM) and fallback (ERP) sources
* Joins with `erp_loc_a101` to enrich location data
* Sample Transformations:

  * Gender fallback using `CASE` and `COALESCE`
  * Composite joins across CRM and ERP customer keys

#### 📘 `gold.dim_products`

A product dimension view enriched with category metadata:

* Filters out **inactive products** (`prd_end_dt IS NULL`)
* Joins with `erp_px_cat_g1v2` to bring in category and maintenance info
* Creates a surrogate `product_key` ordered by start date

#### 📘 `gold.fact_sales`

The central fact view capturing all sales transactions:

* Joins sales data with `dim_products` and `dim_customers`
* Provides transactional measures like:

  * `sales_amount`
  * `quantity`
  * `price`
* Includes sales lifecycle dates (order, shipping, due)

---

### 🧰 Key SQL Features Used

* `ROW_NUMBER()` for surrogate key generation
* `CASE`, `COALESCE()` for fallback logic
* `LEFT JOIN` to enrich dimensions with additional data
* Filtering historical data using `WHERE` clauses
* Standard view creation with error-safe `IF OBJECT_ID(...) DROP VIEW` pattern

---

### ⚙️ Usage

* These views are **query-ready** for BI dashboards and reporting
* No additional transformation is needed downstream
* Connect directly to reporting tools or query via SQL for ad hoc insights

---

* [📄 Gold Tables DDL](https://github.com/aliarafat1000/sql-analytics-bike/blob/main/scripts/gold/ddl_gold.sql)


Here's a GitHub-style `README.md` section for your **Exploratory Data Analysis (EDA)** work. You can directly **append this** to the previous README that covered data layers (Bronze → Silver → \[Gold]).

---

## 🔍 Exploratory Data Analysis (EDA)

After building the cleaned and enriched dimensional and fact tables, the next step was to perform **exploratory data analysis** using SQL. This helps understand the structure, behavior, and performance of key business entities such as customers, products, and sales.

### 📌 Objectives

* Understand the data's **scope**, **volume**, and **granularity**
* Identify key **metrics**, **distributions**, and **groupings**
* Derive early **insights** to inform advanced analytics and dashboarding
* Practice **dimension vs. measure classification** and business logic reasoning

---

### 🛠️ Skills & Tools Used

| Skill/Tool     | Description                                                           |
| -------------- | --------------------------------------------------------------------- |
| SQL            | Used for querying, aggregating, and joining dimension and fact tables |
| Data Profiling | Column-wise examination using `DISTINCT`, `COUNT`, `MIN`, `MAX`, etc. |
| Aggregation    | Business metrics calculated using `SUM`, `AVG`, `COUNT(DISTINCT)`     |
| Joins          | Combined fact table with dimensions for deeper contextual insights    |
| Grouping       | Used `GROUP BY` and filters to segment data by various dimensions     |
| Ranking        | Identified top performers using `TOP`, `ORDER BY`, and `ROW_NUMBER()` |
| Data Reasoning | Interpreted results (e.g., why count vs. count distinct matters)      |

---

### 🗂️ Key Analyses Performed

* 📋 **Schema Exploration**
  Verified structure and availability of tables and columns

* 🧩 **Dimension Analysis**
  Explored countries, categories, subcategories, and demographics

* 🕒 **Date Ranges**
  Checked timespan of sales and customer birthdates to understand historical coverage

* 📈 **Measure Aggregation**
  Reported total sales, order counts, quantities, and average prices

* 🌍 **Magnitude Analysis**
  Grouped sales and quantity by geography, gender, and product category

* 🏆 **Ranking & Performance**
  Identified best/worst-performing products, categories, and top customers

---

### 📊 Sample Insights

* All customers placed at least one order
* Top 5 products generated a significant share of total revenue
* Customers are evenly distributed across genders and regions
* Some subcategories dominate in both sales volume and revenue

---

* [📄 EDA Script](https://github.com/aliarafat1000/sql-analytics-bike/tree/main/scripts/Exploratory%20Data%20Analysis%20(EDA))





---

# Data Analysis and Reporting in SQL

## Overview

This repository contains a series of SQL queries designed to perform data analysis and generate reports for various business metrics. The analysis covers key aspects such as customer segmentation, product performance, sales contributions, and customer behaviors. The purpose is to provide actionable insights and identify trends for business decision-making.

### Key Areas of Analysis:

1. **Data Segmentation**
2. **Part-to-Whole Analysis**
3. **Customer Reports**
4. **Product Reports**

## Skills Used

* **SQL Aggregate Functions**: `SUM()`, `AVG()`, `COUNT()`
* **Window Functions**: `SUM() OVER()`, `ROW_NUMBER()`, `RANK()`
* **Conditional Logic**: `CASE`
* **Date Functions**: `DATEDIFF()`, `GETDATE()`
* **Grouping and Filtering**: `GROUP BY`, `HAVING`
* **Subqueries and CTEs**: `WITH` clauses, Common Table Expressions (CTEs)
* **Joins**: `INNER JOIN`, `LEFT JOIN`

## Analysis and Findings

### 1. **Data Segmentation Analysis**

**Purpose**: Segment products into cost ranges and customers based on their spending behavior.

**Findings**:

* **Product Segmentation**:

  * Products were segmented by cost into categories such as **Below 100**, **100-500**, **500-1000**, and **Above 1000**.
  * The most popular segment by product count was the **Below 100** cost range.

* **Customer Segmentation**:

  * Customers were categorized into **VIP**, **Regular**, and **New** based on their spending behavior and lifespan.
  * The **VIP** segment had a smaller but highly valuable group of customers who spent more than €5000 and had at least 12 months of purchase history.

**SQL Functions Used**: `CASE`, `GROUP BY`, `COUNT()`, `SUM()`, `DATEDIFF()`, `LEFT JOIN`

### 2. **Part-to-Whole Analysis**

**Purpose**: Analyze the contribution of each product category to overall sales.

**Findings**:

* **Sales Contribution by Category**:

  * **Bikes** were the highest contributing category, representing **96.46%** of total sales.
  * **Accessories** and **Clothing** had much smaller shares, contributing **2.39%** and **1.16%**, respectively.

**SQL Functions Used**: `SUM()`, `WINDOW FUNCTION` (`SUM() OVER()`), `ROUND()`, `CASE`, `GROUP BY`

### 3. **Customer Report**

**Purpose**: Generate a comprehensive customer report with key metrics and behavior segmentation.

**Findings**:

* The report segments customers by **age groups** (Under 20, 20-29, 30-39, etc.) and **customer segments** (VIP, Regular, New).
* Key metrics calculated included:

  * **Total orders**, **Total sales**, **Total quantity purchased**
  * **Average order value** (AOV), **Average monthly spend**
  * **Recency** (months since last order)

**SQL Functions Used**: `CASE`, `COUNT()`, `SUM()`, `DATEDIFF()`, `AVG()`, `GROUP BY`, `LEFT JOIN`

### 4. **Product Report**

**Purpose**: Analyze product performance and categorize products into **High-Performer**, **Mid-Range**, and **Low-Performer** based on sales data.

**Findings**:

* Products were classified into three segments based on total sales:

  * **High-Performer**: Products with sales greater than **€50,000**
  * **Mid-Range**: Products with sales between **€10,000** and **€50,000**
  * **Low-Performer**: Products with sales below **€10,000**
* Key metrics included:

  * **Total orders**, **Total sales**, **Total quantity sold**
  * **Average selling price**, **Average order revenue**, **Average monthly revenue**
  * **Recency** (months since last sale)

**SQL Functions Used**: `CASE`, `COUNT()`, `SUM()`, `AVG()`, `DATEDIFF()`, `LEFT JOIN`, `GROUP BY`, `ROUND()`

## Queries Overview

### 1. Data Segmentation Query

```sql
-- Segmentation of products into cost ranges and customer segmentation by spending
WITH product_segments AS (...), customer_spending AS (...) 
SELECT ... FROM product_segments GROUP BY cost_range;
```

### 2. Part-to-Whole Analysis Query

```sql
-- Analyzing the contribution of product categories to overall sales
WITH category_sales AS (...) 
SELECT ... FROM category_sales ORDER BY total_sales DESC;
```

### 3. Customer Report Query

```sql
-- Consolidating customer metrics such as total orders, total sales, and recency
IF OBJECT_ID('gold.report_customers', 'V') IS NOT NULL DROP VIEW gold.report_customers;
CREATE VIEW gold.report_customers AS ...
```

### 4. Product Report Query

```sql
-- Generating a product performance report, categorizing products by total sales
IF OBJECT_ID('gold.report_products', 'V') IS NOT NULL DROP VIEW gold.report_products;
CREATE VIEW gold.report_products AS ...
```

* [📄 Analytics Script](https://github.com/aliarafat1000/sql-analytics-bike/tree/main/scripts/analytics%20and%20eda%20scripts)


---


