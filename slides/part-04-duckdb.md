---
layout: image-right
image: /images/part-04-duckdb.png
backgroundSize: contain
class: my-auto
---

# Part 4
## DuckDB: SQL Meets DataFrames

---

# What is DuckDB?

An **in-process SQL database** designed for fast analytical queries. Think "SQLite, but for data analysis."

<div class="grid grid-cols-2 gap-8 mt-4">

<div>

### How It Works

- Runs inside your Python process (no server)
- Columnar storage (reads only needed columns)
- Vectorized execution (operates on entire columns, not row-by-row like traditional databases)
- Queries files directly (CSV, Parquet, JSON) without reading into memory.

<div class="mt-4"></div>

### Why It Exists

Traditional databases are built for transactions (OLTP). DuckDB is built for **analytics** (OLAP) - aggregations, joins, and scans on large tables.

</div>

<div class="bg-purple-50 dark:bg-purple-900/30 p-4 rounded">

### Key Features

- **SQL interface** - familiar syntax
- **Fast** - optimized for analytics
- **Reads anything** - CSV, Parquet, JSON, pandas, polars
- **Connects anywhere** - PostgreSQL, MySQL, SQLite

```python
import duckdb

result = duckdb.sql("""
    SELECT product, SUM(sales)
    FROM 'sales_data.csv'
    GROUP BY product
""")
```

</div>

</div>

---

# DuckDB Basics

```python
import duckdb
con = duckdb.connect()                              # In-memory (default)
con = duckdb.connect("data/my_data.duckdb")         # Persistent database
```

<div class="grid grid-cols-2 gap-4 mt-3">

<div>

### Query Files Directly

```python
# CSV
result = con.execute("""
    SELECT * FROM read_csv_auto('data/sales.csv')
    WHERE price > 100
""").df()

# Parquet (supports globs)
result = con.execute("""
    SELECT * FROM 'data/*.parquet'
""").df()

# JSON
result = con.execute("""
    SELECT * FROM read_json_auto('data/records.json')
""").df()
```

</div>

<div>

### Query DataFrames

```python
import pandas as pd
import polars as pl

df_pandas = pd.read_csv("data/sales.csv")
df_polars = pl.read_csv("data/sales.csv")

# Query pandas with SQL
result = con.execute("""
    SELECT product, AVG(price)
    FROM df_pandas
    GROUP BY product
""").df()

# Query polars, return as polars
result = con.execute("""
    SELECT * FROM df_polars WHERE qty > 10
""").pl()
```

</div>

</div>

---

# SQL for Data Analysis

```python
import duckdb
con = duckdb.connect()

result = con.execute("""
    WITH monthly_sales AS (
        SELECT date_trunc('month', date) as month, product,
               SUM(quantity * price) as revenue, COUNT(*) as transactions
        FROM read_csv_auto('data/sales_data.csv')
        GROUP BY date_trunc('month', date), product
    )
    SELECT *,
           revenue / SUM(revenue) OVER (PARTITION BY month) as pct_of_month
    FROM monthly_sales
    ORDER BY month, revenue DESC
""").df()
```

<div class="grid grid-cols-2 gap-4 mt-4 text-sm">

<div class="bg-blue-50 dark:bg-blue-900/30 p-1 rounded">

**Standard SQL features work:**
- CTEs - `WITH` clauses for multi-step queries
- Window functions - `OVER`, `PARTITION BY`
- Date functions - `date_trunc`, `extract`

</div>

<div class="bg-purple-50 dark:bg-purple-900/30 p-1 rounded">

**Query external databases directly:**
```sql
ATTACH 'postgres://user:pass@host/db' AS pg;
SELECT * FROM pg.sales WHERE revenue > 1000;
```

</div>

</div>

---

# When to Use DuckDB

<div class="grid grid-cols-3 gap-4 mt-6">

<div class="bg-green-50 dark:bg-green-900/30 p-4 rounded">

### Perfect For

- **Ad-hoc analysis** on files
- **SQL-first** workflows
- **Large file** processing
- **Joining multiple** data sources
- **Complex aggregations**
- Quick data exploration

</div>

<div class="bg-blue-50 dark:bg-blue-900/30 p-4 rounded">

### Use With Polars When

- Need **programmatic** transformations
- Building **data pipelines**
- **Streaming** processing
- Need **Python ecosystem**
- Complex **business logic**

</div>

<div class="bg-orange-50 dark:bg-orange-900/30 p-4 rounded">

### Avoid When

- Need **OLTP** (transactions)
- High **concurrency** writes
- Need a **server database**
- Real-time **streaming**
- Simple operations on small data

</div>

</div>

<div class="mt-6 bg-yellow-50 dark:bg-yellow-900/30 p-4 rounded text-center">

**Rule of thumb:** If you're working with files larger than your RAM, try DuckDB first

</div>

---

# DuckDB + Polars Integration

<div class="text-sm opacity-70 -mt-2 mb-2">Best of both worlds: SQL queries with Polars performance</div>

