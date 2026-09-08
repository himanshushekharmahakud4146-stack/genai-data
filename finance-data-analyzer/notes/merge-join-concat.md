# Merge, Join & Concatenate

### Objective

Learn how to combine multiple Pandas DataFrames when data is stored in separate tables or datasets.

This section covers:

* `pd.concat()`
* Vertical concatenation
* Horizontal concatenation
* Concatenation with MultiIndex
* `DataFrame.merge()`
* `pd.merge()`
* Inner Join
* Left Join
* Right Join
* Outer Join
* Practical data-combination problems
* Choosing the correct operation for a real-world data problem

---

# 1. Why Do We Need to Combine Data?

Real-world data is often distributed across multiple tables.

For example:

```text
Employee Information
        +
Salary Information
        ↓
Combined Employee Dataset
```

Different datasets may contain related information that needs to be combined before analysis.

Example:

```text
EmployeeNumber | Name | Department
```

and

```text
EmployeeNumber | MonthlyIncome | HourlyRate
```

The common column `EmployeeNumber` can be used to connect the datasets.

---

# 2. Three Main Ways to Combine Data

Pandas provides different methods depending on how the data should be combined.

| Method        | Main Purpose                                 |
| ------------- | -------------------------------------------- |
| `pd.concat()` | Stack DataFrames together                    |
| `merge()`     | Combine DataFrames using common columns/keys |
| `join()`      | Combine DataFrames mainly using indexes      |

### Simple Mental Model

```text
concat  → stack
merge   → match
join    → align
```

---

# 3. `pd.concat()`

`pd.concat()` combines multiple Pandas objects along a particular axis.

Basic syntax:

```python
pd.concat([df1, df2])
```

By default:

```python
axis=0
```

which means rows are combined vertically.

---

# 4. Vertical Concatenation

Suppose we have two DataFrames:

```python
df1
df2
```

We can stack them:

```python
result = pd.concat([df1, df2])
```

Conceptually:

```text
df1
────
A
B
C

df2
────
D
E
F

↓ concat

A
B
C
D
E
F
```

This is useful when multiple datasets have the same or compatible columns.

---

# 5. Horizontal Concatenation

We can also concatenate DataFrames side by side.

Use:

```python
pd.concat([df1, df2], axis=1)
```

Here:

```text
axis=0 → rows
axis=1 → columns
```

Conceptually:

```text
df1        df2

A  B       X  Y
C  D       Z  W

↓ axis=1

A  B  X  Y
C  D  Z  W
```

Horizontal concatenation aligns data based on the index.

---

# 6. Concatenating Multiple DataFrames

More than two DataFrames can be concatenated:

```python
result = pd.concat([df1, df2, df3])
```

This is useful when data comes from multiple periods or sources.

Example:

```text
January Data
February Data
March Data
       ↓
Combined Data
```

---

# 7. Concatenation with MultiIndex

A `keys` argument can be used to identify the source of each DataFrame:

```python
result = pd.concat(
    [df1, df2],
    keys=["January", "February"]
)
```

This creates a hierarchical index that identifies where each row came from.

Conceptually:

```text
January
   row 1
   row 2

February
   row 1
   row 2
```

This is useful when the source of the data needs to be preserved after concatenation.

---

# 8. `append()` and Concatenation

Older Pandas workflows sometimes used:

```python
df1.append(df2)
```

for adding rows.

For modern Pandas code, prefer:

```python
pd.concat([df1, df2])
```

The important concept is that concatenation is the standard approach for combining DataFrames along rows or columns.

---

# 9. `merge()`

`merge()` combines DataFrames using one or more common columns or keys.

Basic syntax:

```python
df1.merge(df2, on="key")
```

or:

```python
pd.merge(df1, df2, on="key")
```

Example:

```text
Employee Table

EmployeeNumber | Name
---------------|------
101            | Rahul
102            | Priya
103            | Aman
```

```text
Salary Table

EmployeeNumber | Salary
---------------|-------
101            | 50000
102            | 60000
104            | 45000
```

The common key is:

```text
EmployeeNumber
```

We can combine them using:

```python
pd.merge(
    employee_info,
    salary_info,
    on="EmployeeNumber"
)
```

---

# 10. Inner Join

An inner join keeps only records that have matching keys in both DataFrames.

```python
pd.merge(
    employee_info,
    salary_info,
    on="EmployeeNumber",
    how="inner"
)
```

Conceptually:

```text
Left:   101  102  103
Right:  101  102  104

Result: 101  102
```

### Key idea

```text
INNER = matching records only
```

---

# 11. Left Join

A left join keeps all records from the left DataFrame and adds matching records from the right DataFrame.

```python
pd.merge(
    employee_info,
    salary_info,
    on="EmployeeNumber",
    how="left"
)
```

Conceptually:

```text
Left:   101  102  103
Right:  101  102  104

Result: 101  102  103
```

The unmatched right-side information becomes missing (`NaN`).

### Key idea

```text
LEFT = everything from the left
       + matching data from the right
```

---

# 12. Right Join

A right join keeps all records from the right DataFrame.

```python
pd.merge(
    employee_info,
    salary_info,
    on="EmployeeNumber",
    how="right"
)
```

Conceptually:

```text
Left:   101  102  103
Right:  101  102  104

Result: 101  102  104
```

### Key idea

```text
RIGHT = everything from the right
        + matching data from the left
```

---

# 13. Outer Join

An outer join keeps all records from both DataFrames.

```python
pd.merge(
    employee_info,
    salary_info,
    on="EmployeeNumber",
    how="outer"
)
```

Conceptually:

```text
Left:   101  102  103
Right:  101  102  104

Result: 101  102  103  104
```

Unmatched values become `NaN`.

### Key idea

```text
OUTER = everything from both sides
```

---

# 14. Join Types at a Glance

```text
INNER
→ Only matching records

LEFT
→ Everything from left + matches from right

RIGHT
→ Everything from right + matches from left

OUTER
→ Everything from both
```

| Join    | Keeps                     |
| ------- | ------------------------- |
| `inner` | Matching records          |
| `left`  | All left + matching right |
| `right` | All right + matching left |
| `outer` | All records from both     |

---

# 15. `on` Parameter

The `on` parameter specifies the common column used for merging.

```python
pd.merge(
    df1,
    df2,
    on="EmployeeNumber"
)
```

The column should represent the relationship between the datasets.

Examples:

```text
EmployeeNumber
CustomerID
ProductID
OrderID
StudentID
```

---

# 16. When Column Names Are Different

Sometimes the key column has different names in the two DataFrames.

Example:

```text
df1 → EmployeeNumber
df2 → EmployeeID
```

Use:

```python
pd.merge(
    df1,
    df2,
    left_on="EmployeeNumber",
    right_on="EmployeeID"
)
```

This allows different key names to be matched.

---

# 17. Alternate `pd.merge()` Syntax

Instead of:

```python
df1.merge(
    df2,
    on="EmployeeNumber",
    how="inner"
)
```

we can use:

```python
pd.merge(
    df1,
    df2,
    on="EmployeeNumber",
    how="inner"
)
```

Both approaches perform a merge.

For this project, understanding both forms is useful.

---

# 18. `DataFrame.join()`

`join()` is another method for combining DataFrames.

It is particularly convenient when the relationship is based on indexes.

Example:

```python
employee_indexed = employee_info.set_index("EmployeeNumber")

salary_indexed = salary_info.set_index("EmployeeNumber")
```

Then:

```python
employee_indexed.join(
    salary_indexed,
    how="inner"
)
```

Common options include:

```python
how="inner"
how="left"
how="right"
how="outer"
```

---

# 19. Merge vs Join vs Concat

| Feature                | `merge()`             | `join()`                | `concat()`            |
| ---------------------- | --------------------- | ----------------------- | --------------------- |
| Main purpose           | Match related records | Align DataFrames        | Stack/combine objects |
| Common key             | Yes                   | Usually index           | Not required          |
| Uses columns           | Yes                   | Mainly index            | Yes                   |
| Uses index             | Can                   | Yes                     | Yes                   |
| Inner/left/right/outer | Yes                   | Yes                     | Different mechanism   |
| Best for               | Relational data       | Index-based combination | Stacking datasets     |

