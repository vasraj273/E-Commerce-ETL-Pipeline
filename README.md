# 🛒 End-to-End E-Commerce ETL Pipeline & Business Analytics

**Project Focus:** Automated ETL Architecture, Multi-Metric Data Aggregation, and Business Leak Identification using Python and SQL.

## 📌 Business Context
For modern retail companies, manually querying massive CSV files is inefficient and prone to memory crashes. Furthermore, leadership doesn't just need to know what is making money—they need automated reporting to identify exactly which products are bleeding company resources. 

This project builds a localized ETL (Extract, Transform, Load) pipeline that completely automates this process. It ingests raw e-commerce data into a robust SQLite database, executes multi-level SQL aggregations to generate a comprehensive executive summary, and proactively identifies the worst-performing inventory items ("business leaks").

## 🛠️ Tools & Technologies Used
* **Environment:** Linux / Jupyter Notebook
* **Language:** Python 3
* **Database:** SQLite (Relational Database)
* **Libraries:** `pandas` (Data Manipulation), `sqlite3` (Database connection), `matplotlib` (Visualization)
* **Key Techniques:** ETL Pipeline Engineering, Advanced SQL Aggregation (`SUM`, `AVG`, `COUNT`), and automated business reporting.

---

## 💡 Key Analytical Insights
1. **The Executive Summary:** The pipeline successfully extracted the core performance metrics, proving that the **Technology** category is the undisputed revenue champion ($827,455.87) with an Average Order Value of $456.40.
2. **Identifying Business Leaks:** By isolating the bottom performers, the pipeline identified that the **Fasteners** sub-category is actively draining resources, generating only $3,001.96 across all historical data. This signals a need for immediate inventory reduction.

---

## 🚀 The Pipeline Architecture (Code Execution)

### 1. The "Load" Phase
Automated the ingestion of a raw 9,800-row CSV file into a newly generated relational SQL database to protect system memory.
```python
import pandas as pd
import sqlite3
import matplotlib.pyplot as plt

df = pd.read_csv('superstore_sales.csv') 
connection = sqlite3.connect('superstore_analytics.db')
df.to_sql('sales_data', connection, if_exists='replace', index=False)
```

### 2. The "Extract" Phase
Executed two targeted SQL queries directly inside Python. The first generates a multi-metric executive summary. The second isolates the lowest-performing assets.
```python
# Query A: The Complete Executive Summary
query_summary = """
SELECT 
    "Category", 
    COUNT("Order ID") AS Total_Orders,
    ROUND(SUM("Sales"), 2) AS Total_Revenue,
    ROUND(AVG("Sales"), 2) AS Average_Order_Value
FROM sales_data
GROUP BY "Category"
ORDER BY Total_Revenue DESC;
"""
executive_summary_df = pd.read_sql_query(query_summary, connection)

# Query B: Identifying the Business Leaks (Worst Performers)
query_leaks = """
SELECT 
    "Sub-Category", 
    ROUND(SUM("Sales"), 2) AS Total_Sales
FROM sales_data
GROUP BY "Sub-Category"
ORDER BY Total_Sales ASC
LIMIT 5;
"""
business_leaks_df = pd.read_sql_query(query_leaks, connection)
```

### 3. The "Transform & Visualize" Phase
Passed the extracted SQL DataFrames into Matplotlib to generate an executive-ready visualization and formatted text report.
```python
executive_summary_df.plot(
    kind='bar', 
    x='Category', 
    y='Total_Revenue', 
    color=['#1f77b4', '#ff7f0e', '#2ca02c'], 
    legend=False, 
    figsize=(8, 5)
)
plt.title('Total Revenue by Product Category', fontsize=14, fontweight='bold')
plt.xlabel('Product Category', fontsize=12)
plt.ylabel('Total Revenue ($)', fontsize=12)
plt.xticks(rotation=0) 
plt.show()

connection.close()
```
