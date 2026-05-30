# DAY 4 – BIG DATA, PYSPARK & MODERN DATA ARCHITECTURE

### Codeboosters Tech – Data Engineering + GenAI Internship

---

# 1. Introduction

In Days 1–3, we processed datasets ranging from 30 to 5000 rows using Pandas. As data grows into millions or billions of records, Pandas becomes insufficient. To handle large-scale data efficiently, industries use Apache Spark.

Today we learn:

* Big Data Fundamentals
* Apache Spark
* PySpark Data Processing
* Parquet Files
* Medallion Architecture
* Data Lakes, Warehouses, and Lakehouses

---

# 2. The 5 Vs of Big Data

Big Data is commonly described using five characteristics.

## Volume

Refers to the amount of data generated.

Example:

* Flipkart processes millions of orders during sale events.

## Velocity

Refers to the speed at which data is generated.

Example:

* Ola receives GPS updates every second from thousands of vehicles.

## Variety

Refers to different data formats.

Example:

* Zomato handles text, images, GPS data, and payment information.

## Veracity

Refers to data accuracy and trustworthiness.

Example:

* Paytm detecting fraudulent transactions.

## Value

Refers to useful insights obtained from data.

Example:

* IRCTC using search patterns for predictive pricing.

### When is Data Considered Big Data?

Data becomes Big Data when a single machine cannot process it efficiently within an acceptable time.

---

# 3. Pandas vs PySpark

| Feature   | Pandas              | PySpark             |
| --------- | ------------------- | ------------------- |
| Data Size | Small datasets      | Very large datasets |
| Storage   | RAM of one machine  | Multiple machines   |
| Speed     | Fast for small data | Fast for large data |
| Execution | Eager Execution     | Lazy Execution      |
| Best Use  | Data Analysis       | Big Data Processing |

### Rule of Thumb

Use Pandas when data fits comfortably in memory.

Use PySpark when data becomes too large for a single machine.

---

# 4. PySpark Setup

## Install PySpark

```python
!pip install pyspark --quiet
```

If Java-related errors occur:

```python
!apt-get install -y default-jdk
```

## Import Required Libraries

```python
from pyspark.sql import SparkSession
from pyspark.sql import functions as F
from pyspark.sql.functions import col, year, month, to_date
```

## Create Spark Session

```python
spark = SparkSession.builder \
    .appName("MyApp") \
    .getOrCreate()
```

Check Spark Version:

```python
print(spark.version)
```

---

# 5. Loading Data into PySpark

```python
df = spark.read \
    .option("header", "true") \
    .option("inferSchema", "true") \
    .csv("large_sales_data.csv")
```

### Inspect Data

```python
df.count()
df.printSchema()
df.show(5)
print(df.columns)
```

---

# 6. Transformations and Actions

## Transformations (Lazy Operations)

These operations do not execute immediately.

Examples:

```python
df.select("col1", "col2")
df.filter(col("revenue") > 50000)
df.withColumn("new_col", expression)
df.groupBy("category")
df.orderBy("revenue")
df.dropDuplicates()
df.dropna()
```

## Actions

Actions trigger execution.

Examples:

```python
df.show()
df.count()
df.first()
df.collect()
df.toPandas()
df.write.parquet()
```

### Best Practice

Perform multiple transformations and trigger execution using a single action.

---

# 7. Important PySpark Operations

## Select Columns

```python
df.select("product", "revenue", "city").show()
```

## Filter Records

```python
df.filter(col("revenue") > 50000).show()
```

Multiple Conditions:

```python
df.filter(
    (col("category") == "Electronics") &
    (col("revenue") > 40000)
).show()
```

## Group By and Aggregation

```python
df.groupBy("category") \
  .agg(
      F.sum("revenue").alias("total_rev"),
      F.count("order_id").alias("num_orders"),
      F.avg("revenue").alias("avg_rev")
  ) \
  .show()
```

---

# 8. Creating New Columns

