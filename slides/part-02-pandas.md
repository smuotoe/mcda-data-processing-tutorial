---
layout: section
---

# Part 2
## Pandas: Fundamentals

---

# What is Pandas?

A Python library for **data manipulation and analysis**, providing fast, flexible data structures for working with tabular (spreadsheet-like) data.

<div class="grid grid-cols-2 gap-8 mt-4">

<div>

### The De Facto Standard

- Created in 2008 by Wes McKinney
- Built on NumPy
- ~95% of Python data analysis uses pandas
- Massive ecosystem and community
- Excellent documentation

<div class="mt-4"></div>

### Core Data Structures

- **Series**: 1D labeled array
- **DataFrame**: 2D labeled table

</div>

<div class="bg-gray-50 dark:bg-gray-800 p-4 rounded text-sm">

```python
import pandas as pd

# Series: 1D labeled array
prices = pd.Series([1.20, 0.50, 0.80])
# 0    1.20
# 1    0.50
# 2    0.80

# DataFrame: 2D labeled table
df = pd.DataFrame({
    "product": ["Apple", "Banana", "Orange"],
    "price": [1.20, 0.50, 0.80],
})
#   product  price
# 0   Apple   1.20
# 1  Banana   0.50
# 2  Orange   0.80
```

</div>

</div>

---

# Reading Data

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### Common File Formats

```python
import pandas as pd
from pathlib import Path

data = Path("data")

# CSV files
df = pd.read_csv(data / "sales.csv")

# Excel files
df = pd.read_excel(data / "report.xlsx")

# JSON files
df = pd.read_json(data / "records.json")

# Parquet files (columnar format)
df = pd.read_parquet(data / "large_data.parquet")
```

</div>

<div>

### Quick Data Inspection

```python
# First/last rows
df.head()       # First 5 rows
df.tail(10)     # Last 10 rows

# Shape and info
df.shape        # (rows, columns)
df.info()       # Column types, memory
df.describe()   # Statistics

# Column names and types
df.columns      # Column names
df.dtypes       # Data types

# Sample data
df.sample(5)    # Random 5 rows
```

</div>

</div>

---

# Selecting Data

<div class="grid grid-cols-2 gap-4 mt-2">

<div>

### Column Selection

```python
# Single column (returns Series)
df["product"]
df.product          # Dot notation

# Multiple columns (returns DataFrame)
df[["product", "price"]]
```

### Row Selection

```python
# By index position
df.iloc[0]          # First row
df.iloc[0:5]        # First 5 rows
df.iloc[[0, 2, 4]]  # Specific rows

# By label
df.loc[0]           # Row with index 0
df.loc[0:5]         # Rows 0 through 5
```

</div>

<div>

### Filtering

```python
# Boolean filtering
df[df["price"] > 10]

# Multiple conditions
df[(df["price"] > 10) & (df["quantity"] > 50)]

# Using query (cleaner syntax)
df.query("price > 10 and quantity > 50")

# Check if value in list
df[df["product"].isin(["Apple", "Banana"])]
```

### Combined Selection

```python
# Rows and columns together
df.loc[0:5, ["product", "price"]]
df.iloc[0:5, 0:2]
```

</div>

</div>

---

# Data Manipulation

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### Creating New Columns

```python
# Direct assignment
df["revenue"] = df["quantity"] * df["price"]

# Using assign (returns new DataFrame)
df = df.assign(
    revenue=df["quantity"] * df["price"],
    tax=df["price"] * 0.1
)

# Conditional values
df["size"] = df["quantity"].apply(
    lambda x: "large" if x > 100 else "small"
)

# Using np.where
import numpy as np
df["size"] = np.where(
    df["quantity"] > 100, "large", "small"
)
```

</div>

<div>

### Modifying Data

```python
# Rename columns
df = df.rename(columns={
    "old_name": "new_name"
})

# Drop columns
df = df.drop(columns=["unwanted_col"])

# Fill missing values
df["price"] = df["price"].fillna(0)

# Replace values
df["status"] = df["status"].replace({
    "Y": "Yes", "N": "No"
})

# Sort
df = df.sort_values("price", ascending=False)
```

</div>

</div>

---

# Aggregations & Grouping

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### Basic Aggregations

```python
# Single column
df["price"].sum()
df["price"].mean()
df["price"].max()
df["quantity"].nunique()  # Unique count

# Multiple aggregations
df["price"].agg(["sum", "mean", "std"])

# Across DataFrame
df[["price", "quantity"]].sum()
```

</div>

<div>

### GroupBy Operations

```python
# Group and aggregate
df.groupby("product")["revenue"].sum()

# Multiple aggregations
df.groupby("product").agg({
    "revenue": ["sum", "mean"],
    "quantity": "sum",
    "customer_id": "nunique"
})

# Group by multiple columns
df.groupby(["product", "region"]).sum()

# Transform (keeps original shape)
df["pct_of_total"] = (
    df["revenue"] /
    df.groupby("product")["revenue"].transform("sum")
)
```

</div>

</div>

---

# Writing Data

```python
from pathlib import Path

output = Path("output")
output.mkdir(exist_ok=True)

df.to_csv(output / "results.csv", index=False)              # CSV - most common
df.to_excel(output / "report.xlsx", sheet_name="Sales")     # Excel
df.to_parquet(output / "data.parquet")                      # Parquet - compressed, fast
df.to_json(output / "data.json", orient="records")          # JSON
```

<div class="grid grid-cols-2 gap-4 mt-4 text-sm">

<div class="bg-green-50 dark:bg-green-900/30 p-3 rounded">

**CSV** - Universal format, human-readable, opens in Excel/Google Sheets. Use when sharing data with others.

**Parquet** - Binary columnar format, compressed, fast for analytics. Use for large datasets and data pipelines.

</div>

<div class="bg-blue-50 dark:bg-blue-900/30 p-3 rounded">

**Excel** - Native spreadsheet format with multiple sheets. Use when stakeholders need to open in Excel directly.

**JSON** - Structured text format. Use for web APIs or when data has nested structures.

</div>

</div>
