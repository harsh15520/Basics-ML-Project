# Modification Exercise 2: Compare df.describe() vs Multi-Method Diagnosis

## **The Challenge**

You diagnosed data using THREE separate tools:
1. `df.isnull().sum()` — missing values
2. `df.describe()` — statistics (numeric columns only)
3. `df.dtypes` — data types
4. `df.info()` — columns, types, non-null counts

**Challenge:** What if you used ONLY `df.describe()`?  
Can you diagnose problems as effectively?  
What would you MISS?

---

## **Step 1: Run Both Approaches**

### **Approach A: Your Current M1 Method (3 tools)**

```python
# Tool 1: Missing values
print("Missing values:")
print(df.isnull().sum())

# Tool 2: Describe
print("\nStatistics:")
print(df.describe())

# Tool 3: Data types
print("\nData types:")
print(df.dtypes)
```

### **Approach B: describe() Only**

```python
# Just describe
print(df.describe())
```

---

## **Step 2: Compare Outputs**

### **What You Get From `df.describe()`:**

```
            PassengerId    Survived      Pclass        Age        SibSp  \
count     891.000000     891.000000  891.000000  714.000000  891.000000   
mean     446.000000       0.383838    2.308642   29.699118    0.523008   
std      257.905412       0.486592    0.836071   14.526497    1.102743   
min        1.000000       0.000000    1.000000    0.420000    0.000000   
25%      223.500000       0.000000    2.000000   20.125000    0.000000   
50%      446.000000       0.000000    3.000000   28.000000    0.000000   
75%      668.500000       1.000000    3.000000   38.000000    1.000000   
max      891.000000       1.000000    3.000000   80.000000    8.000000   
```

**What you see:**
- Mean, std, min, max for each NUMERIC column
- 25th/50th/75th percentiles (quartiles)
- Count: How many non-missing values per column

**What you DON'T see:**
- Categorical columns (Sex, Embarked, Name, Ticket, Cabin) are NOT shown
- Exact number of missing values (only implicit in "count")
- Data types (you don't know which are numeric vs text)

---

## **Step 3: Answer These Questions**

**Q1: From describe() output ALONE, can you find Age is missing 177 values?**

A: Sort of, but not clearly.
- Age count = 714
- Total rows = 891 (from PassengerId count)
- 891 - 714 = 177 missing
- **You CAN figure it out, but it's indirect and error-prone**

**Q2: From describe() output ALONE, can you see that Sex is categorical text?**

A: NO. Sex column doesn't appear in describe() at all.
- describe() ignores non-numeric columns
- **You would have NO IDEA Sex even exists**

**Q3: From describe() output ALONE, can you see that Cabin has 687 missing?**

A: NO. Cabin doesn't appear because it's text ("C85", "NaN", etc).
- You can't count missing values in a column you can't see
- **You would miss one of the biggest problems**

**Q4: Why does describe() only show 714 for Age but not show HOW MANY ARE MISSING?**

A: Because describe() is designed for NUMERIC data only.
- It shows "count" = non-missing count
- It assumes: If you want to know missing, you'll check separately
- **describe() alone is incomplete without companion tools**

---

## **Step 4: Build a Comparison Table**

Fill this out:

| Question | Using describe() Only | Using 3-Tool Method | Winner |
|----------|----------------------|-------------------|--------|
| Is Age missing data? | Count=714, but hard to tell | df.isnull().sum() → 177 missing | 3-Tool |
| Is Sex numeric or text? | Can't see Sex column | df.dtypes → object | 3-Tool |
| What's Cabin's data type? | Can't see Cabin column | df.dtypes → object, df.info() shows 204 non-null | 3-Tool |
| What's Age's min value? | 0.42 (suspicious) | df.describe() + df.isnull().sum() (suspicious + missing) | 3-Tool |
| How many total missing values? | 891 - 714 (error-prone) | df.isnull().sum().sum() → 889 | 3-Tool |

---

## **Step 5: What Does Each Tool Do BEST?**

### **df.describe() is best for:**
- ✓ Quick statistics on numeric data
- ✓ Seeing ranges (min/max/mean/std)
- ✓ Understanding distributions (25%/50%/75%)

### **df.dtypes is best for:**
- ✓ Knowing data types (numeric vs text vs date)
- ✓ Catching type mismatches (Age should be float, not object)

### **df.isnull().sum() is best for:**
- ✓ Finding exactly which columns have missing values
- ✓ Counting how many missing (per column and total)
- ✓ Calculating percentage missing

### **df.info() is best for:**
- ✓ Seeing all columns at once (including non-numeric)
- ✓ Checking data types and non-null counts together
- ✓ Memory usage

---

## **Step 6: The Real-World Lesson**

### **If You ONLY Used describe():**

You would diagnose:
- ✓ Age ranges 0.42 to 80 (suspicious low end)
- ✓ Fare ranges 0 to 512 (ranges vary widely)
- ✗ MISS: Age has 177 missing values (critical!)
- ✗ MISS: Sex/Embarked/Cabin are categorical (can't use directly)
- ✗ MISS: Cabin is 77% missing (critical!)
- ✗ MISS: Multiple data types need handling

**Result:** You'd try to train a model, it would CRASH when hitting text columns and NaN values.

### **If You Use 3-Tool Method:**

You diagnose:
- ✓ Age ranges 0.42-80 AND has 177 missing
- ✓ Sex/Embarked are categorical (need encoding)
- ✓ Cabin is 77% missing (must drop)
- ✓ All data types are known
- ✓ All problems are found

**Result:** You proceed to M2 with complete understanding. M2 fixes everything. M3 works perfectly.

---

## **Step 7: Why Data Scientists Use ALL Tools**

Real-world ML teams don't just run describe(). They run:

```python
# The standard diagnostic check (used everywhere)
print(df.shape)
print(df.dtypes)
print(df.info())
print(df.describe())
print(df.isnull().sum())
print(df.head(10))
print(df.tail(10))
```

**Why?** Because each tool reveals different aspects:
- `shape` → Size
- `dtypes` → Format
- `info()` → Types + Non-null counts
- `describe()` → Ranges & distributions
- `isnull().sum()` → Missing values
- `head()` → Sample of data
- `tail()` → End of data (catches issues at boundaries)

**Miss ONE tool → Miss REAL problems → Model fails in production.**

---

## **Your Deliverable**

After this exercise, you should understand:

✓ describe() is useful BUT INCOMPLETE  
✓ You need multiple diagnostic tools  
✓ Each tool shows different aspects  
✓ Together they prevent blind spots  
✓ This is standard practice in ML  

**For your interview:**

Recruiter asks: "Walk me through how you explore a new dataset"

**Bad answer:** "I run describe() and look at the numbers"  
**Good answer:** "I use a multi-part diagnostic: shape, dtypes, info, describe, isnull count, plus a sample. Each reveals different issues. For example, describe() wouldn't show that Cabin is 77% missing, but isnull().sum() does. I need both."

**This shows:** You know tools' strengths AND limitations.
