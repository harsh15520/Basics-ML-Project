# Concept Card 3: The 3-Part Data Diagnosis Framework

## **The Core Idea**

Data diagnosis means asking three specific questions in order:
1. **Problem 1: Are there missing values? (NaN/holes in data)**
2. **Problem 2: Are data types correct? (numeric vs text vs dates)**
3. **Problem 3: Are value ranges suspicious? (outliers/errors)**

This ORDER matters. You diagnose before you fix.

---

## **Problem 1: Missing Values**

### **What It Is**
Missing values are empty cells (NaN, null). In your data:
- Age: 177 missing values (out of 891 = 19.9%)
- Cabin: 687 missing values (out of 891 = 77.1%)
- Embarked: 2 missing values (out of 891 = 0.2%)

### **Why It Matters**
- **Models can't work with NaN.** They expect numbers, not empty.
- **Missing can be real.** (Passenger refused to say age) or **fake** (data entry error, lost during recording)
- **You must decide:** Fill it? Drop the row? Drop the column?

### **How to Diagnose**
```python
# Total missing values across all columns
df.isnull().sum().sum()  # 889

# Missing values per column
df.isnull().sum()
# Age         177
# Cabin       687
# Embarked      2
# ...others     0

# Percentage missing per column
(df.isnull().sum() / len(df)) * 100
# Age         19.9%
# Cabin       77.1%
# Embarked     0.2%
```

### **Decision Rule**
- **< 50% missing:** Fill it (impute with median/mode/forward-fill)
- **> 50% missing:** Drop it (too much information lost)
- **0-1% missing:** Fill it (almost complete)

For your data:
- Age (19.9%) → FILL
- Cabin (77.1%) → DROP
- Embarked (0.2%) → FILL

---

## **Problem 2: Data Types**

### **What It Is**
Data types are the kind of data each column holds:
- **int64, float64:** Numbers (integers or decimals)
- **object:** Text (strings)
- **datetime64:** Dates/times
- **bool:** True/False

### **Why It Matters**
- **Models expect consistent types.** Age should be numeric, not text "25".
- **Operations depend on type.** You can't add text: "25" + "30" = "2530" (wrong!) vs 25 + 30 = 55 (right).
- **Type mismatch = silent failure.** Code runs but gives weird results.

### **How to Diagnose**
```python
df.dtypes
# PassengerId       int64
# Survived          int64
# Pclass            int64
# Name             object  ← Text
# Sex              object  ← Text
# Age             float64  ← Numbers with decimals
# Fare            float64  ← Numbers with decimals
# Embarked         object  ← Text
```

### **What to Check**
- Numeric columns (Age, Fare, Pclass) should be int64 or float64
- Categorical columns (Sex, Embarked, Name) should be object
- If Age is "object" but should be numeric → loading/parsing error

### **Common Type Problems**
| Problem | Symptom | Example |
|---------|---------|---------|
| Numbers loaded as text | Age is "object" not "float" | "25" instead of 25 |
| Dates loaded as text | Date is "object" not "datetime" | "2020-01-01" as string |
| Mixed types in column | Some numeric, some text | Age has [25, "unknown", 30] |

---

## **Problem 3: Suspicious Value Ranges**

### **What It Is**
Ranges are min/max values in numeric columns. Suspicious means:
- **Outliers:** Values that seem too extreme (Age=0.42, Age=80)
- **Errors:** Values that don't make logical sense (Fare=0, Age=-5)
- **Clues:** Values hinting at data quality issues

### **Why It Matters**
- **Outliers affect models.** One extreme value can bias the entire model.
- **Errors are real problems.** Fare=0 means "no ticket purchased" (impossible for Titanic).
- **Ranges tell you about data.** Age 0.42 = baby (rare but real), Age=80 = elderly (real).

### **How to Diagnose**
```python
df.describe()
# Shows for numeric columns:
#            Age        Fare
# count    714.000   891.000
# mean      29.700    32.254
# std       14.526    49.693
# min        0.420     0.000   ← MIN: suspicious!
# 25%       20.125     7.854
# 50%       28.000    14.454
# 75%       38.000    31.275
# max       80.000   512.329   ← MAX: reasonable but note it
```

**What to check:**
- Min/Max: Are they reasonable?
- Mean vs Median: If very different, there are outliers
- Std: Large std = wide spread (maybe outliers)

### **Decision Rule**
- **Age = 0.42:** Suspicious (newborn on ship?) but real (keep it)
- **Fare = 0:** Impossible (everyone paid) → drop those rows
- **Age = 80:** Extreme but possible → keep it
- **Age = 150:** Impossible → delete that row

---

## **Why This Order?**

```
1. Find missing values  →  Know which columns are broken
2. Check types         →  Know if data is the right format
3. Check ranges        →  Know if specific values are errors

Without this order:
- If you check ranges first, you might miss that Age is text, not numbers
- If you check types first, you won't know Age has 177 holes
- If you fix missing before checking ranges, you might fill with outlier values
```

---

## **Complete Diagnosis Framework**

```python
# Step 1: Missing values
print("Missing values per column:")
print(df.isnull().sum())
print(f"Total missing: {df.isnull().sum().sum()}")

# Step 2: Data types
print("\nData types:")
print(df.dtypes)

# Step 3: Value ranges (numeric only)
print("\nStatistics:")
print(df.describe())

# Bonus: Categorical distribution
print("\nCategorical values:")
print(df['Sex'].value_counts())
print(df['Embarked'].value_counts())
```

---

## **In Your M1 Code**

You did exactly this:
```python
# Step 1: Missing
missing = df.isnull().sum()
print(missing[missing > 0])

# Step 2: Types
print(df.dtypes)

# Step 3: Ranges
print(f"Age: min={df['Age'].min()}, max={df['Age'].max()}")
print(f"Fare: min={df['Fare'].min()}, max={df['Fare'].max()}")
```

✓ You diagnosed in the right order.  
✓ You found real problems (Age has 177 missing, Fare has 0 values).  
✓ This diagnosis drove M2's cleaning strategy.

---

## **Why This Framework Matters**

**It's the SAME for any dataset, any problem.**

You'll use this framework 100 times in your career:
- New client data?  → Run the 3-part diagnosis
- New Kaggle competition? → Run the 3-part diagnosis
- Real production data? → Run the 3-part diagnosis

It never changes. Missing → Types → Ranges. Always.
