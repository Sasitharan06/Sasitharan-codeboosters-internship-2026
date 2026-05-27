# Day 3 — ETL Pipelines + Pandas Data Cleaning + APIs
## Codeboosters Tech — Data Engineering + GenAI Internship

---

# 📘 Overview

Day 3 introduces one of the most important concepts in Data Engineering:

# 🔄 ETL Pipeline

```text
EXTRACT → TRANSFORM → LOAD
```

In this project, we:

- Extract messy sales data from CSV files
- Transform and clean the dataset using Pandas
- Handle missing values and duplicates
- Standardize incorrect data
- Parse and fix dates
- Create calculated columns
- Load cleaned data into a new CSV
- Fetch real-time job data using APIs

This simulates how real companies process raw business data before analytics or machine learning.

---

# 📂 Project Structure

```bash
Day_03_ETL_Pipeline/
│
├── day-3_DEandGenAI.ipynb
├── messy_sales_data.csv
├── clean_sales_data.csv
├── README.md
└── charts/
    ├── revenue_chart.png
    ├── category_chart.png
    └── weather_dashboard.png
```

---

# 🛠 Technologies Used

| Technology | Purpose |
|---|---|
| Python | Programming Language |
| Pandas | Data Cleaning & Analysis |
| Requests | API Calls |
| JSON | API Response Handling |
| Matplotlib | Visualization |
| Google Colab | Development Environment |

---

# 🔄 Understanding ETL

## 1️⃣ Extract

Collect raw data from:

- CSV files
- APIs
- Databases
- Cloud storage

Example:

```python
raw_df = pd.read_csv('messy_sales_data.csv')
```

---

## 2️⃣ Transform

Clean and process data:

- Fix null values
- Remove duplicates
- Convert data types
- Standardize text
- Parse dates
- Create new columns

---

## 3️⃣ Load

Save processed data into:

- CSV
- Database
- Data warehouse
- Cloud storage

Example:

```python
df.to_csv('clean_sales_data.csv', index=False)
```

---

# 🌊 Real World ETL Analogy

```text
River Water → Water Treatment Plant → Clean Water Tank → Homes
```

| ETL Stage | Real Meaning |
|---|---|
| Extract | Collect river water |
| Transform | Clean/filter water |
| Load | Store in tank |

---

# 📥 Import Libraries

```python
import pandas as pd
import requests
import json
import matplotlib.pyplot as plt

import matplotlib.patches as mpatches
from datetime import datetime
import warnings

warnings.filterwarnings('ignore')
```

---

# 📌 Why These Libraries?

| Library | Purpose |
|---|---|
| pandas | Data analysis |
| requests | API requests |
| json | Handle JSON data |
| matplotlib | Visualization |
| datetime | Timestamp handling |

---

# 📊 Load Raw Dataset

```python
raw_df = pd.read_csv('messy_sales_data.csv')
```

---

# 🔍 Explore Dataset

```python
print(raw_df.shape)
print(raw_df.columns.tolist())
raw_df.head()
```

---

# 📌 Dataset Analysis

We check:

- Number of rows
- Number of columns
- Column names
- Sample records

This is the first step of data profiling.

---

# 🚨 Detect Data Quality Problems

```python
print(raw_df.isnull().sum())
print(raw_df.duplicated().sum())
print(raw_df.dtypes)
```

---

# 🧠 What Problems Are We Checking?

| Check | Purpose |
|---|---|
| isnull() | Find missing values |
| duplicated() | Find duplicate rows |
| dtypes | Check data types |

---

# 🛡 Create Working Copy

```python
df = raw_df.copy()
```

---

# ❓ Why Use `.copy()`?

Never modify raw data directly.

Benefits:

- Raw data stays safe
- Easy rollback
- Better debugging

---

# 🔧 Handling Missing Values

---

## Fill Missing Customer Names

```python
df['customer_name'].fillna(
    'Unknown Customer',
    inplace=True
)
```

### Why?

Text columns are usually filled with labels.

---

## Fill Missing Quantity Using Median

```python
median_qty = df['quantity'].median()

df['quantity'].fillna(
    median_qty,
    inplace=True
)
```

---

# 🧠 Why Median Instead of Mean?

Median handles outliers better.

Example:

```text
1, 2, 3, 1000
```

| Metric | Value |
|---|---|
| Mean | 251.5 |
| Median | 2.5 |

Median is more realistic.

---

## Fill Missing Categories

```python
df['category'].fillna(
    'Uncategorized',
    inplace=True
)
```

---

# 🧹 Remove Duplicate Rows

```python
df.drop_duplicates(inplace=True)
```

---

# 🔍 View Duplicate Rows

```python
duplicate_mask = df.duplicated(keep=False)

print(df[duplicate_mask])
```

---

# 📌 Why Remove Duplicates?

Duplicates can:

- Inflate revenue
- Distort analytics
- Create reporting errors

---

# 📅 Date Parsing

```python
df['order_date'] = pd.to_datetime(
    df['order_date'],
    dayfirst=False,
    errors='coerce'
)
```

---

# 🧠 What Does `errors='coerce'` Do?

If Pandas cannot parse a date:

```text
invalid-date
```

It converts it into:

```text
NaT (Not a Time)
```

instead of crashing the program.

---

# 📆 Extract Date Features

```python
df['year'] = df['order_date'].dt.year
df['month'] = df['order_date'].dt.month
df['month_name'] = df['order_date'].dt.strftime('%B')
df['day_name'] = df['order_date'].dt.strftime('%A')
```

---

# 📌 Why Extract Date Features?

Useful for:

- Monthly reports
- Sales trends
- Seasonal analysis
- Dashboard filtering

