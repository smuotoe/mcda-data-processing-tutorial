# MCDA 5511: Data Processing in Python

A tutorial on the modern Python data stack covering `pathlib`, `pandas`, `polars`, and `DuckDB`.

## Live Slides

View the presentation: [mcda-data-processing.pages.dev](https://mcda-data-processing.pages.dev)

## Contents

### Slides

The presentation covers four main topics:

1. **Part 1: File Operations with pathlib** - Cross-platform file handling, path manipulation, reading/writing files
2. **Part 2: Pandas Fundamentals** - DataFrames, data manipulation, aggregations
3. **Part 3: Polars** - The modern alternative to pandas, lazy evaluation, expression API
4. **Part 4: DuckDB** - SQL meets DataFrames, querying files directly, integration patterns

### Exercise Files

| File | Description |
|------|-------------|
| `01-file-operations.py` | pathlib exercises |
| `02-pandas-polars-intro.py` | Introduction to pandas and polars |
| `03-pandas-polars-performance.py` | Performance comparison benchmarks |
| `04-duckdb-intro.py` | DuckDB basics |
| `05-polars-duckdb-performance.py` | Polars vs DuckDB benchmarks |

### Data

- `data/sales_data.csv` - Sample sales data for exercises

## Getting Started

### Prerequisites

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) (recommended) or pip

### Setup

```bash
# Clone the repository
git clone https://github.com/smuotoe/mcda-data-processing-tutorial.git
cd mcda-data-processing-tutorial

# Install dependencies with uv
uv sync
```

### Running Exercises

```bash
# With uv
uv run python 01-file-operations.py

# Or directly
python 01-file-operations.py
```

## PDF Exports

PDF versions of the slides are available in the `slides/` folder:

- `mcda-5511-data-processing-in-python-light.pdf` - Light mode
- `mcda-5511-data-processing-in-python-dark.pdf` - Dark mode

## Resources

### Documentation

- [pathlib](https://docs.python.org/3/library/pathlib.html)
- [Pandas](https://pandas.pydata.org/docs/)
- [Polars](https://docs.pola.rs/)
- [DuckDB](https://duckdb.org/docs/)

### Tutorials & Guides

- [Polars User Guide](https://docs.pola.rs/user-guide/)
- [Pandas to Polars Migration](https://docs.pola.rs/user-guide/migration/pandas/)
- [Modern Polars](https://kevinheavey.github.io/modern-polars/) (cookbook)
