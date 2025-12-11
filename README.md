# 📦 Real-Time E-Commerce Order Intelligence and Sales Monitoring (Azure-Based)

> **Live Analytics Project** using Azure Event Hub, Databricks (PySpark), Azure Blob Storage, and Power BI – built entirely using U.S.-based simulated e-commerce data.

---

## 🔧 Tech Stack
- **Data Ingestion**:

![Azure Event Hub](https://img.shields.io/badge/Azure%20Event%20Hub-0078D4?logo=microsoftazure&logoColor=white&style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white&style=for-the-badge)

- **Processing**:
  
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?logo=apachespark&logoColor=white&style=for-the-badge)
![Azure Databricks](https://img.shields.io/badge/Azure%20Databricks-FF3621?logo=databricks&logoColor=white&style=for-the-badge)
- **Storage**:

![Delta Lake](https://img.shields.io/badge/Delta%20Lake-0A74DA?logo=data%20bricks&logoColor=white&style=for-the-badge)
![Azure Blob Storage](https://img.shields.io/badge/Azure%20Blob%20Storage-0089D6?logo=microsoftazure&logoColor=white&style=for-the-badge)

- **Visualization**:

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?logo=powerbi&logoColor=black&style=for-the-badge)

- **Version Control**:

![Git](https://img.shields.io/badge/Git-F05032?logo=git&logoColor=white&style=for-the-badge)

---

## 🚀 Project Overview

This real-time data engineering project simulates and processes live e-commerce order data to deliver insightful dashboards for U.S. retail operations. We built an end-to-end pipeline using Azure-native tools and open-source technologies, following a **Bronze → Silver → Gold** architecture.

<img src="https://drive.google.com/uc?export=view&id=19VB0cYZYxyCZZHMVDokRX96UflPi1XzK" width="800" alt="Dashboard 1">

## 🎥 Project Walkthrough Video

I’ve created a detailed YouTube video explaining the project and walking through the entire execution step-by-step.  
[![Watch the video](https://img.shields.io/badge/YouTube-Video-red?logo=youtube&logoColor=white)](https://youtu.be/H9cK7ELbjLg?si=r0okbdiiWJCO5R8v)


## 🧪 Project Structure

```
real-time-ecommerce-insights-azure/
│
├── Databrick Notebooks/    # Bronze, Silver, Gold notebooks
│   └── 01_stream_orders_to_bronze.py
│   └── 02_cleaned_values_silver.py
│   └── 03_aggregated_to_gold.py
├── Power BI/               # Dashboard file (.pbix)
│   └── ecommerce_sales_dashboard.pbix
├── Simulator/              # Python script to simulate U.S. orders
│   └── generate_orders.py
├── CICD/                   # Git commit history & tracking
│   └── git_commands.sh
└── README.md               # 📄 This file
```
---

## 🧱 Step 1: Data Simulation + Event Streaming

📦 Created a Python simulator to continuously send fake U.S.-based e-commerce orders (TX, NY, CA...) to **Azure Event Hub**. [generate_orders.py](simulator/generate_orders.py)

- Events included `order_id`, `state`, `city`, `product`, `quantity`, `price`, `timestamp`
- Used Python libraries: `uuid`, `random`, `json`, `time`, `azure-eventhub`
- Streamed events every second using a for-loop and `send_batch()` method

🪛 Commands:
- Used `pip install` to install Azure SDKs
- Set up Event Hub with namespace `e-commerce-namespace` and hub name `e-commerce-orders`
- Git-tracked simulator files with: `git add Simulator/`

---

## 🔁 Step 2: Real-Time Processing in Databricks

We created a **3-layer Delta Lake architecture** using **Structured Streaming** in PySpark.

### 🍂 Bronze Layer [01_stream_orders_to_bronze.py](databricks_notebooks/01_stream_orders_to_bronze.py)
- Read streaming data directly from Event Hub
- Parsed and flattened JSON events
- Wrote raw data to Azure Blob in Delta format

### 🔧 Silver Layer [02_cleaned_values_silver.py](databricks_notebooks/02_cleaned_values_silver.py)
- Cleaned, filtered, and type-casted Bronze data
- Removed nulls, bad records
- Stored refined data as a Delta table

### 🪙 Gold Layer [03_aggregated_to_gold.py](databricks_notebooks/03_aggregated_to_gold.py)
- Performed aggregation with **1-minute windows** (not hourly)
- Grouped by `state`, `product`, and `timestamp`
- Stored final result table (`gold`) to Blob in Delta

🧾 Notebooks:
- Named `1_bronze_stream.py`, `2_silver_clean.py`, `3_gold_agg.py`
- Git tracked using:  
```bash
cd databricks_notebooks
git add 01-streamorders-to-bronze.py
cd ..
git commit -m "Bronze layer streaming for U.S. order data"

cd databricks_notebooks
git add 02_clean_to_silver.py
cd ..
git commit -m "Cleaned and enriched Bronze data into Silver layer for U.S. cities"

cd databricks_notebooks
git add 03_aggregate_to_gold.py
cd ..
git commit -m "Aggregated Silver data to Gold layer for U.S. states (minute totals)"
```

---

## 🗃️ Step 3: Azure Blob Storage – Delta Tables

Used Azure Blob Storage to persist all three layers in **Delta format**, organized as:

```
abfss://ecommerce@ecommercestoragejk.dfs.core.windows.net/bronze/
abfss://ecommerce@ecommercestoragejk.dfs.core.windows.net/silver/  
abfss://ecommerce@ecommercestoragejk.dfs.core.windows.net/gold/
```

- Delta files were automatically updated via streaming write
- All Power BI visuals connected to the **Gold** layer

---

## 📊 Step 4: Power BI Dashboard (U.S. States)

Connected Power BI to **Databricks SQL Endpoint** to read the Gold Delta Table.

### 🔌 Connection Steps:
- Created SQL Warehouse in Databricks
- Connected Power BI using Azure Databricks connector (with token)
- Loaded `gold` table for visuals

### 📈 Visuals Created:
| Visual Type | Description |
|-------------|-------------|
| 📍 Map Chart | State-wise total sales |
| 📉 Line Chart | Sales trend over time (1-min window) |
| 🍩 Donut Chart 1 | Total sales per state |
| 🍩 Donut Chart 2 | Total items sold per state |
| 📄 Raw Data Table | Sorted by highest sales at top |


<img width="1418" height="792" alt="Screenshot 2025-08-07 135137" src="https://github.com/user-attachments/assets/1a9187b6-8ff9-4b77-81d2-43060aa981d1" />


🧾 Tracked Power BI with:
```bash
cd Power\ BI
touch README.md
echo "# Power BI Dashboard for Real-Time E-Commerce Insights (U.S.)" > README.md
git add Power\ BI
git commit -m "Power BI dashboard with U.S. state-wise forecasting from Gold layer"
```

---

## 🔗 Git Version Control

Used Git CLI for all version tracking:
- `git init`, `git remote add origin ...`
- `git add`, `git commit`, `git push` at every phase
- Maintained clear folder-by-folder commits

---

## ✅ Final Outcome

This project delivers a **fully functional real-time analytics pipeline** powered by Azure and Delta Lake. The dashboard provides operations teams with **minute-level insights** into U.S. e-commerce orders by product, state, and time.

---

## 📁 Folder Summary

```
📂 Simulator         → Sends real-time JSON orders to Event Hub
📂 Databrick Notebooks → Bronze → Silver → Gold PySpark logic
📂 Power BI          → Final .pbix with charts and map
📂 CICD              → Git commit history & tracking
```

---

**Author**: *Jaya Chandra Kadiveti*  
**GitHub**: [username](https://github.com/Jay61616)
**Contact**: [datawithjay1@gmail.com](mailto:datawithjay1@gmail.com)
---