### Mental Model

```text
MERGE
"Find matching keys."

JOIN
"Align based on index/key."

CONCAT
"Put datasets together."
```

---

# 20. HR Analytics Application

Our project uses:

```text
HR_Analytics.csv
```

We can split the dataset into two logical tables.

### Employee information

```python
employee_info = df[
    [
        "EmployeeNumber",
        "Age",
        "Gender",
        "Department",
        "JobRole"
    ]
].copy()
```

### Salary information

```python
salary_info = df[
    [
        "EmployeeNumber",
        "MonthlyIncome",
        "HourlyRate",
        "DailyRate"
    ]
].copy()
```

The common key is:

```text
EmployeeNumber
```

We can combine the two tables:

```python
merged_data = pd.merge(
    employee_info,
    salary_info,
    on="EmployeeNumber",
    how="inner"
)
```

---

# 21. Practical Business Questions

After learning merge and join operations, we can solve questions such as:

### 1. Which employees have salary information?

Use an inner join.

### 2. Do all employees have salary information?

Compare employee records with salary records using a left join.

### 3. Are there employees present in one dataset but missing from another?

Use an outer join and inspect missing values.

### 4. Can employee information and salary information be analyzed together?

Merge the datasets using `EmployeeNumber`.

### 5. Can monthly datasets be combined?

Use `pd.concat()`.

---

# 22. Common Mistakes

### Mistake 1: Choosing the wrong key

Always identify the column that actually connects the datasets.

```python
EmployeeNumber
```

is different from:

```python
Age
```

A key should represent the relationship between records.

### Mistake 2: Confusing concat and merge

Do not use `concat()` simply because two DataFrames need to be combined.

Ask:

```text
Do I need to STACK them?
        ↓
concat()

Do I need to MATCH records?
        ↓
merge()
```

### Mistake 3: Forgetting `how`

When performing a merge, explicitly understand whether you need:

```python
how="inner"
how="left"
how="right"
how="outer"
```

### Mistake 4: Ignoring unmatched records

Outer and left/right joins can produce `NaN`.

Missing values should be inspected rather than ignored.

---

# 23. Quick Reference

## Concatenate

```python
pd.concat([df1, df2])
```

Vertical:

```python
pd.concat([df1, df2], axis=0)
```

Horizontal:

```python
pd.concat([df1, df2], axis=1)
```

With source labels:

```python
pd.concat(
    [df1, df2],
    keys=["A", "B"]
)
```

## Merge

```python
pd.merge(
    df1,
    df2,
    on="key",
    how="inner"
)
```

Join types:

```python
how="inner"
how="left"
how="right"
how="outer"
```

Different key names:

```python
pd.merge(
    df1,
    df2,
    left_on="id",
    right_on="employee_id"
)
```

## Join

```python
df1.join(
    df2,
    how="inner"
)
```

---

# 24. Final Mental Model

```text
                 COMBINE DATA
                      │
          ┌───────────┴───────────┐
          │                       │
       STACK                    MATCH
          │                       │
       concat                  merge
                                  │
                         ┌────────┼────────┐
                         │        │        │
                       inner    left     outer
                                  │
                                right
```

### Remember

```text
concat → stack datasets
merge  → match datasets using keys
join   → combine using indexes/keys
```

---

# Day 5 Outcome

After this section, I should be able to:

* Explain why datasets need to be combined.
* Use `pd.concat()`.
* Concatenate rows.
* Concatenate columns.
* Understand MultiIndex concatenation.
* Use `pd.merge()`.
* Identify a common key.
* Perform inner joins.
* Perform left joins.
* Perform right joins.
* Perform outer joins.
* Understand `join()`.
* Choose between merge, join, and concat.
* Combine employee and salary information in the HR Analytics dataset.

## Key Takeaway

Real-world data is rarely perfectly organized in one table.

A strong data professional must know how to combine related datasets correctly.

```text
Learn → Identify the relationship → Choose the operation → Combine → Validate → Analyze
```

** Merging, Joining & Concatenating**