```python
import polars as pl
import duckdb

df = pl.read_parquet("data/large_data.parquet")  # Read with Polars
con = duckdb.connect()

# Use DuckDB for complex SQL on Polars DataFrame
result = con.execute("""
    SELECT store_id, date_trunc('week', timestamp) as week,
           SUM(quantity * price) as weekly_revenue,
           COUNT(DISTINCT customer_id) as unique_customers
    FROM df
    GROUP BY store_id, date_trunc('week', timestamp)
    HAVING SUM(quantity * price) > 10000
""").pl()  # Return as Polars DataFrame

# Continue processing in Polars
final = result.with_columns(
    (pl.col("weekly_revenue") / pl.col("unique_customers")).alias("rev_per_customer")
)
```

**Workflow:** Load with Polars -> Query with DuckDB SQL -> Process result in Polars

---

# Performance Comparison: Polars vs DuckDB

<div class="text-sm opacity-70">Task: Calculate revenue and sort by store (3 columns, 1M/10M/100M rows)</div>

<div class="grid grid-cols-2 gap-3 mt-2">

<div class="bg-orange-50 dark:bg-orange-900/30 p-2 rounded text-xs">

**Polars**
```python
df_pl = pl.DataFrame(data)
rev = pl.col("quantity") * pl.col("price")
result = df_pl.with_columns(
    rev.alias("revenue")
).sort(["store_id", "revenue"],
       descending=[False, True])
```

</div>

<div class="bg-purple-50 dark:bg-purple-900/30 p-2 rounded text-xs">

**DuckDB**
```python
result = con.execute("""
    SELECT *, quantity * price as revenue
    FROM data
    ORDER BY store_id ASC, revenue DESC
""").pl()
```

</div>

</div>

<div class="mt-2 text-xs">

| Rows | Polars | DuckDB | Notes |
|------|--------|--------|-------|
| 1M | 0.03s | 0.45s | Polars 16x faster |
| 10M | 0.37s | 0.64s | Polars 1.7x faster |
| 100M | 4.9s | 9.3s | Polars 1.9x faster |

</div>

<div class="mt-2 text-center text-sm">

Choose based on: **SQL familiarity** -> DuckDB | **Programmatic pipelines** -> Polars

</div>

---

# Summary: Choosing the Right Tool

<div class="mt-2">

| Tool | Best For | Syntax | Speed |
|------|----------|--------|-------|
| **pathlib** | File/directory operations | Object-oriented | N/A |
| **Pandas** | Small data, ecosystem compatibility | DataFrame API | Baseline |
| **Polars** | Large data, pipelines, performance | Expression API | 9-16x faster |
| **DuckDB** | SQL analysis, file queries, joins | SQL | 5-10x faster |

</div>

<div class="grid grid-cols-2 gap-6 mt-6">

<div class="bg-blue-50 dark:bg-blue-900/30 p-1 rounded">

### Recommended Stack

1. **pathlib** for file operations
2. **Polars** as primary DataFrame library
3. **DuckDB** for complex SQL queries
4. **Pandas** only when needed for compatibility

</div>

<div class="bg-green-50 dark:bg-green-900/30 p-1 rounded">

### Quick Decision Guide

- Small data + quick analysis -> **Pandas**
- Large data + pipelines -> **Polars**
- Data larger than RAM + multiple files -> **DuckDB**
- Mix and match as needed!

</div>

</div>

---
layout: center
---

# Hands-on Exercise

<div class="text-left max-w-2xl">

### Analyze Sales Data with All Three Libraries

Using `data/sales_data.csv`:

1. **Pandas**: Read data, calculate total revenue per product
2. **Polars**: Same analysis - compare the syntax
3. **DuckDB**: Find the best sales day using SQL

```python
# Start here
from pathlib import Path
import pandas as pd
import polars as pl
import duckdb

data_path = Path("data") / "sales_data.csv"

# Your code...
```

**Bonus:** Time each approach and compare performance!

</div>

---

# Resources

<div class="grid grid-cols-2 gap-4">
<div>

**Documentation**
- [pathlib](https://docs.python.org/3/library/pathlib.html) - docs.python.org
- [Pandas](https://pandas.pydata.org/docs/) - pandas.pydata.org
- [Polars](https://docs.pola.rs/) - docs.pola.rs
- [DuckDB](https://duckdb.org/docs/) - duckdb.org

**Tutorials & Guides**
- [Polars User Guide](https://docs.pola.rs/user-guide/)
- [Pandas to Polars Migration](https://docs.pola.rs/user-guide/migration/pandas/)
- [Modern Polars](https://kevinheavey.github.io/modern-polars/) (cookbook)

</div>
<div>

**Cheat Sheets**
- [Pandas Cheat Sheet](https://pandas.pydata.org/Pandas_Cheat_Sheet.pdf)
- [Polars Cheat Sheet](https://franzdiebold.github.io/polars-cheat-sheet/Polars_cheat_sheet.pdf) (community)

**This Tutorial**
- [Slides](https://mcda-data-processing.pages.dev) - mcda-data-processing.pages.dev
- [Code](https://github.com/smuotoe/mcda-data-processing-tutorial) - github.com/smuotoe

</div>
</div>
