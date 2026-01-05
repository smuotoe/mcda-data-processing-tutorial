---
layout: image-right
image: /images/part-03-polars.png
backgroundSize: contain
backgroundPosition: center
class: flex flex-col justify-center
---

<div class="pr-8">
<div class="text-orange-400 uppercase tracking-widest text-sm font-semibold mb-2">Part 3</div>
<h1 class="!text-5xl font-bold !leading-tight">Polars</h1>
<h2 class="!text-3xl font-light text-gray-300 mt-2">The Modern Alternative</h2>
<div class="mt-4 text-gray-400 text-lg">Lightning-fast DataFrames in Rust</div>
</div>

---

# What is Polars?

A **DataFrame library** written in Rust, designed as a faster and more memory-efficient alternative to pandas.

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### The Problem with Pandas

- **Single-threaded** - Can't use multiple CPU cores
- **High memory usage** - Loads everything into RAM
- **GIL-bound** - Limited by Python's interpreter lock
- **Inconsistent API** - Many ways to do the same thing

</div>

<div>

### How Polars Solves It

- **Multi-threaded** - Uses all cores by default
- **Lazy evaluation** - Builds a plan first, then optimizes before running
- **Rust-powered** - Runs outside Python, bypasses GIL
- **Consistent API** - One clear way to do things

</div>

</div>

<div class="grid grid-cols-3 gap-4 mt-6 text-sm">

<div class="bg-orange-50 dark:bg-orange-900/30 p-3 rounded text-center">

**10-100x faster** than pandas on large datasets

</div>

<div class="bg-blue-50 dark:bg-blue-900/30 p-3 rounded text-center">

**Lazy evaluation** optimizes queries before execution

</div>

<div class="bg-green-50 dark:bg-green-900/30 p-3 rounded text-center">

**Handles larger-than-RAM** datasets via streaming

</div>

</div>

---

# Polars vs Pandas: Side by Side

<div class="grid grid-cols-2 gap-4 mt-4">

<div class="bg-gray-50 dark:bg-gray-800 p-3 rounded text-sm">

### Pandas

```python
import pandas as pd
from pathlib import Path

df = pd.read_csv(Path("data") / "sales.csv")

# Calculate revenue and filter
df["revenue"] = df["quantity"] * df["price"]
result = df[df["revenue"] > 1000]

# Group and aggregate (named aggregation)
summary = df.groupby("product").agg(
    total_revenue=("revenue", "sum"),
    avg_revenue=("revenue", "mean"),
    total_qty=("quantity", "sum")
)
```

</div>

<div class="bg-orange-50 dark:bg-orange-900/30 p-3 rounded text-sm">

### Polars

```python
import polars as pl
from pathlib import Path

df = pl.read_csv(Path("data") / "sales.csv")

# Calculate revenue and filter
revenue = pl.col("quantity") * pl.col("price")
result = df.with_columns(
    revenue.alias("revenue")
).filter(pl.col("revenue") > 1000)

# Group and aggregate
summary = df.group_by("product").agg(
    pl.col("revenue").sum().alias("total_revenue"),
    pl.col("revenue").mean().alias("avg_revenue"),
    pl.col("quantity").sum().alias("total_qty")
)
```

</div>

</div>

<div class="mt-4 text-sm text-center opacity-70">

Polars syntax is more verbose but explicit - you always know what's happening

</div>

---

# Expressions vs Contexts

Expressions describe *what* to do. Unlike pandas (which runs each step immediately), Polars collects expressions and optimizes them before execution.

<div class="grid grid-cols-2 gap-8 mt-6">

<div>

### Expressions (the "what")

```python
pl.col("price")           # reference column
pl.col("price") * 1.1     # computation
pl.col("x").sum()         # aggregation
pl.lit("USD")             # literal value
```

Expressions are composable:
```python
pl.col("name").str.to_lowercase().str.strip_chars()
pl.col("price").round(2).cast(pl.Int32).alias("cents")
```

</div>

<div>

### Contexts (the "where")

```python
df.select(...)        # choose/transform columns
df.with_columns(...)  # add new columns
df.filter(...)        # filter rows
df.group_by(...).agg(...)  # aggregate
```

Contexts accept expressions and define what to do with the result.

</div>

</div>

<div class="mt-6 bg-blue-50 dark:bg-blue-900/30 p-3 rounded text-center">

**Pattern:** `df.context(expression)` → `df.select(pl.col("price") * 1.1)`

</div>

---

# Expression API in Action

```python
import polars as pl
from pathlib import Path

df = pl.read_csv(Path("data") / "sales_data.csv")

result = df.select(
    pl.col("product"),                                    # Select column
    pl.col("price").round(2).alias("rounded_price"),     # Transform + rename
    (pl.col("quantity") * pl.col("price")).alias("revenue"),  # Compute
    pl.col("date").str.to_date().alias("parsed_date"),   # Parse dates
    pl.lit("USD").alias("currency")                       # Add constant
)
```

<div class="grid grid-cols-3 gap-4 mt-4 text-sm">

<div class="bg-gray-50 dark:bg-gray-800 p-2 rounded">

**`select()`** - Choose columns

```python
df.select(
    pl.col("name"),
    pl.col("price") * 2
)
```

</div>

<div class="bg-gray-50 dark:bg-gray-800 p-2 rounded">

**`with_columns()`** - Add columns

