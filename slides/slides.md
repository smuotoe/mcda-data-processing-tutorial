---
theme: default
title: "MCDA 5511: Data Processing in Python"
info: |
  ## Data Processing in Python
  The Modern Python Data Stack: pathlib, pandas, polars & duckdb
class: text-center
highlighter: shiki
drawings:
  persist: false
transition: slide-left
mdc: true
---

<div class="absolute top-12 left-1/2 -translate-x-1/2 text-center">
  <div class="text-xs uppercase tracking-[0.3em] opacity-50">MCDA 5511</div>
  <div class="text-sm opacity-60 mt-1">Current Practices in Computing and Data Science</div>
</div>

<div class="mt-16">
  <h1 class="!text-5xl font-bold tracking-tight">Data Processing in Python</h1>
  <p class="text-xl opacity-70 mt-4">The Modern Python Data Stack</p>
</div>

<div class="absolute bottom-14 left-1/2 -translate-x-1/2 text-center">
  <div class="text-base font-medium">Somto Muotoe</div>
  <div class="text-sm opacity-50 mt-1">{{ new Date().toLocaleDateString('en-US', { month: 'long', year: 'numeric' }) }}</div>
</div>

---
layout: default
---

# Agenda

<div class="grid grid-cols-2 gap-8 mt-8">

<div class="border-l-4 border-blue-500 pl-4 py-2">
<div class="font-bold text-lg mb-3">Part 1: File Operations</div>
<div class="opacity-80">

- What is pathlib?
- Path basics & properties
- Reading & writing files
- Directory operations

</div>
</div>

<div class="border-l-4 border-green-500 pl-4 py-2">
<div class="font-bold text-lg mb-3">Part 2: Pandas</div>
<div class="opacity-80">

- DataFrame fundamentals
- Reading data files
- Data manipulation
- Aggregations & grouping

</div>
</div>

<div class="border-l-4 border-orange-500 pl-4 py-2">
<div class="font-bold text-lg mb-3">Part 3: Polars</div>
<div class="opacity-80">

- Why Polars over Pandas?
- Lazy vs Eager evaluation
- Expression API
- Performance comparison

</div>
</div>

<div class="border-l-4 border-purple-500 pl-4 py-2">
<div class="font-bold text-lg mb-3">Part 4: DuckDB</div>
<div class="opacity-80">

- What is DuckDB?
- SQL on DataFrames
- When to use DuckDB
- Integration patterns

</div>
</div>

</div>

<div class="mt-2 bg-blue-50 dark:bg-blue-900/30 p- rounded text-sm text-center">

**Setup:** Clone the repo for data files and exercises → [github.com/smuotoe/mcda-data-processing-tutorial](https://github.com/smuotoe/mcda-data-processing-tutorial)

</div>

---
src: ./part-01-pathlib.md
---

---
src: ./part-02-pandas.md
---

---
src: ./part-03-polars.md
---

---
src: ./part-04-duckdb.md
---
