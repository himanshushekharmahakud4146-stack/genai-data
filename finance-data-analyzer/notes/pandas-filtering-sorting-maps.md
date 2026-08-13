# Pandas Summary Functions, Maps, Filtering & Sorting

##  Month 5: Pandas Fundamentals

### Overview

focused on using Pandas to summarize data, transform values, apply custom logic, filter records, and sort results.

The main practice was completed using the Kaggle Pandas **Summary Functions and Maps** exercise and reinforced with HR Analytics data.

### Topics Covered

* Summary functions
* `median()`
* `mean()`
* `unique()`
* `value_counts()`
* `idxmax()`
* Arithmetic operations on Series
* Mean-centering
* `.map()`
* Lambda functions
* `.apply()`
* Row-wise operations with `axis="columns"`
* Boolean filtering
* Multiple conditions
* `.sort_values()`
* Business-oriented data filtering

---

# 1. Summary Functions

Summary functions reduce or describe data in a Series.

They are useful for quickly understanding numerical and categorical columns.

---

## 2. `median()`

The median is the middle value of an ordered dataset.

Example:

```python
reviews.points.median()
```

The Kaggle exercise calculated the median of the `points` column.

Result:

```text
88.0
```

### Use case

Median is useful when the data contains extreme values because it is less affected by outliers than the mean.

---

# 3. `mean()`

The mean is calculated by adding all values and dividing by the number of values.

Example:

```python
reviews.price.mean()
```

Mean can be used for numerical analysis and transformations.

---

# 4. `unique()`

`unique()` returns the distinct values present in a Series.

Example:

```python
reviews.country.unique()
```

This identifies the different countries represented in the dataset.

### Important

`unique()` returns the actual unique values.

It does not return how many times each value occurs.

For frequencies, use:

```python
reviews.country.value_counts()
```

---

# 5. `value_counts()`

`value_counts()` counts how frequently each unique value appears.

Example:

```python
reviews.country.value_counts()
```

This creates a Series containing:

```text
value → frequency
```

For example:

```text
US       54504
France   22093
...
```

### Use case

`value_counts()` is useful for understanding the distribution of categorical data.

Examples:

* Number of employees by department
* Number of customers by country
* Number of transactions by category

---

# 6. Mean-Centering a Column

Mean-centering means subtracting the mean from every value.

The Kaggle exercise used:

```python
centered_price = reviews.price - reviews.price.mean()
```

Conceptually:

```text
centered value = original value - mean
```

If a value is above the mean, the result is positive.

If a value is below the mean, the result is negative.

If a value is equal to the mean, the result is approximately zero.

### Why is this useful?

Mean-centering is a common preprocessing operation and can be useful before applying certain machine learning algorithms.

---

# 7. Finding the Maximum with `idxmax()`

`idxmax()` returns the index label where the maximum value occurs.

The Kaggle exercise calculated the best points-to-price ratio:

```python
i = (reviews.points / reviews.price).idxmax()
bargain_wine = reviews.loc[i, "title"]
```

The logic is:

```text
points ÷ price
      ↓
find maximum ratio
      ↓
get its index
      ↓
use .loc[] to retrieve the wine title
```

Result:

```text
Bandit NV Merlot (California)
```

### Important distinction

```python
.max()
```

returns the maximum value.

```python
.idxmax()
```

returns the index where the maximum value occurs.

---

# 8. `.map()`

`.map()` applies a transformation to each value in a Series.

Basic structure:

```python
series.map(function)
```

Example:

```python
reviews.description.map(
    lambda d: "tropical" in d
)
```

This checks whether the word `"tropical"` appears in each description.

---

# 9. Lambda Functions

A lambda is a small anonymous function.

Example:

```python
lambda x: x > 10
```

This means:

```text
Take x
↓
Check whether x is greater than 10
↓
Return True or False
```

The Kaggle exercise used lambda functions to count occurrences of words in wine descriptions.

Example:

```python
a = reviews.description.map(
    lambda d: "tropical" in d
).sum()
```