```python
df.with_columns(
    (pl.col("a") + pl.col("b"))
    .alias("sum")
)
```

</div>

<div class="bg-gray-50 dark:bg-gray-800 p-2 rounded">

**`filter()`** - Filter rows

```python
df.filter(
    pl.col("price") > 100
)
```

</div>

</div>

---

# Lazy Evaluation

For large data, Polars can build a query plan first and optimize before running.

<div class="grid grid-cols-2 gap-8 mt-6">

<div class="bg-gray-50 dark:bg-gray-800 p-4 rounded">

### Eager (runs immediately)

```python
df = pl.read_csv(data)
result = df.filter(pl.col("x") > 0)
```

Each operation executes as it's called. Simple, but loads all data into memory.

</div>

<div class="bg-orange-50 dark:bg-orange-900/30 p-4 rounded">

### Lazy (builds plan first)

```python
lf = pl.scan_csv(data)
query = lf.filter(pl.col("x") > 0)
          .select(["a", "b"])
result = query.collect()  # runs here
```

Builds a plan, optimizes it, then executes. Only loads needed columns.

</div>

</div>

<div class="text-center mt-2 mb-2 font-bold">Why Lazy Wins for Large Data</div>

<div class="grid grid-cols-3 gap-4">

<div class="bg-blue-50 dark:bg-blue-900/30 p-1 rounded text-center">

**Query Optimization**

Reorders operations, removes unused work

</div>

<div class="bg-blue-50 dark:bg-blue-900/30 p-1 rounded text-center">

**Memory Efficiency**

Filters before loading, streams large files

</div>

<div class="bg-blue-50 dark:bg-blue-900/30 p-1 rounded text-center">

**Smarter Parallelism**

Parallelizes across the entire query plan

</div>

</div>

---

# Performance Comparison

<div class="text-sm opacity-70">Task: Calculate revenue and sort by store (3 columns, 1M/10M/100M rows)</div>

<div class="grid grid-cols-2 gap-3 mt-2">

<div class="bg-gray-50 dark:bg-gray-800 p-2 rounded text-xs">

**Pandas**
```python
df_pd = pd.DataFrame(data)
df_pd["revenue"] = df_pd["quantity"] * df_pd["price"]
result = df_pd.sort_values(["store_id", "revenue"],
                           ascending=[True, False])
```

</div>

<div class="bg-orange-50 dark:bg-orange-900/30 p-2 rounded text-xs">

**Polars**
```python
df_pl = pl.DataFrame(data)
rev = pl.col("quantity") * pl.col("price")
result = df_pl.with_columns(rev.alias("revenue")).sort(
    ["store_id", "revenue"], descending=[False, True])
```

</div>

</div>

<div class="mt-3 text-xs">

| Rows | Pandas | Polars | Speedup |
|------|--------|--------|---------|
| 1 million | 0.25s | 0.03s | **9x** |
| 10 million | 4.1s | 0.38s | **11x** |
| 100 million | 75s | 4.8s | **16x** |

</div>

<div class="mt-2 bg-green-50 dark:bg-green-900/30 p-2 rounded text-center text-sm">

The speedup **increases with data size** - Polars shines on large datasets

</div>

---

# Common Operations Cheat Sheet

<div class="grid grid-cols-2 gap-4 text-sm mt-2">

<div>

### Reading/Writing

```python
from pathlib import Path
data = Path("data")

# Read
df = pl.read_csv(data / "file.csv")
df = pl.read_parquet(data / "file.parquet")
lf = pl.scan_csv(data / "file.csv")  # Lazy

# Write
df.write_csv(data / "out.csv")
df.write_parquet(data / "out.parquet")
```

### Filtering

```python
df.filter(pl.col("x") > 10)
df.filter(
    (pl.col("x") > 10) & (pl.col("y") < 5)
)
df.filter(pl.col("name").is_in(["A", "B"]))
```

</div>

<div>

### Aggregations

```python
df.group_by("category").agg(
    pl.col("value").sum().alias("total"),
    pl.col("value").mean().alias("average"),
    pl.len().alias("count"),
    pl.col("id").n_unique().alias("unique_ids")
)
```

### Sorting & Limiting

```python
df.sort("price", descending=True)
df.sort(["cat", "price"], descending=[True, False])
df.head(10)
df.tail(5)
```

</div>

</div>

---

# When to Use Polars vs Pandas

<div class="grid grid-cols-2 gap-6 mt-4">

<div class="bg-green-50 dark:bg-green-900/30 p-4 rounded">

### Use Polars When

- Working with **large datasets** (>100MB)
- Performance is critical
- Memory is constrained
- Building **data pipelines**
- Starting a **new project**
- Need **lazy evaluation**

<div class="text-xs opacity-70 mt-2 italic">

Time to process 111 million rows: 5.2 seconds

</div>

</div>

<div class="bg-blue-50 dark:bg-blue-900/30 p-4 rounded">

### Use Pandas When

- Working with **small datasets**
- Need specific **library integrations**
- Team familiarity matters
- Using **legacy code**
- Need **extensive ecosystem** (statsmodels, sklearn)
- Quick exploratory analysis

</div>

</div>

<div class="mt-4 bg-yellow-50 dark:bg-yellow-900/30 p-3 rounded text-center">

**Tip:** Convert between them with Polars: `pl_df.to_pandas()` and `pl.from_pandas(pd_df)`

</div>
