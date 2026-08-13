# Pandas Indexing, Selection & Slicing

## Day 2 |  Pandas Fundamentals

### Overview

Pandas provides multiple ways to access, select, filter, and slice data from a DataFrame.

In this practice, I worked with the **HR Analytics dataset** and learned how to:

* Select single and multiple columns
* Select rows using labels
* Select rows and columns using `.loc[]`
* Select data by integer position using `.iloc[]`
* Slice rows and columns
* Select specific individual values
* Filter rows using conditions
* Combine multiple conditions
* Filter using `isin()`
* Check non-null values using `notnull()`
* Work with DataFrame indexes
* Create reusable subsets of a dataset

---

## 1. Loading the Dataset

```python
import pandas as pd

hr = pd.read_csv("../data/raw/HR_Analytics.csv")
```

The dataset is stored in the `hr` DataFrame.

Basic inspection:

```python
hr.head()
```

---

# 2. Selecting a Single Column

A single column can be selected using its column name.

```python
hr["Department"]
```

This returns a Pandas `Series`.

Example:

```python
hr["EducationField"]
```

### Key point

```text
df["column"]
```

returns a **Series**.

---

# 3. Selecting Multiple Columns

Multiple columns can be selected by passing a list of column names.

```python
hr[["EmployeeNumber", "Department", "EducationField"]]
```

This returns a **DataFrame**.

### Difference

```python
hr["Department"]
```

Single column → Series

```python
hr[["Department"]]
```

Single column inside a list → DataFrame

---

# 4. Selecting a Specific Value

A specific value can be accessed using `.loc[]`.

```python
hr.loc[0, "EducationField"]
```

This selects:

* Row label: `0`
* Column: `EducationField`

Example result:

```text
Life Sciences
```

---

# 5. Selecting a Complete Row with `.loc[]`

```python
hr.loc[0, :]
```

This selects the complete row with index label `0`.

The `:` means:

```text
all columns
```

---

# 6. Selecting Rows with `.loc[]`

`.loc[]` is primarily **label-based indexing**.

Select one row:

```python
hr.loc[0]
```

Select multiple specific rows:

```python
hr.loc[[1, 4, 6, 8, 9]]
```

Select a range:

```python
hr.loc[0:5]
```

### Important

With `.loc[]`, the ending label is generally **included** when slicing.

---

# 7. Selecting Rows and Columns with `.loc[]`

The general structure is:

```python
df.loc[rows, columns]
```

Example:

```python
hr.loc[5, "Department"]
```

Select one value.

Select multiple columns from one row:

```python
hr.loc[5, ["Department", "EducationField"]]
```

Select rows and columns:

```python
hr.loc[0:99, ["EmployeeNumber", "Department", "EducationField"]]
```

This selects:

* Rows `0` through `99`
* Only the three specified columns

Result:

```text
100 rows × 3 columns
```

---

# 8. Selecting Data with `.iloc[]`

`.iloc[]` uses **integer position** instead of labels.

Select the first row:

```python
hr.iloc[0]
```

Select rows by position:

```python
hr.iloc[0:5]
```

Select rows and columns by position:

```python
hr.iloc[5, 0:7]
```

This means:

```text
Row position → 5
Column positions → 0 through 6
```

### Key difference

```text
.loc[]  → label-based
.iloc[] → position-based
```

---

# 9. Selecting the First 10 Values of a Column

A column can also be accessed first and then sliced.

```python
first_EducationField = hr["EducationField"].iloc[:10]

first_EducationField
```

This returns the first 10 values from the `EducationField` column.

---

# 10. Using an Index List for Selection

A list of index labels can be used with `.loc[]`.

```python
indexed = hr.loc[[1, 4, 6, 8, 9]]

indexed
```

This selects only the specified rows.

---

# 11. Selecting Specific Rows and Columns Together

We can store the required rows and columns in variables.

```python
columns = [
    "EmployeeNumber",
    "Department",
    "EducationField"
]

indices = [0, 1, 10, 100]

df = hr.loc[indices, columns]

df
```

This is useful when building reusable data-selection logic.

---

# 12. Filtering with a Condition

Pandas allows rows to be filtered using Boolean conditions.

Example:

```python
hr["EducationField"] == "Medical"
```

This produces a Boolean Series:

```text
True
False
True
False
...
```

The Boolean values indicate whether each row satisfies the condition.

---

# 13. Filtering Rows with `.loc[]`

The Boolean condition can be passed directly to `.loc[]`.

```python
hr.loc[hr["EducationField"] == "Medical"]
```

This returns employees whose education field is `Medical`.

---

# 14. Filtering with Multiple Conditions

Multiple conditions can be combined.

### AND condition

Use `&`.

```python
hr.loc[
    (hr["EducationField"] == "Medical") &
    (hr["Age"] > 30)
]
```

This means:

```text
EducationField = Medical
AND
Age > 30
```

Only rows satisfying both conditions are returned.

---

# 15. OR Condition

Use `|` for OR.

```python
hr.loc[
    (hr["EducationField"] == "Medical") |
    (hr["Age"] > 30)
]
```

This returns rows where either condition is true.

### Important

Use parentheses around each condition.

Correct:

```python
(df["Age"] > 30) & (df["Education"] == 3)
```

Do not use Python's `and` or `or` for Pandas Series conditions.

---

# 16. Filtering with `isin()`

`isin()` is useful when checking whether a value belongs to a list of values.

Example:

```python
hr.loc[
    hr["BusinessTravel"].isin(
        ["Non-Travel", "Travel_Frequently"]
    )
]
```

This selects employees whose `BusinessTravel` value is either:

* `Non-Travel`
* `Travel_Frequently`