---

# ✨ Standardize Customer Names

```python
df['customer_name'] = (
    df['customer_name']
    .str.strip()
    .str.title()
)
```

---

# 🧠 What Happens Here?

| Method | Purpose |
|---|---|
| strip() | Remove extra spaces |
| title() | Convert to Title Case |

---

# 🔧 Fix Incorrect Categories

```python
wrong_mask = (
    (df['product'] == 'keyboard') &
    (df['category'] == 'Electronics')
)

df.loc[wrong_mask, 'category'] = 'Accessories'
```

---

# 🧠 Boolean Masking

Boolean masks help target specific rows.

```python
df.loc[condition, column] = new_value
```

Very powerful for data cleaning.

---

# 🔢 Convert Data Types

```python
df['quantity'] = pd.to_numeric(
    df['quantity'],
    errors='coerce'
).astype(int)
```

---

# 📌 Why Convert Types?

Sometimes numbers are stored as text:

```text
"25"
```

We convert them into real numeric types for calculations.

---

# 💰 Create Revenue Column

```python
df['revenue'] = (
    df['quantity'] *
    df['unit_price']
)
```

---

# 📊 Why Create Revenue?

Derived columns are common in ETL pipelines.

Example business metrics:

- Revenue
- Profit
- Discount
- Tax
- Total sales

---

# 💾 Save Clean Dataset

```python
df.to_csv(
    'clean_sales_data.csv',
    index=False
)
```

---

# 🌐 API Integration

Day 3 also introduces APIs.

---

# ❓ What Is an API?

API = Application Programming Interface

APIs allow applications to communicate.

Example:

```text
Your Program ↔ Weather Server
```

---

# 🔑 SerpAPI Setup

```python
SERP_API_KEY = 'your_key_here'
SERP_URL = 'https://serpapi.com/search.json'
```

---

# 📡 Fetch Job Listings

```python
response = requests.get(
    SERP_URL,
    params=params,
    timeout=15
)
```

---

# 🧠 HTTP Request Flow

```text
Python Program
      ↓
API Request
      ↓
Server Processes Request
      ↓
JSON Response Returned
```

---

# 📦 Parse JSON Response

```python
data = response.json()
```

---

# 📌 JSON Structure

API responses are usually JSON:

```json
{
  "title": "Data Engineer",
  "company": "Google"
}
```

Python converts JSON into dictionaries.

---

# 📋 Extract Job Details

```python
all_jobs.append({
    'title': job.get('title'),
    'company': job.get('company_name'),
    'location': job.get('location')
})
```

---

# 🧠 Why Use `.get()`?

Safer than direct indexing.

Example:

```python
job.get('title', 'Unknown')
```

If key is missing:

```text
Unknown
```

instead of crashing.

---

# 🌐 HTTP Status Codes

| Code | Meaning |
|---|---|
| 200 | Success |
| 401 | Invalid API key |
| 404 | Resource not found |
| 429 | Too many requests |
| 500 | Server error |

---

# 📈 Data Cleaning Workflow Summary

```text
Raw CSV
   ↓
Detect Issues
   ↓
Handle Missing Values
   ↓
Remove Duplicates
   ↓
Fix Dates
   ↓
Fix Text
   ↓
Convert Types
   ↓
Create Features
   ↓
Save Clean Data
```

---

# 🧠 Important Pandas Functions Learned

| Function | Purpose |
|---|---|
| read_csv() | Load CSV |
| copy() | Create safe copy |
| isnull() | Detect nulls |
| fillna() | Fill missing values |
| duplicated() | Detect duplicates |
| drop_duplicates() | Remove duplicates |
| to_datetime() | Parse dates |
| to_numeric() | Convert types |
| loc[] | Modify rows |
| to_csv() | Save CSV |

---

# ⚠ Common Errors and Fixes

| Error | Cause | Fix |
|---|---|---|
| KeyError | Missing column | Check column names |
| ConnectionError | No internet | Check connection |
| ValueError | Invalid conversion | Use `errors='coerce'` |
| Duplicate rows remain | Wrong subset | Review duplicate logic |
| API 401 | Invalid API key | Check API key |

---

# 🎯 Learning Outcomes

After completing Day 3, you can:

✅ Build ETL pipelines  
✅ Clean messy datasets  
✅ Handle missing values  
✅ Remove duplicates  
✅ Parse dates correctly  
✅ Standardize text data  
✅ Create calculated columns  
✅ Call external APIs  
✅ Parse JSON responses  
✅ Save processed datasets  

---

# 📌 Day 3 Completion Checklist

- [x] All notebook cells executed
- [x] Missing values fixed
- [x] Duplicate rows removed
- [x] Dates parsed successfully
- [x] Revenue column created
- [x] API data fetched
- [x] Clean CSV saved
- [x] ETL workflow completed

---

# 🚀 Day 4 Preview

# Big Data + Apache Spark + Modern Data Architecture

Next Topics:

- Why Pandas fails on huge datasets
- Distributed computing
- Apache Spark
- PySpark basics
- Medallion Architecture
- Bronze → Silver → Gold pipeline

---

# 👨‍💻 Author

**Sasi Tharan**  
Engineering Student  
Data Engineering + GenAI Internship

---

# 📜 License

This project is for educational purposes only.

---

# ⭐ Final Summary

Day 3 introduced the heart of modern data engineering:

```text
ETL + Data Cleaning + APIs
```

Every real-world data engineer works with:

- Messy data
- Missing values
- APIs
- Automation pipelines
- Data transformation

These are foundational skills for:

- Data Engineering
- Data Analytics
- Machine Learning
- AI Systems
- Cloud Data Platforms

---