Because `True` behaves like `1` and `False` behaves like `0` in this context, summing the Boolean values counts how many descriptions contain the word.

---

# 10. Counting Values with `.map()`

The Kaggle exercise counted the words:

```text
tropical
fruity
```

using:

```python
a = reviews.description.map(
    lambda d: "tropical" in d
).sum()

b = reviews.description.map(
    lambda d: "fruity" in d
).sum()
```

The resulting counts were:

```text
tropical → 3607
fruity   → 9090
```

This demonstrates how `.map()` can be combined with Boolean logic and aggregation.

---

# 11. `.apply()`

`.apply()` allows a function to be applied to Pandas data.

For a Series:

```python
series.apply(function)
```

For a DataFrame, `axis` controls whether the function works across rows or columns.

Example:

```python
df.apply(function, axis="columns")
```

---

# 12. Row-Wise `.apply()`

The Kaggle exercise created a custom function for converting wine review scores into star ratings.

The function used information from an entire row:

```python
def reviews_stars(r):
    if r.country == "Canada":
        return 3
    elif r.points >= 95:
        return 3
    elif r.points >= 85:
        return 2
    else:
        return 1
```

Then:

```python
star_ratings = reviews.apply(
    reviews_stars,
    axis="columns"
)
```

### Why `axis="columns"`?

The function needs to access multiple columns from the same row:

```text
country
points
```

Therefore, the function is applied row by row.

---

# 13. `.map()` vs `.apply()`

### `.map()`

Usually used with a Series to transform each individual value.

```python
df["column"].map(function)
```

Think:

```text
One Series
+
Value-by-value transformation
```

### `.apply()`

Can be used with a Series or DataFrame and is more flexible.

For row-wise DataFrame operations:

```python
df.apply(function, axis="columns")
```

Think:

```text
Custom function
+
More flexible transformation
```

### Easy mental model

```text
map   → transform Series values
apply → apply a function more generally
```

---

# 14. Boolean Filtering

Boolean expressions produce `True` or `False`.

Example:

```python
hr["Age"] > 30
```

The result is a Boolean Series.

This Boolean Series can then be used to filter the DataFrame:

```python
hr.loc[
    hr["Age"] > 30
]
```

---

# 15. Multiple Conditions

Use `&` for AND.

```python
hr.loc[
    (hr["Age"] > 30) &
    (hr["MonthlyIncome"] < 40000)
]
```

Meaning:

```text
Age > 30
AND
MonthlyIncome < 40000
```

Use `|` for OR.

```python
hr.loc[
    (hr["Department"] == "Sales") |
    (hr["Department"] == "Human Resources")
]
```

Use `~` for NOT.

```python
hr.loc[
    ~(hr["Department"] == "Sales")
]
```

### Important

Each condition should normally be wrapped in parentheses.

Use:

```python
(condition1) & (condition2)
```

not:

```python
condition1 and condition2
```

---

# 16. Sorting with `sort_values()`

`sort_values()` sorts a DataFrame or Series according to one or more columns.

Example:

```python
hr.sort_values("MonthlyIncome")
```

Default:

```python
ascending=True
```

For descending order:

```python
hr.sort_values(
    "MonthlyIncome",
    ascending=False
)
```

### Ascending

```text
Low → High
```

### Descending

```text
High → Low
```

---

# 17. Filtering and Sorting Together

Filtering and sorting can be combined.

Example:

```python
hr.loc[
    hr["Department"] == "Sales"
].sort_values(
    "MonthlyIncome",
    ascending=False
)
```

This means:

```text
Select Sales employees
        ↓
Sort by MonthlyIncome
        ↓
Highest income first
```

This pattern is useful for answering business questions.

---

# 18. Sorting by Multiple Columns

Multiple columns can be supplied as a list.

```python
hr.sort_values(
    ["Department", "MonthlyIncome"],
    ascending=[True, False]
)
```

This means:

1. Sort departments in ascending order.
2. Within each department, sort monthly income in descending order.

---

# 19. Business-Oriented HR Filtering

Pandas becomes more useful when technical operations are connected to business questions.