This is cleaner than writing multiple OR conditions.

---

# 17. Checking Non-Null Values

`notnull()` checks whether values are present.

Example:

```python
hr.loc[
    hr["YearsInCurrentRole"].notnull()
]
```

This returns rows where `YearsInCurrentRole` is not missing.

### Related method

```python
df["column"].isnull()
```

checks for missing values.

```python
df["column"].notnull()
```

checks for non-missing values.

---

# 18. Indexing with `set_index()`

I also practiced setting a column as the DataFrame index.

```python
hr.set_index("EmployeeNumber")
```

This creates a DataFrame where `EmployeeNumber` is used as the index.

### Important behavior

By default:

```python
hr.set_index("EmployeeNumber")
```

returns a new DataFrame.

It does **not** permanently modify `hr`.

To modify `hr` directly:

```python
hr.set_index("EmployeeNumber", inplace=True)
```

Or assign the result:

```python
hr = hr.set_index("EmployeeNumber")
```

For learning and production code, understanding whether an operation returns a new object or modifies the existing object is important.

---

# 19. Common Selection Patterns

### One column

```python
df["column"]
```

### Multiple columns

```python
df[["column1", "column2"]]
```

### One row

```python
df.loc[0]
```

### Specific rows

```python
df.loc[[1, 5, 10]]
```

### Specific rows and columns

```python
df.loc[[1, 5, 10], ["name", "salary"]]
```

### Row range

```python
df.loc[0:10]
```

### Position-based selection

```python
df.iloc[0:5, 0:3]
```

### Conditional filtering

```python
df.loc[df["salary"] > 50000]
```

### Multiple conditions

```python
df.loc[
    (df["salary"] > 50000) &
    (df["age"] > 30)
]
```

### Multiple possible values

```python
df.loc[
    df["department"].isin(["Sales", "HR"])
]
```

### Non-null values

```python
df.loc[
    df["salary"].notnull()
]
```

---

# 20. `.loc[]` vs `.iloc[]`

| Method    | Selection type | Example            |
| --------- | -------------- | ------------------ |
| `.loc[]`  | Label-based    | `df.loc[5, "Age"]` |
| `.iloc[]` | Position-based | `df.iloc[5, 2]`    |

### Easy way to remember

```text
loc  → location by label
iloc → integer location
```

---

# 21. Important Difference in Slicing

`.loc[]` uses labels:

```python
df.loc[0:5]
```

`.iloc[]` uses integer positions:

```python
df.iloc[0:5]
```

A key difference is that `.loc[]` generally includes the ending label in a slice, while `.iloc[]` follows normal Python positional slicing and excludes the ending position.

---

# 22. Practical HR Analytics Examples

### Employees from Sales

```python
hr.loc[
    hr["Department"] == "Sales"
]
```

### Employees older than 30

```python
hr.loc[
    hr["Age"] > 30
]
```

### Medical education field

```python
hr.loc[
    hr["EducationField"] == "Medical"
]
```

### Medical education field and age above 30

```python
hr.loc[
    (hr["EducationField"] == "Medical") &
    (hr["Age"] > 30)
]
```

### Employees with selected travel types

```python
hr.loc[
    hr["BusinessTravel"].isin(
        ["Non-Travel", "Travel_Frequently"]
    )
]
```

These examples demonstrate how Pandas can turn a large HR dataset into targeted business subsets.

---

# 23. Common Mistakes

### Mistake 1: Incorrect `.loc[]` syntax

Incorrect:

```python
df.loc[[:99], ["country", "variety"]]
```

Correct:

```python
df.loc[:99, ["country", "variety"]]
```

---

### Mistake 2: Using `and`

Incorrect:

```python
df[(df["Age"] > 30) and (df["Education"] == 3)]
```

Correct:

```python
df[(df["Age"] > 30) & (df["Education"] == 3)]
```

---

### Mistake 3: Forgetting parentheses

Correct:

```python
df[(df["Age"] > 30) & (df["Education"] == 3)]
```

---

### Mistake 4: Confusing `.loc[]` and `.iloc[]`

```text
loc  → labels
iloc → positions
```

---

### Mistake 5: Assuming `set_index()` changes the original DataFrame

```python
hr.set_index("EmployeeNumber")
```

does not permanently change `hr` unless the result is assigned or `inplace=True` is used.

---

# 24. Day 2 Practice Completed

The Day 2 notebook covered:

* [x] Single-column selection
* [x] Multiple-column selection
* [x] `.loc[]`
* [x] `.iloc[]`
* [x] Row selection
* [x] Column selection
* [x] Row and column slicing
* [x] Specific index selection
* [x] Conditional filtering
* [x] Multiple conditions with `&`
* [x] Multiple conditions with `|`
* [x] `isin()`
* [x] `notnull()`
* [x] DataFrame indexing with `set_index()`
* [x] HR Analytics dataset practice

---

# 25. Key Takeaways

The most important concepts from Day 2 are:

```text
df["column"]
    → select one column

df[["col1", "col2"]]
    → select multiple columns

df.loc[]
    → label-based selection

df.iloc[]
    → position-based selection

df.loc[condition]
    → conditional filtering

df.loc[condition, columns]
    → filter rows and select columns

df["column"].isin([...])
    → match multiple values

df["column"].notnull()
    → select non-missing values

df.loc[rows, columns]
    → precise row + column selection
```

## Day 2 Objective

> Learn to precisely select, index, slice, and filter real-world data using Pandas.

This skill is a foundation for the next stages of the Month 5 pipeline:

```text
Raw Data
    ↓
Inspect
    ↓
Select
    ↓
Filter
    ↓
Clean
    ↓
Transform
    ↓
Analyze
    ↓
GenAI Utility
```
