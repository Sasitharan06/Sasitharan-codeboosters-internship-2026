# Day 2 — Database Fundamentals + SQL + Data Visualization
## Codeboosters Tech — Data Engineering + GenAI Internship

---

# 📘 Overview

Day 2 focuses on transforming raw CSV data into a structured SQLite database, performing SQL analysis, and visualizing insights using Matplotlib.

This project demonstrates the complete mini data engineering workflow:

```text
CSV Dataset → SQLite Database → SQL Queries → DataFrames → Charts
```

By the end of Day 2, you will understand:

- How databases work
- How SQL queries retrieve data
- How Python connects with databases
- How to visualize data using charts
- How ETL concepts begin in real projects

---

# 📂 Project Structure

```bash
Day_02_Database_SQL_Visualization/
│
├── Day_02_Database_SQL_Visualization.ipynb
├── student_performance.csv
├── college.db
├── README.md
└── charts/
    ├── avg_math_score.png
    └── math_distribution.png
```

---

# 🛠 Technologies Used

| Technology | Purpose |
|---|---|
| Python | Programming language |
| Pandas | Data analysis |
| SQLite | Lightweight database |
| SQL | Querying database |
| Matplotlib | Data visualization |
| Google Colab | Development environment |

---

# 📥 Import Required Libraries

```python
import sqlite3
import pandas as pd
import matplotlib.pyplot as plt
import warnings

warnings.filterwarnings("ignore")
print("Successfully imported libraries")
```

---

# 📊 Load Dataset

The dataset is loaded from a CSV file using Pandas.

```python
df = pd.read_csv('/content/student_performance.csv')
display(df.head(3))
```

## Explanation

- `pd.read_csv()` reads CSV files into a DataFrame.
- `head(3)` displays the first 3 rows.
- DataFrame is the main data structure used in Pandas.

---

# 📌 Dataset Information

```python
print(f"Dataset loaded: {df.shape[0]} students, {df.shape[1]} columns")
```

### Example Output

```text
Dataset loaded: 100 students, 10 columns
```

---

# 🗄 Creating SQLite Database

```python
conn = sqlite3.connect('college.db')
cursor = conn.cursor()

print("Cursor created")
```

## Explanation

### sqlite3.connect()

Creates or connects to an SQLite database file.

```python
college.db
```

If the file does not exist, SQLite creates it automatically.

### cursor

The cursor object is used to execute SQL commands.

---

# 📥 Store CSV Data into Database

```python
df.to_sql(
    'students',
    conn,
    if_exists='replace',
    index=False
)
```

## Explanation

| Parameter | Meaning |
|---|---|
| `'students'` | Table name |
| `conn` | Database connection |
| `if_exists='replace'` | Replace table if already exists |
| `index=False` | Do not store DataFrame index |

---

# 🔍 Verify Data Inserted

```python
cursor.execute('SELECT COUNT(*) FROM students')
count = cursor.fetchone()[0]

print(f"Number of students in the database: {count}")
```

---

# 🧾 Display Table Structure

```python
cursor.execute("PRAGMA table_info(students)")
columns_info = cursor.fetchall()
```

## Explanation

`PRAGMA table_info()` returns:

- Column names
- Data types
- Primary key information

---

# 📌 Helper Function for SQL Queries

```python
def run_query(sql_query, description):

    print(description)

    try:
        result_df = pd.read_sql_query(sql_query, conn)
        return result_df

    except Exception as e:
        print(f"Error executing query: {e}")
        return None
```

## Why This Function Is Useful

Instead of writing:

```python
pd.read_sql_query(query, conn)
```

again and again, we create a reusable helper function.

### Benefits

- Cleaner code
- Better readability
- Easier debugging
- Reusable queries

---

# 🧠 SQL Queries Performed

---

## 1️⃣ Last 5 Entries

```sql
SELECT * FROM students
ORDER BY student_id DESC
LIMIT 5
```

### Explanation

| SQL Clause | Purpose |
|---|---|
| `ORDER BY student_id DESC` | Sort descending |
| `LIMIT 5` | Show only 5 rows |

---

## 2️⃣ Top 5 Math Scores

```sql
SELECT name, department, math_score
FROM students
ORDER BY math_score DESC
LIMIT 5
```

### Concepts Used

- SELECT
- ORDER BY
- LIMIT

---

## 3️⃣ Highest Programming Scores

```sql
SELECT name, department, programming_score
FROM students
ORDER BY programming_score DESC
LIMIT 5
```

---

## 4️⃣ Attendance Above 90%

```sql
SELECT name, department, attendance_percentage
FROM students
WHERE attendance_percentage > 90
AND department != 'Civil'
ORDER BY attendance_percentage DESC
```

### Concepts Used

| Clause | Purpose |
|---|---|
| WHERE | Filters rows |
| AND | Multiple conditions |
| != | Not equal |
| ORDER BY | Sorting |

---

# 🏢 Department Table Creation

```python
dept_data = {
    'dept_code': ['CS','EC','ME','CIV'],
    'dept_name': ['Computer Science','Electronics','Mechanical','Civil'],
    'hod_name': ['Dr. A','Dr. B','Dr. C','Dr. D'],
    'established': [1985,1999,2006,2008],
    'intake': [60,60,69,37]
}
```

