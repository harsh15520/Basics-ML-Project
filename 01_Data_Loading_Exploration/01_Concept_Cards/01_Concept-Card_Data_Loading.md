# Concept Card 1: Why Load Data Properly?

## **The Core Idea**

Loading data means reading it from disk (CSV file) into memory (Python dataframe). This seems simple, but it's critical because:
- **Garbage in → garbage out.** If you load data wrong, everything downstream breaks.
- **You only get one chance.** Load it incorrectly and you might not notice until the model fails.

---

## **What Happens When You Load Data**

```python
df = pd.read_csv('train.csv')
```

This single line does A LOT:
1. **Reads the file** from disk
2. **Interprets encoding** (UTF-8, Latin1, etc.)
3. **Parses columns** (detects headers)
4. **Infers data types** (integer, float, text, etc.)
5. **Creates a dataframe** (rows × columns table in memory)

If ANY of these steps go wrong:
- Wrong encoding → special characters become gibberish
- No headers detected → first row treated as data
- Wrong types inferred → numbers become text
- → Model fails later when it expects numbers, gets text

---

## **Why This Matters for ML**

**Data loading is where you establish trust:**
- Did I get the right dataset?
- How many rows/columns? (expected vs actual)
- Are values reasonable? (Age 0-80 or Age 0-800?)
- Is the target column present? (Survived column there?)

**If you load data wrong:**
- You train on the wrong thing
- Models learn wrong patterns
- Results are meaningless

---

## **The 3-Step Verification After Loading**

### **Step 1: Shape Check**
```python
print(df.shape)  # Should output (891, 12) for Titanic
```
**What to check:** 
- Row count: Is it what you expected?
- Column count: Is it what you expected?
- If different: Did you download the right file?

### **Step 2: Data Types Check**
```python
print(df.dtypes)
```
**What to check:**
- Numeric columns (int64, float64) are numeric
- Text columns (object) are text
- If Age is "object", something went wrong (should be float)

### **Step 3: Content Spot Check**
```python
print(df.head())
```
**What to check:**
- Do names look like real passenger names?
- Do ages look reasonable (not 0, not 999)?
- Is the Survived column 0/1 (not "yes"/"no")?
- If anything looks weird, investigate before proceeding

---

## **Common Pitfalls (What Can Go Wrong)**

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Wrong encoding | Special characters as `\xc3\xa9` | `pd.read_csv(..., encoding='latin1')` |
| No headers | First row treated as data | `pd.read_csv(..., header=0)` |
| Wrong delimiter | All data in one column | `pd.read_csv(..., sep=';')` |
| Type inference | Ages loaded as text "25" | Manual type conversion or reload |
| Wrong file | Data doesn't match expectations | Verify file path + size |

---

## **In Your M1 Code**

You did this:
```python
df = pd.read_csv('train.csv')
print(f"Original data: {df.shape[0]} rows, {df.shape[1]} columns")
print(df.isnull().sum().sum())  # Total missing values
```

**This is correct because:**
1. ✓ You loaded the file
2. ✓ You checked shape (891 rows, 12 columns)
3. ✓ You counted missing values immediately
4. → You established that the data loaded properly before proceeding

---

## **Why This Is Step 1 of ML**

Before you clean data (M2), you must load it correctly.  
Before you train models (M3), you must know what you loaded.  
Before you compare models (M4), you must trust your data source.

**Loading is unglamorous but essential.** Like checking your brakes before driving.
