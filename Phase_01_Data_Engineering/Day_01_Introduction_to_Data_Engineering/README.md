# Day 1 — Student Reference Guide
## Introduction to Data Engineering + GitHub
### Codeboosters Tech — Data Engineering + GenAI Internship Program

---

# 📘 Quick Reference Card

| Topic | Key Point |
|-------|-----------|
| Data Engineering | Building systems to collect, store, clean, and deliver data |
| Structured Data | Rows and columns — CSV, Excel, SQL |
| Semi-Structured | Key-value pairs — JSON, XML |
| Unstructured | No fixed format — images, videos, audio, PDFs |
| Pandas | Python library for working with tabular data |
| DataFrame | In-memory table with rows and columns |
| GitHub | Cloud platform for version control and code portfolio |
| Commit | Saved snapshot of code with a description |

---

# 🐼 Essential Pandas Commands — Day 1

```python
import pandas as pd                    # Import Pandas library

df = pd.read_csv('filename.csv')       # Load a CSV file

df.head()                              # Show first 5 rows
df.head(10)                            # Show first 10 rows
df.tail()                              # Show last 5 rows

df.shape                               # (number_of_rows, number_of_columns)
df.dtypes                              # Data type of each column
df.describe()                          # Statistical summary
df.isnull().sum()                      # Count missing values per column
df.columns.tolist()                    # List all column names

df['column_name']                      # Select one column

# Boolean Filtering
df[df['column'] == 'value']            # Filter rows equal to value
df[df['column'] > 80]                  # Filter rows greater than 80
df[df['column'] < 75]                  # Filter rows less than 75

# Groupby and Aggregation
df.groupby('column')['other'].mean()   # Average per group
df['column'].value_counts()            # Count each unique value

# Sorting
df.sort_values(by='column', ascending=False)   # Sort highest first
df.sort_values(by='column', ascending=True)    # Sort lowest first

# New Column
df['new_col'] = df['col1'] + df['col2']  # Add columns together

# Save DataFrame
df.to_csv('output.csv', index=False)   # Save as CSV