---

# 🔗 Foreign Key Concept

A new column called `dept_code` is added to link:

```text
students table ↔ department table
```

This simulates real relational databases.

---

# 🔄 Mapping Department Codes

```python
department_to_code_map = dict(
    zip(dept_data['dept_name'],
        dept_data['dept_code'])
)

df['dept_code'] = df['department'].map(
    department_to_code_map
)
```

---

# 📚 INNER JOIN Query

```sql
SELECT s.name,
       s.math_score,
       d.dept_name,
       d.hod_name,
       d.established

FROM students AS s
INNER JOIN department AS d
ON s.dept_code = d.dept_code

ORDER BY s.math_score DESC
LIMIT 8
```

---

# 🧠 JOIN Explanation

JOIN combines data from multiple tables.

| Table | Contains |
|---|---|
| students | Student details |
| department | Department details |

---

# 📈 Data Visualization

---

## 1️⃣ Bar Chart — Average Math Score

```python
chart_sql = """
SELECT department,
ROUND(AVG(math_score),2) AS avg_math
FROM students
GROUP BY department
ORDER BY avg_math DESC
"""
```

### Chart Code

```python
fig, ax = plt.subplots(figsize=(10,6))

ax.bar(
    chart1_data['department'],
    chart1_data['avg_math']
)

ax.set_xlabel('Department')
ax.set_ylabel('Average Math Score')
ax.set_title('Average Math Score by Department')

plt.tight_layout()
plt.show()
```

### What This Chart Shows

- Average math performance
- Department comparison
- Highest performing department

---

## 2️⃣ Histogram — Math Score Distribution

```python
plt.hist(
    df['math_score'],
    bins=10,
    edgecolor='black'
)
```

### Histogram Explanation

Histogram helps identify:

- Score distribution
- Concentration of values
- Outliers
- Performance spread

---

# 🧠 Important SQL Concepts Learned

| Concept | Meaning |
|---|---|
| SELECT | Retrieve columns |
| WHERE | Filter rows |
| ORDER BY | Sorting |
| LIMIT | Restrict results |
| GROUP BY | Group records |
| AVG() | Average calculation |
| INNER JOIN | Combine tables |
| COUNT() | Count rows |

---

# ⚡ WHERE vs HAVING

| Feature | WHERE | HAVING |
|---|---|---|
| Filters | Rows | Groups |
| Applied | Before GROUP BY | After GROUP BY |
| Uses Aggregate? | ❌ No | ✅ Yes |

## Example

### WHERE

```sql
SELECT * FROM students
WHERE math_score > 80
```

### HAVING

```sql
SELECT department, AVG(math_score)
FROM students
GROUP BY department
HAVING AVG(math_score) > 80
```

---

# 🔄 SQL Execution Order

```text
FROM
→ WHERE
→ GROUP BY
→ HAVING
→ SELECT
→ ORDER BY
→ LIMIT
```

---

# 📌 Common Errors and Solutions

| Error | Cause | Solution |
|---|---|---|
| no such table | Table not created | Run `to_sql()` |
| no such column | Typo in column name | Check schema |
| closed connection | Database closed | Reconnect |
| blank chart | Empty DataFrame | Print DataFrame |
| labels overlap | tight_layout missing | Use `plt.tight_layout()` |

---

# 🧪 Practice Questions

## 1. Difference between Primary Key and Foreign Key?

- Primary Key uniquely identifies a row.
- Foreign Key links one table to another.

---

## 2. SQL for Average Programming Score of Female Students

```sql
SELECT AVG(programming_score)
FROM students
WHERE gender = 'Female'
```

---

## 3. Departments with Attendance Above 85%

```sql
SELECT department,
AVG(attendance_percentage)
FROM students
GROUP BY department
HAVING AVG(attendance_percentage) > 85
```

---

# 🎯 Learning Outcomes

After completing Day 2, you can:

✅ Create databases using SQLite  
✅ Store CSV data into SQL tables  
✅ Execute SQL queries from Python  
✅ Perform JOIN operations  
✅ Analyze data using SQL  
✅ Create professional charts  
✅ Build mini data engineering pipelines  

---

# 📌 Day 2 Completion Checklist

- [x] SQLite database created
- [x] CSV data inserted
- [x] SQL queries executed
- [x] JOIN operations completed
- [x] Charts created
- [x] Dashboard concepts learned
- [x] Notebook saved
- [x] GitHub upload completed

---

# 🚀 Day 3 Preview

## ETL Pipelines + Data Cleaning + Weather API

### Next Day Topics

- Real-time API collection
- Data cleaning
- Handling null values
- Removing duplicates
- Building ETL pipelines
- Live weather data processing

---

# 👨‍💻 Author

**Sasi Tharan**  
Engineering Student  
Data Engineering + GenAI Internship

---

# 📜 License

This project is for educational and learning purposes.

---

# ⭐ Final Summary

Day 2 introduced the foundation of modern data engineering:

```text
Structured Data + SQL + Visualization
```

This workflow is used in:

- Data Analytics
- Machine Learning
- Business Intelligence
- Backend Systems
- Cloud Data Pipelines
- AI Applications

Understanding these fundamentals is essential before moving into ETL pipelines, APIs, and large-scale data systems.

---