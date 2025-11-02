# NumPy vs Pandas: Arrays and Data Handling
## 1. NumPy
- **Purpose:** Numerical computation, efficient array operations, linear algebra, math functions.
- **Core Object:** `ndarray`
- **Example:**
```python
import numpy as np
# Create a 1D array
arr = np.array([10, 20, 30, 40])
print(arr)
# Element-wise operation
arr_squared = arr ** 2
print(arr_squared)
```
- **Pros:** Fast, memory-efficient, supports vectorized math.
- **Cons:** No row/column labels, less convenient for heterogeneous data or missing values.
---
## 2. Pandas
- **Purpose:** Data manipulation, analysis, labeled data structures.
- **Core Objects:** `Series` (1D), `DataFrame` (2D)
- **Example:**
```python
import pandas as pd
# Create a Series (1D)
s = pd.Series([10, 20, 30, 40], index=['a', 'b', 'c', 'd'])
print(s)
# Create a DataFrame (2D)
df = pd.DataFrame({
    'Age': [25, 30, 35],
    'Salary': [50000, 60000, 70000]
})
print(df)
# Column-wise operation
df['Salary_k'] = df['Salary'] / 1000
print(df)
```
- **Pros:** Labels, indexing, missing value handling, groupby, joins, rich methods.
- **Cons:** Slightly slower than raw NumPy arrays for heavy numerical computation.
---
**Key Takeaways:**
1. Use **NumPy** for raw numerical arrays and computation.
2. Use **Pandas** when you need labeled, structured data with easy manipulation.
3. Pandas internally uses NumPy arrays, so they complement each other.
