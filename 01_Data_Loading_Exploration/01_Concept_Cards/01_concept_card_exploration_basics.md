# Concept Card 2: What Does df.shape Tell You?

## **The Core Idea**

`df.shape` tells you the **dimensions** of your data:
- `df.shape[0]` = number of **rows** (samples, observations, passengers)
- `df.shape[1]` = number of **columns** (features, attributes, variables)

This is your first reality check about data size and complexity.

---

## **Why df.shape Matters**

### **Rows (df.shape[0])**

```python
df.shape[0]  # 891 for Titanic
```

**Tells you:**
- How much data you have to work with
- Is it enough? (100 rows = small, 100K = large)
- Affects model complexity (more rows = can train bigger models)

**Example interpretation:**
- 891 rows = 891 passengers = manageable dataset
- 10 rows = tiny, might overfit
- 1M rows = huge, might need special tools

### **Columns (df.shape[1])**

```python
df.shape[1]  # 12 for Titanic
```

**Tells you:**
- How many features/attributes per sample
- Are there too many to understand? (100+ = high-dimensional)
- How much preprocessing needed? (more columns = more work)

**Example interpretation:**
- 12 columns = manageable, can explore all
- 100 columns = need automatic feature selection
- 1000 columns = definitely need dimensionality reduction

---

## **Why Both Together Matter**

```
Rows vs Columns = Samples vs Features

891 rows × 12 columns = 891 samples × 12 features
```

**This ratio matters:**
- **Many rows, few columns** (891 × 12): Good! You have examples to learn from.
- **Few rows, many columns** (100 × 1000): Bad! You'll overfit (memorize instead of learn).
- **Many rows, many columns** (100K × 500): Maybe OK, but computation gets heavy.

---

## **Related Checks: df.info() and df.describe()**

### **df.info() — Data Types and Missing Values**

```python
df.info()
```

**Tells you:**
- **Data types:** int64 (number), float64 (decimal), object (text)
- **Non-null count:** How many values are NOT missing
- **Memory usage:** How much RAM this dataframe uses

**Why it matters:**
- If Age is "object" instead of "float", something went wrong during loading
- If "Survived" has 880 non-nulls but shape says 891 rows, 11 values are missing!

### **df.describe() — Statistics for Numeric Columns**

```python
df.describe()
```

**Tells you:**
- **Count:** How many non-missing values (for each numeric column)
- **Mean, std:** Average and spread
- **Min, 25%, 50%, 75%, Max:** Distribution (quartiles)

**Why it matters:**
- If Age min = 0.42, that's suspicious (newborn on Titanic?)
- If Age max = 80, that's reasonable (elderly people exist)
- If Fare = 0 sometimes, that's an error (everyone paid something)

---

## **The Three-Part Diagnosis (What M1 Does)**

### **Part 1: Shape**
```python
df.shape  # (891, 12)
```
→ "I have 891 passengers and 12 attributes per passenger"

### **Part 2: Data Types**
```python
df.info()  # What types are each column?
```
→ "Age is float (good), Sex is object (need to convert), Survived is int (good)"

### **Part 3: Values**
```python
df.describe()  # Statistics for numeric columns
df.isnull().sum()  # Missing values
```
→ "Age ranges 0-80 (suspicious low end), Fare has 0 values (error?), Age has 177 missing"

---

## **In Your M1 Code**

You did:
```python
print(f"Data shape: {df.shape[0]} rows, {df.shape[1]} columns")
print(f"Missing values: {df.isnull().sum().sum()}")
print(df.dtypes)
print(df.describe())
```

**Why this is correct:**
1. ✓ You got the shape
2. ✓ You counted total missing values
3. ✓ You checked data types
4. ✓ You looked at statistics
→ You diagnosed the data BEFORE trying to clean it

---

## **Common Misinterpretations**

| Wrong | Right |
|-------|-------|
| "df.shape = 891" | df.shape = (891, 12); df.shape[0] = 891 |
| "891 columns in my data" | 891 ROWS; 12 columns |
| "describe() shows all columns" | describe() shows NUMERIC only; strings/dates hidden |
| "isnull().sum() = 180, so 180 missing total" | 180 missing ACROSS ALL COLUMNS; need per-column count |

---

## **Why This Comes Before Everything**

You can't:
- Clean data you don't understand (need shape + types first)
- Engineer features without knowing column names + types
- Train models on data with unknown problems

**df.shape is the first question you ask.** Like checking the patient's vital signs before operating.