Examples:

### Query 1: Older employees with lower income

```python
hr.loc[
    (hr["Age"] > 30) &
    (hr["MonthlyIncome"] < 40000),
    [
        "EmployeeNumber",
        "Age",
        "MonthlyIncome",
        "Department"
    ]
]
```

### Query 2: Long-tenure and higher-income employees

```python
hr.loc[
    (hr["YearsAtCompany"] > 5) &
    (hr["MonthlyIncome"] > 50000),
    [
        "EmployeeNumber",
        "YearsAtCompany",
        "MonthlyIncome",
        "JobRole"
    ]
]
```

### Query 3: Sales or Research & Development

```python
hr.loc[
    hr["Department"].isin(
        ["Sales", "Research & Development"]
    ),
    [
        "EmployeeNumber",
        "Department",
        "JobRole"
    ]
]
```

### Query 4: Frequent travelers with long tenure

```python
hr.loc[
    (hr["BusinessTravel"] == "Travel_Frequently") &
    (hr["YearsAtCompany"] > 5),
    [
        "EmployeeNumber",
        "BusinessTravel",
        "YearsAtCompany",
        "Department"
    ]
]
```

### Query 5: Older employees with lower income, sorted

```python
hr.loc[
    (hr["Age"] > 40) &
    (hr["MonthlyIncome"] < 30000),
    [
        "EmployeeNumber",
        "Age",
        "MonthlyIncome",
        "Department"
    ]
].sort_values(
    "MonthlyIncome",
    ascending=True
)
```

---

# 20. Common Mistakes

### Mistake 1: Using `and` instead of `&`

Incorrect:

```python
df[(df["Age"] > 30) and (df["Salary"] > 40000)]
```

Correct:

```python
df[
    (df["Age"] > 30) &
    (df["Salary"] > 40000)
]
```

---

### Mistake 2: Forgetting parentheses

Correct:

```python
(df["Age"] > 30) & (df["Salary"] > 40000)
```

---

### Mistake 3: Confusing `.max()` and `.idxmax()`

```python
series.max()
```

returns the maximum value.

```python
series.idxmax()
```

returns the index of the maximum value.

---

### Mistake 4: Confusing `unique()` and `value_counts()`

```python
df["country"].unique()
```

returns unique values.

```python
df["country"].value_counts()
```

returns the frequency of each value.

---

### Mistake 5: Forgetting `axis="columns"`

When a DataFrame function needs information from multiple columns in each row:

```python
df.apply(function, axis="columns")
```

is appropriate.

---

# 21. Day 3 Key Takeaways

The most important concepts from today are:

```text
median()
    → middle value

mean()
    → average value

unique()
    → distinct values

value_counts()
    → frequency of values

idxmax()
    → index of maximum value

.map()
    → value-by-value transformation

.apply()
    → apply a custom function

& 
    → AND

|
    → OR

~
    → NOT

sort_values()
    → sort data

.loc[condition]
    → filter rows
```

---

# 22. Practical Data Workflow

The concepts learned today fit into a practical data workflow:

```text
Load Data
    ↓
Understand Data
    ↓
Summarize Data
    ↓
Filter Data
    ↓
Transform Data
    ↓
Sort Data
    ↓
Answer Business Questions
```

This is an important foundation for later data cleaning, analysis, machine learning, and GenAI applications.

---

# Day 3 Completion

### Kaggle Practice

* [x] Median calculation
* [x] Unique values
* [x] Value counts
* [x] Mean-centering
* [x] Maximum index with `idxmax()`
* [x] `.map()`
* [x] Lambda functions
* [x] `.apply()`
* [x] Row-wise `apply()`
* [x] Completed all 7 Kaggle exercises

### Pandas Practice

* [x] Boolean filtering
* [x] `&`
* [x] `|`
* [x] `~`
* [x] `.sort_values()`
* [x] Multiple-column sorting
* [x] Business-oriented filtering

## Day 3 Objective

> Use Pandas to summarize, filter, transform, sort, and interpret real-world data while translating business questions into data operations.
