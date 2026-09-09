# Data Types, Missing Values & Duplicates

### Learning Source

**Kaggle Learn Pandas — Lesson 5: Data Types and Missing Values**

**Instructor:** Aleksey Bilogur + contributors

**Course:** Pandas

**Lesson:** 5 of 6

**Focus:** Data types and missing values

---

# 1. Learning Objectives

By completing this section, I should be able to:

* Understand Pandas data types
* Inspect the data types of DataFrame columns
* Identify missing values
* Count missing values
* Select rows containing missing values
* Understand `NaN`
* Use `isna()`
* Use `isnull()`
* Handle missing values with `fillna()`
* Remove missing values with `dropna()`
* Detect duplicate records
* Remove duplicate records with `drop_duplicates()`

---

# 2. Data Types

Every column in a Pandas DataFrame has a data type.

To inspect the data types:

```python
df.dtypes
```

For a more complete overview:

```python
df.info()
```

Common Pandas data types include:

| Data Type    | Meaning                  |
| ------------ | ------------------------ |
| `int64`      | Integer numbers          |
| `float64`    | Decimal numbers          |
| `object`     | Usually text/string data |
| `bool`       | True/False values        |
| `datetime64` | Date/time values         |

Understanding data types is important because different operations work differently depending on the type of data.

---

# 3. Why Data Types Matter

Consider:

```text
Age
25
30
35
```

This should normally be numerical data.

But:

```text
Age
"25"
"30"
"35"
```

contains strings rather than numbers.

The data may look similar to a human, but Pandas treats the two differently.

Always inspect:

```python
df.dtypes
```

before performing important analysis.

---

# 4. Missing Values

A missing value means that information is unavailable for a particular observation.

Example:

```text
EmployeeNumber | Department | MonthlyIncome
101            | Sales      | 5000
102            | HR         | NaN
103            | IT         | 7000
```

`NaN` represents a missing value.

Missing data is common in real-world datasets and needs to be identified before analysis.

---

# 5. Detecting Missing Values with `isna()`

Use:

```python
df.isna()
```

This returns a Boolean DataFrame.

```text
True  → value is missing
False → value is present
```

For example:

```text
Age
25    → False
30    → False
NaN   → True
```

---

# 6. Counting Missing Values

To count missing values in every column:

```python
df.isna().sum()
```

This is one of the most useful first-pass data-quality checks.

To show only columns containing missing values:

```python
missing = df.isna().sum()

missing[missing > 0]
```

---

# 7. `isnull()`

`isnull()` can also be used to identify missing values.

```python
df.isnull()
```

It can also be combined with `sum()`:

```python
df.isnull().sum()
```

For practical Pandas work:

```python
df.isna()
```

and:

```python
df.isnull()
```

serve the same missing-value detection purpose.

Example:

```python
df.isna().sum()
```

and:

```python
df.isnull().sum()
```

produce equivalent missing-value counts.

---

# 8. Selecting Missing Values

Missing values can be used as a Boolean filter.

Example:

```python
df[df["MonthlyIncome"].isna()]
```

This returns rows where `MonthlyIncome` is missing.

The same idea can be written using `isnull()`:

```python
df[df["MonthlyIncome"].isnull()]
```

---

# 9. Selecting Non-Missing Values

Use `notna()` to identify values that are present.

```python
df[df["MonthlyIncome"].notna()]
```

This keeps rows where `MonthlyIncome` is not missing.

---

# 10. Missing-Value Percentage

Counting missing values tells us the number of missing records.

We can also calculate the percentage:

```python
missing_percentage = df.isna().mean() * 100

missing_percentage
```

To show only columns containing missing values:

```python
missing_percentage[
    missing_percentage > 0
].sort_values(ascending=False)
```

This helps determine the severity of missing data.

---

# 11. Filling Missing Values with `fillna()`

`fillna()` replaces missing values with another value.

Basic syntax:

```python
df["column"].fillna(value)
```

---

## Mean

For numerical data:

```python
df["MonthlyIncome"].fillna(
    df["MonthlyIncome"].mean()
)
```

This replaces missing values with the column mean.

---

## Median

```python
df["MonthlyIncome"].fillna(
    df["MonthlyIncome"].median()
)
```

Median can be useful when the data contains extreme values or is skewed.

---

## Mode

For categorical data:

```python
df["Department"].fillna(
    df["Department"].mode()[0]
)
```

The mode represents the most frequently occurring value.

---

## Important

Do not automatically fill all missing values with the mean, median, or mode.

The appropriate method depends on:

* Data type
* Amount of missing data
* Distribution
* Meaning of the column
* Business context

---

# 12. Removing Missing Values with `dropna()`

`dropna()` removes rows containing missing values.

```python
df.dropna()
```

To see how many rows remain:

```python
df.dropna().shape
```

Compare with:

```python
df.shape
```

This shows how much data would be lost by removing rows containing missing values.

---

# 13. Important `dropna()` Idea

Do not automatically delete every row containing a missing value.

Before using `dropna()`, ask:

```text
How many values are missing?
        ↓
Which columns are affected?
        ↓
How many rows will be removed?
        ↓
Is removing those rows acceptable?
```

Data cleaning should be based on evidence rather than blindly applying a method.

---

# 14. Detecting Duplicate Records