```python
df = df.withColumn(
    "revenue_category",
    F.when(col("revenue") > 40000, "High")
     .when(col("revenue") > 10000, "Medium")
     .otherwise("Low")
)
```

---

# 9. Date Handling in PySpark

Convert String to Date:

```python
df = df.withColumn(
    "order_date",
    to_date(col("order_date"), "yyyy-MM-dd")
)
```

Extract Year and Month:

```python
df = df.withColumn(
    "order_year",
    year(col("order_date"))
)

df = df.withColumn(
    "order_month",
    month(col("order_date"))
)
```

---

# 10. Parquet Files

## Save as Parquet

```python
df.write.mode("overwrite").parquet("output.parquet")
```

## Read Parquet

```python
df2 = spark.read.parquet("output.parquet")
```

### Benefits of Parquet

* Faster query performance
* Compressed storage
* Schema stored automatically
* Ideal for analytics

---

# 11. Aggregate Functions

| Function        | Purpose        |
| --------------- | -------------- |
| sum()           | Total          |
| count()         | Row count      |
| avg()           | Average        |
| min()           | Minimum value  |
| max()           | Maximum value  |
| countDistinct() | Unique count   |
| lit()           | Constant value |

Examples:

```python
F.sum("revenue")
F.avg("revenue")
F.count("order_id")
F.max("revenue")
```

---

# 12. CSV vs Parquet

| Feature        | CSV        | Parquet      |
| -------------- | ---------- | ------------ |
| Format         | Row Based  | Column Based |
| Compression    | No         | Yes          |
| Query Speed    | Slow       | Fast         |
| Schema Storage | No         | Yes          |
| Human Readable | Yes        | No           |
| Best For       | Small Data | Big Data     |

---

# 13. Medallion Architecture

Medallion Architecture organizes data into three layers.

## Bronze Layer

Raw data directly from source systems.

Characteristics:

* No modifications
* Source of truth
* Append-only

Example:

```text
sales_bronze.parquet
```

---

## Silver Layer

Cleaned and validated data.

Characteristics:

* Duplicate removal
* Null handling
* Correct data types

Example:

```text
sales_silver.parquet
```

---

## Gold Layer

Business-ready aggregated data.

Characteristics:

* Dashboard ready
* Reporting ready
* Fast analytical queries

Examples:

```text
gold_region_revenue.parquet
gold_product_summary.parquet
gold_monthly_trend.parquet
```

---

# 14. Data Storage Architecture

## Data Warehouse

Examples:

* Snowflake
* Amazon Redshift

Features:

* Structured data only
* Fast analytics
* Higher cost

---

## Data Lake

Examples:

* AWS S3
* Azure Data Lake Storage

Features:

* Any data format
* Lower cost
* Flexible storage

---

## Lakehouse

Examples:

* Databricks
* Apache Iceberg

Features:

* Combines Data Lake and Warehouse advantages
* Supports analytics and machine learning

---

# 15. Common Errors and Solutions

| Error                        | Cause                  | Solution          |
| ---------------------------- | ---------------------- | ----------------- |
| ModuleNotFoundError: pyspark | Package missing        | Install PySpark   |
| JAVA_HOME not set            | Java missing           | Install JDK       |
| Path does not exist          | File not uploaded      | Upload CSV        |
| Multiple SparkContexts       | Multiple sessions      | Use getOrCreate() |
| Out of Memory                | collect() on huge data | Use show()        |
| Column not found             | Wrong column name      | Check schema      |
| sum(revenue) column name     | Alias missing          | Use alias()       |

---

# 16. Best Practices

1. Use PySpark for large datasets.
2. Prefer Parquet over CSV for analytics.
3. Avoid collect() on huge datasets.
4. Use transformations first and actions later.
5. Follow Bronze → Silver → Gold architecture.
6. Stop Spark session after completion.

```python
spark.stop()
```

---

# Conclusion

Apache Spark and PySpark enable processing of massive datasets efficiently across multiple machines. Combined with Parquet storage and Medallion Architecture, they form the foundation of modern Data Engineering pipelines used in organizations worldwide.
