---
layout: image-right
image: /images/part-01-pathlib.png
backgroundSize: cover
backgroundPosition: center
class: flex flex-col justify-center
---

<div class="pr-8">
<div class="text-blue-400 uppercase tracking-widest text-sm font-semibold mb-2">Part 1</div>
<h1 class="!text-5xl font-bold !leading-tight">File Operations<br/>with pathlib</h1>
<div class="mt-4 text-gray-400 text-lg">Cross-platform file handling in Python</div>
</div>

---

# What is pathlib?

A **built-in Python module** for working with file system paths in an object-oriented way. Part of the standard library since Python 3.4.

<div class="grid grid-cols-2 gap-8 mt-6">

<div>

### The Problem

- File paths differ across operating systems (`/` on Unix vs `\` on Windows)
- String manipulation for paths is error-prone
- `os.path` functions are verbose and scattered
- Edge cases are easy to miss (e.g., `data/` vs `data`)

</div>

<div class="bg-blue-50 dark:bg-blue-900/30 p-4 rounded">

### How pathlib Solves It

- **Cross-platform** - handles path separators automatically
- **Object-oriented** - paths are objects with methods
- **Intuitive operators** - use `/` to join paths
- **All-in-one** - file operations built into Path objects

```python
from pathlib import Path

path = Path("data") / "sales.csv"
content = path.read_text()
```

</div>

</div>

---

# Why pathlib?

<div class="grid grid-cols-2 gap-4 mt-6">

<div class="bg-red-50 dark:bg-red-900/30 p-4 rounded">

### The Old Way: os.path

```python
import os

# Joining paths - error prone
path = os.path.join("data", "sales", "2024.csv")

# Reading a file
with open(path, 'r') as f:
    content = f.read()

# Getting file info
exists = os.path.exists(path)
is_file = os.path.isfile(path)
name = os.path.basename(path)
```

<div class="text-sm mt-2 opacity-70">String-based, verbose, platform issues</div>

</div>

<div class="bg-green-50 dark:bg-green-900/30 p-4 rounded">

### The Modern Way: pathlib

```python
from pathlib import Path

# Joining paths - clean and safe
path = Path("data") / "sales" / "2024.csv"

# Reading a file
content = path.read_text()

# Getting file info
exists = path.exists()
is_file = path.is_file()
name = path.name
```

<div class="text-sm mt-2 opacity-70">Object-oriented, readable, cross-platform</div>

</div>

</div>

<div class="mt-1 bg-yellow-50 dark:bg-yellow-900/30 p-1 rounded text-sm">

**An edge case pathlib handles:** 
  `"data/" + "file.txt"` → `"data/file.txt"` but `"data" + "file.txt"` → `"datafile.txt"` ✗

With pathlib: `Path("data") / "file.txt"` and `Path("data/") / "file.txt"` both → `data/file.txt` ✓

</div>

---

# Path Basics

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### Creating Paths

```python
from pathlib import Path

# Current directory
cwd = Path.cwd()

# Home directory
home = Path.home()  # C:\Users\somto

# From string
data_path = Path("data/sales")

# Absolute path
abs_path = Path("C:\Users\somto\projects")

# Joining paths with /
file_path = data_path / "report.csv"
```

</div>

<div>

### Path Properties

```python
path = Path("data/sales/2024_q1.csv")

path.name        # "2024_q1.csv"
path.stem        # "2024_q1"
path.suffix      # ".csv"
path.parent      # Path("data/sales")
path.parts       # ("data", "sales", "2024_q1.csv")

# Resolve to absolute
path.resolve()   # Full absolute path

# Check path type
path.is_file()   # True/False
path.is_dir()    # True/False
path.exists()    # True/False
```

</div>

</div>

---

# Reading & Writing Files

<div class="grid grid-cols-2 gap-6 mt-4">

<div>

### Reading Files

```python
from pathlib import Path

path = Path("data/config.json")

# Read entire file as text
content = path.read_text()

# Read as bytes (for binary files)
binary = path.read_bytes()

# With encoding
content = path.read_text(encoding="utf-8")
```

</div>

<div>

### Writing Files

```python
from pathlib import Path
import json

path = Path("output/results.json")

# Ensure parent directory exists
path.parent.mkdir(parents=True, exist_ok=True)

# Write text
path.write_text("Hello, World!")

# Write JSON data
data = {"name": "test", "value": 42}
path.write_text(json.dumps(data, indent=2))

# Write bytes
path.write_bytes(b"binary data")
```

</div>

</div>

<div class="mt-3 bg-blue-50 dark:bg-blue-900/30 p-2 rounded text-sm">

**Tip:** `read_text()` and `write_text()` handle file opening/closing via context managers automatically

</div>

---

# Directory Operations

```python
from pathlib import Path

# Create directories
Path("output/reports").mkdir(parents=True, exist_ok=True)

# List directory contents
for item in Path("data").iterdir():
    print(item.name, "- dir" if item.is_dir() else "- file")

# Find files with glob patterns
csv_files = list(Path("data").glob("*.csv"))           # Direct children only
all_csvs = list(Path("data").rglob("*.csv"))           # All subdirectories

# Pattern matching examples
Path(".").glob("*.py")              # All Python files
Path(".").glob("**/*.json")         # All JSON files (recursive)
Path(".").glob("data_202[0-9].csv") # data_2020.csv through data_2029.csv
```

<div class="mt-4 bg-yellow-50 dark:bg-yellow-900/30 p-3 rounded text-sm">

**Note:** `rglob("*.csv")` is shorthand for `glob("**/*.csv")` - both search recursively

</div>

---

# Practical Example: Organizing Files

```python
from pathlib import Path
import shutil

def organize_downloads():
    downloads = Path.home() / "Downloads"
    categories = {
        "images": [".jpg", ".png", ".gif"],
        "documents": [".pdf", ".docx", ".txt"],
        "data": [".csv", ".json", ".parquet"],
    }
    for path in downloads.iterdir():  # iterate over directory contents
        if not path.is_file():
            continue
        for category, extensions in categories.items():
            if path.suffix.lower() in extensions:
                dest_dir = downloads / category
                dest_dir.mkdir(exist_ok=True)
                shutil.move(path, dest_dir / path.name)  # move to category folder
                break
```

<div class="mt-2 text-sm opacity-70">

Combines `iterdir()`, `is_file()`, `suffix`, `mkdir()`, and path joining

</div>

---
layout: center
---

# Exercise: File Operations

<div class="text-left max-w-2xl">

### Task: Create a Student Profile System

1. Create a directory named `profiles`
2. Save your profile as JSON (name, languages, hobby)
3. List all `.json` files in the directory
4. Read and display your profile

```python
from pathlib import Path
import json

def quick_profile_update():
    # 1. Create profiles directory
    # TODO: Use Path().mkdir()

    # 2. Create and save profile
    my_profile = {"name": "Your Name", "languages": ["Python"]}
    # TODO: Write to profiles/my_profile.json

    # 3. List all profiles
    # TODO: Use glob("*.json")

    # 4. Read and print
    # TODO: Use read_text() and json.loads()
```

</div>