A duplicate record is a row that appears more than once.

To check duplicate rows:

```python
df.duplicated()
```

To count them:

```python
df.duplicated().sum()
```

Result:

```text
0 → no duplicate rows
>0 → duplicate rows exist
```

---

# 15. Inspecting Duplicate Rows

To see duplicate rows:

```python
df[df.duplicated()]
```

To display all rows involved in duplicated groups:

```python
df[df.duplicated(keep=False)]
```

This makes it easier to inspect whether the duplicates are genuine duplicates or legitimate repeated records.

---

# 16. Removing Duplicates

Use:

```python
df_clean = df.drop_duplicates()
```

Compare the original and cleaned DataFrames:

```python
df.shape
```

```python
df_clean.shape
```

Calculate the number of removed records:

```python
duplicates_removed = len(df) - len(df_clean)

duplicates_removed
```

---

# 17. Practical HR Analytics Workflow

For the `HR_Analytics.csv` dataset, the first-pass data-quality workflow is:

```text
Load Dataset
     ↓
Inspect Data Types
     ↓
Check Missing Values
     ↓
Count Missing Values
     ↓
Identify Columns with Gaps
     ↓
Inspect Missing Records
     ↓
Choose an Appropriate Treatment
     ↓
Check Duplicate Records
     ↓
Inspect Duplicates
     ↓
Remove Duplicates if Appropriate
     ↓
Validate the Dataset
```

---

# 18. HR Dataset Checks

Load the dataset:

```python
import pandas as pd

df = pd.read_csv("../data/raw/HR_Analytics.csv")
```

Inspect data types:

```python
df.dtypes
```

Inspect the DataFrame:

```python
df.info()
```

Check missing values:

```python
df.isna().sum()
```

Alternative:

```python
df.isnull().sum()
```

Check duplicate records:

```python
df.duplicated().sum()
```

---

# 19. Kaggle Lesson 5: Key Practice Concepts

The Kaggle lesson focuses on two important data-quality areas:

### Data Types

Understand and inspect the types of data stored in DataFrame columns.

Main tool:

```python
df.dtypes
```

### Missing Data

Identify missing values and work with them appropriately.

Main tools:

```python
pd.isnull()
```

```python
df.isnull()
```

```python
df.isna()
```

Kaggle's exercise section then gives hands-on tasks involving missing-value detection and selection.

---

# 20. Kaggle Exercise Pattern: Finding Missing Values

A common pattern from the lesson is:

```python
reviews[pd.isnull(reviews.country)]
```

This selects rows where the `country` value is missing.

The same concept can be written using the Series method:

```python
reviews[reviews.country.isnull()]
```

Both approaches express the same missing-value filtering idea.

---

# 21. Kaggle Exercise Pattern: Counting Missing Values

To count missing records in a column:

```python
reviews[reviews.price.isnull()]
```

Then:

```python
len(reviews[reviews.price.isnull()])
```

This gives the number of rows where `price` is missing.

The important pattern is:

```text
Detect → Filter → Count
```

---

# 22. `isna()` vs `isnull()`

Both can be used for missing-value detection.

```python
df.isna()
```

```python
df.isnull()
```

For a count:

```python
df.isna().sum()
```

or:

```python
df.isnull().sum()
```

### Mental Model

```text
isna()    → Is this value missing?
isnull()  → Is this value missing?
```

For your future notebooks, using `isna()` consistently is a clean choice.

---

# 23. First-Pass Data Quality Checklist

```text
☐ Check column data types
☐ Check missing values
☐ Count missing values
☐ Identify columns with gaps
☐ Calculate missing percentages
☐ Inspect missing records
☐ Decide whether to fill or remove missing values
☐ Check duplicate rows
☐ Inspect duplicate records
☐ Remove duplicates when appropriate
☐ Validate the result
```

---

# 24. Quick Reference

| Method              | Purpose                               |
| ------------------- | ------------------------------------- |
| `df.dtypes`         | Inspect column data types             |
| `df.info()`         | Inspect DataFrame structure and types |
| `isna()`            | Detect missing values                 |
| `isnull()`          | Detect missing values                 |
| `notna()`           | Detect non-missing values             |
| `isna().sum()`      | Count missing values                  |
| `fillna()`          | Replace missing values                |
| `dropna()`          | Remove rows containing missing values |
| `duplicated()`      | Detect duplicate rows                 |
| `drop_duplicates()` | Remove duplicate rows                 |

---

# 25. Key Mental Model

```text
DATA QUALITY
     │
     ├── Data Types
     │      ↓
     │   Inspect with dtypes / info
     │
     ├── Missing Values
     │      ↓
     │   Detect → Count → Inspect → Handle
     │
     └── Duplicates
            ↓
         Detect → Inspect → Remove → Validate
```

---

# 26. Key Takeaway

Before analyzing a dataset, first understand its quality.

A professional data workflow does not immediately modify the data.

Instead:

```text
Detect
   ↓
Measure
   ↓
Understand
   ↓
Choose a treatment
   ↓
Validate
```

The most important Day 6 methods are:

```python
df.isna()
df.isnull()
df.isna().sum()

df.fillna(...)
df.dropna()

df.duplicated()
df.duplicated().sum()
df.drop_duplicates()
```

**Data Types, Missing Values & Duplicates**
