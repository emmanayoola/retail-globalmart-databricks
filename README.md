# 🛒 GlobalMart Retail Intelligence Pipeline
 
> An end-to-end data engineering project simulating a modern enterprise retail analytics stack — built with Databricks, PySpark, Delta Lake (Medallion Architecture), and Power BI.
 
---
 
## 📋 Project Overview
 
**Client:** GlobalMart Inc. (Fictitious Retail Giant)  
**Role:** Junior Data Engineer  
**Engagement Lead:** Chris Gambill  
 
GlobalMart currently compiles sales spreadsheets manually at the end of every month. This 30-day latency prevents the supply chain team from reacting to shipping delays and the marketing team from identifying high-value customers in real time.
 
This project builds an **end-to-end data pipeline** that ingests raw sales data, cleans and standardises it, and produces a Gold-layer dimensional model powering a Power BI dashboard — with **zero manual effort**.
 
---
 
## 🏗️ Technical Architecture
 
```
CSV Source (Kaggle Superstore)
        │
        ▼
┌──────────────┐
│  BRONZE LAYER │  Raw ingestion → Delta Tables (with ingest metadata)
└──────────────┘
        │
        ▼
┌──────────────┐
│  SILVER LAYER │  Cleaned, deduplicated, validated, business-friendly schema
└──────────────┘
        │
        ▼
┌──────────────┐
│   GOLD LAYER  │  Star Schema — Fact & Dimension tables for reporting
└──────────────┘
        │
        ▼
┌──────────────┐
│   POWER BI    │  Dashboard → Profitability, Logistics, Customer Value KPIs
└──────────────┘
```
 
| Layer | Tool / Engine |
|---|---|
| Ingestion | Databricks Free Edition + PySpark |
| Processing | Apache Spark (PySpark) |
| Orchestration | Databricks Jobs (Scheduled Notebook Execution) |
| Storage | Delta Lake (Medallion Architecture) |
| Visualisation | Power BI Desktop (Import Mode via Partner Connect) |
 
---
 
## 📦 Dataset
 
- **Source:** [Superstore Sales Dataset — Kaggle](https://www.kaggle.com/)
- **File:** `Sample - Superstore.csv`
---
 
## 🚀 Getting Started
 
### Prerequisites
 
Ensure you have the following set up before beginning:
 
- [ ] **Databricks Free Edition** — [Sign Up](https://www.databricks.com/try-databricks)
- [ ] **Microsoft Power BI Desktop** — [Download](https://powerbi.microsoft.com/desktop) *(Windows only — see note below)*
- [ ] **Kaggle Account** — [Sign Up](https://www.kaggle.com) to download the dataset
- [ ] **Git / GitHub** — New to Git? Start here:
  - [GitHub "Hello World" Guide](https://docs.github.com/en/get-started/quickstart/hello-world)
  - [Git & GitHub for Beginners (Video)](https://www.youtube.com/watch?v=RGOj5yH7evk)
> **Mac Users:** Power BI Desktop is Windows only. Please reach out in the cohort Discord channel for alternative options.
 
---
 
## 🛠️ Implementation Plan
 
### Phase 1 — Environment Setup & Bronze Layer
**Estimated Effort: 3 Hours**
 
- Spin up Databricks Free Edition
- Create `bronze`, `silver`, and `gold` catalogs
- Create a `superstore` schema inside the `bronze` catalog
- Create a `raw_superstore` volume under `bronze.superstore`
- Upload `Sample - Superstore.csv` to the Volume
- Write a PySpark notebook to read raw CSVs and save as Delta Tables in the Bronze catalog
**✅ Success Criteria:** Bronze tables are queryable; row counts match source CSV.
 
> 💡 **Key Concept — Idempotency:** Your pipeline should be safe to re-run without creating duplicate records. New files should append new records while preserving existing ones.
 
---
 
### Phase 2 — Silver Layer (Data Quality)
**Estimated Effort: 4 Hours**
 
- Filter out orders with negative quantities (returns handling)
- Standardise date formats (String → Timestamp)
- Handle null values in `City` and `Postal Code` columns
- Rename all fields to business-friendly names (no abbreviations, no acronyms)
- Model data appropriately:
  - Dimension tables for Customers, Dates, and Products
  - Primary keys defined on all tables
- Write clean data to Silver Delta Tables
**✅ Success Criteria:** No duplicates; dates formatted correctly; nulls handled explicitly.
 
---
 
### Phase 3 — Gold Layer (Business Logic)
**Estimated Effort: 4 Hours**
 
Create a dimensional (Star Schema) model:
 
| Table | Type |
|---|---|
| `Fact_Sales` | Fact Table |
| `Dim_Customer` | Dimension |
| `Dim_Product` | Dimension |
| `Dim_Date` | Dimension |
 
- Calculate derived metrics (e.g., `Delivery_Days = Ship_Date - Order_Date`)
- Decide on persistence strategy: Table vs View vs Materialised View
**✅ Success Criteria:** Star Schema created; one row per transaction in the Fact table.
 
---
 
### Phase 4 — Power BI Dashboard
**Estimated Effort: 3 Hours**
 
- Connect Power BI to Gold tables/views via Databricks Partner Connect
- Build the Data Model (define relationships) inside Power BI
- Design and publish the dashboard
**✅ Success Criteria:** Dashboard refreshes without errors; KPIs match validated logic.
 
---
 
## 📊 Dashboard Requirements — Business ROI
 
The final dashboard must answer these three business questions:
 
| # | Business Question | Visual Type |
|---|---|---|
| 1 | **Profitability** — Which Product Sub-Categories have the lowest profit margins? | Bar / Table Chart |
| 2 | **Logistics Efficiency** — What is the Average Days to Ship per Region? | Bar Chart |
| 3 | **Customer Value** — Who are the Top 10 Customers by total spend YTD? | Ranked Table |
 
> 📌 **Required:** A **KPI Card** at the top of the dashboard showing **Total Profit** formatted as currency.
 
---
 
## 📁 Repository Structure
 
```
globalmart-retail-pipeline/
│
├── notebooks/
│   ├── 01_bronze_ingestion.ipynb
│   ├── 02_silver_cleaning.ipynb
│   └── 03_gold_modeling.ipynb
│
├── data/
│   └── Sample - Superstore.csv
│
├── powerbi/
│   └── GlobalMart_Dashboard.pbix
│
├── docs/
│   └── architecture_diagram.png
│
└── README.md
```
 
---
 
## 🏆 Milestone Tracker
 
| Phase | Deliverable | Est. Effort | Status |
|---|---|---|---|
| 01 | Ingestion Pipeline (Bronze) | 3 hrs | ⬜ Not Started |
| 02 | Data Cleaning (Silver) | 4 hrs | ⬜ Not Started |
| 03 | Dimensional Modelling (Gold) | 4 hrs | ⬜ Not Started |
| 04 | Power BI Dashboard | 3 hrs | ⬜ Not Started |
 
---
 
## 🤝 Support & Community
 
If you run into issues, don't understand any terminology, or have questions along the way — reach out in the **Gambill Datasphere Discord** cohort page.
 
---
 
## 📄 License
 
This project is for educational purposes as part of the Gambill Datasphere Data Engineering cohort.