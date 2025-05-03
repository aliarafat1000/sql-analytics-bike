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

