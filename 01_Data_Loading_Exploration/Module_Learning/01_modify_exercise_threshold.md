# Modification Exercise 1: Change the "Problem Detection" Threshold

## **The Challenge**

In your M1 code, you find columns with missing values using:

```python
missing = df.isnull().sum()
print(missing[missing > 0])  # Find columns with ANY missing (> 0)
```

This finds ALL columns with missing values, no matter how many (strict).

**Your task:** Change the threshold to find only MAJOR problems.

---

## **Step 1: Understand Current Behavior**

Current code finds columns with **more than 0 missing values**:

```python
missing[missing > 0]
# Output:
# Age         177  ← Found (>0)
# Cabin       687  ← Found (>0)
# Embarked      2  ← Found (>0)
```

**All 3 columns are detected** because all have at least 1 missing value.

---

## **Step 2: Modify the Threshold**

**Task:** Change the threshold from `> 0` to `> 50`

```python
missing = df.isnull().sum()
print(missing[missing > 50])  # Find columns with MORE than 50 missing
```

**Run this and observe:**

What columns appear?  
What columns disappear?  
Why?

---

## **Step 3: Interpret the Results**

After you change to `> 50`, you should see:

```python
missing[missing > 50]
# Output:
# Age       177   ← Still found (177 > 50)
# Cabin     687   ← Still found (687 > 50)
# Embarked    2   ← GONE (2 is NOT > 50)
```

**What changed:**
- Age & Cabin still flagged (they have many missing)
- Embarked disappeared (only 2 missing, not significant)

---

## **Step 4: Answer These Questions**

1. **What threshold value would show ONLY Cabin?**
   - (Hint: Cabin=687, Age=177. What number between 177 and 687?)

2. **What threshold would show NOTHING?**
   - (Hint: What's bigger than 687?)

3. **Why would a recruiter care about this threshold?**
   - (Hint: Think about data quality decisions)

---

## **Step 5: Real-World Thinking**

Different thresholds → Different cleaning strategies:

| Threshold | What It Means | Strategy |
|-----------|--------------|----------|
| > 0 | Find ALL problems (strict) | Handle every missing value |
| > 50 | Find MAJOR problems only | Ignore minor missing values |
| > 500 | Find CRITICAL problems only | Only address severe gaps |

**Real example:**
- **Medical data:** You can't ignore even 1 missing patient value (> 0 threshold)
- **Customer survey data:** You ignore if < 5% missing (> 50 threshold for 1000 rows)
- **Log data with millions of rows:** You ignore if < 1% missing (> 10000 threshold)

---

## **Step 6: Think About Your Data**

For Titanic with 891 rows:

- **Age (177 missing = 19.9%):** Major problem? YES, almost 1 in 5 is missing
- **Cabin (687 missing = 77.1%):** Critical problem? YES, 3 in 4 are missing
- **Embarked (2 missing = 0.2%):** Problem? Maybe not (almost everyone has it)

**With threshold > 50:**
- You're saying: "Only fix columns with > 50 missing values"
- You're ignoring: Embarked's 2 missing values
- You're accepting: "2 missing out of 891 is acceptable"

**Is that reasonable?**
- For Embarked: YES (0.2% missing is minor)
- For Age: NO (19.9% is major, needs fixing)
- For Cabin: CRITICAL (77% is too much, must drop)

---

## **Step 7: Write Your Own Rule**

Based on your data, define a threshold that makes sense:

```python
# My missing value thresholds:
THRESHOLD_FILL = 50      # Fill if less than 50 missing
THRESHOLD_DROP = 200     # Drop if more than 200 missing

# Then apply:
missing = df.isnull().sum()
columns_to_fill = missing[(missing > 0) & (missing < THRESHOLD_FILL)]
columns_to_drop = missing[missing > THRESHOLD_DROP]
columns_to_investigate = missing[(missing >= THRESHOLD_FILL) & (missing <= THRESHOLD_DROP)]

print("FILL:", columns_to_fill)
print("DROP:", columns_to_drop)
print("INVESTIGATE:", columns_to_investigate)
```

---

## **Why This Matters**

### **For Your Learning:**
- You see that data science isn't one-size-fits-all
- Thresholds are CHOICES, not rules
- Different data → Different thresholds
- You're learning to make principled decisions

### **For Your Interview:**
Recruiter asks: "How would you handle missing values?"

**Bad answer:** "Use mode or median"  
**Good answer:** "Depends on percentage missing. If < 5%, fill with median (numeric) or mode (categorical). If > 50%, drop the column. Between 5-50%, investigate domain meaning."

**Better answer:** "I'd define thresholds based on data quality requirements, then apply them systematically, like in this exercise."

---

## **Your Deliverable**

After completing this exercise, you should:

✓ Understand that `missing[missing > 0]` is ONE choice  
✓ Know HOW to change the threshold  
✓ Understand WHY different thresholds matter  
✓ Be able to explain your choice to a recruiter  

**Save this exercise:** When you interview, show them:
- Your original M1 threshold (> 0)
- Your modified threshold (> 50)
- Your reasoning for the choice

This shows: **Thinking > Copying Code**
