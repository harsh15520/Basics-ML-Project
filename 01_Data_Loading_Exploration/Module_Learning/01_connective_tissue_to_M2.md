# Connective Tissue: Why M1 Feeds Into M2

## **The Problem: Why M2 Exists Because of M1**

Module 1 found **problems**. Module 2 **solves** those problems.

### **M1 Diagnosed:**

```
Age:      177 missing values (19.9% of dataset)
Cabin:    687 missing values (77.1% of dataset)  
Embarked: 2 missing values (0.2% of dataset)
Fare:     Some rows have Fare=0 (impossible value)
Sex:      Categorical (text: "male"/"female")
Embarked: Categorical (text: "S"/"C"/"Q")
```

---

## **The Solution: What M2 Does With M1's Findings**

### **Finding 1: Age has 177 missing values**

**M2's Decision:** FILL with MEDIAN

```python
age_median = df['Age'].median()  # 28.0
df['Age'].fillna(age_median, inplace=True)
```

**Why MEDIAN (not MEAN)?**
- Age is numeric (numbers, not categories)
- Median is the middle value: sort ages, take the middle one
- Robust to outliers: one extreme age doesn't shift it much
- Mean would be pulled by outliers (Age=80 or Age=0.42)

**Result:** All 177 missing Ages now filled with 28. No holes in Age column.

---

### **Finding 2: Embarked has 2 missing values**

**M2's Decision:** FILL with MODE

```python
embarked_mode = df['Embarked'].mode()[0]  # 'S' (most common)
df['Embarked'].fillna(embarked_mode, inplace=True)
```

**Why MODE (not MEDIAN)?**
- Embarked is categorical (text categories, not numbers)
- No "middle" value for categories
- MODE = most common value = safest guess
- Distribution: S=644, C=168, Q=77 → S is overwhelmingly common

**Result:** All 2 missing Embarked values filled with 'S'. No holes in Embarked column.

---

### **Finding 3: Cabin has 687 missing values (77%!)**

**M2's Decision:** DROP the entire column

```python
df = df.drop('Cabin', axis=1)
```

**Why DROP (not FILL)?**
- 77% missing = you'd be making up 3 out of 4 values
- If you fill with mode/median, 77% of Cabin column is guessed
- Information lost > information gained
- Rule: > 50% missing → drop

**Result:** Cabin column removed entirely. Less data, but more trustworthy.

---

### **Finding 4: Some Fare values are 0**

**M2's Decision:** REMOVE those rows

```python
df = df[df['Fare'] > 0]  # Keep only rows where Fare > 0
```

**Why DELETE ROWS (not FILL)?**
- Fare=0 doesn't make sense (everyone on Titanic bought a ticket)
- This is a DATA ERROR, not a missing value
- Can't "fix" an error, must remove it
- Only 15 rows affected (out of 876)

**Result:** 15 rows removed. Data cleaner, more trustworthy.

---

### **Finding 5: Sex is text ("male"/"female")**

**M2's Decision:** ENCODE to numeric (1/0)

```python
df['Sex_encoded'] = (df['Sex'] == 'Male').astype(int)
# Male → 1, Female → 0
```

**Why ENCODE?**
- Models need numbers, not text
- Can't add: "male" + "female" = ??? 
- But can add: 1 + 0 = meaningful comparison
- One-way encoding: Sex_encoded tells model if passenger was male

**Result:** New column Sex_encoded. Models can now use it.

---

### **Finding 6: Embarked is categorical text**

**M2's Decision:** ONE-HOT ENCODE

```python
embarked_dummies = pd.get_dummies(df['Embarked'], prefix='Embarked')
# Creates 3 columns: Embarked_C, Embarked_Q, Embarked_S
```

**Why ONE-HOT (not simple encoding)?**
- Simple encoding: S=1, C=2, Q=3 → Model thinks Q is "3x better" than S (WRONG!)
- One-hot: Each port gets its own binary column (1=yes, 0=no)
- Prevents fake ordering of categories

**Result:** 1 Embarked column becomes 3 binary columns. No false hierarchy.

---

## **The Chain: M1 → M2 → M3**

```
M1: DIAGNOSIS
Input:  Raw Titanic data (891 rows, 12 columns, messy)
Output: Problem list
        - Missing: Age(177), Cabin(687), Embarked(2)
        - Types: Sex/Embarked are text
        - Errors: Fare=0
        
        ↓ (feeds problem list into M2)

M2: CLEANING & ENGINEERING
Input:  Problem list from M1 + Raw data
Output: Clean data
        - No missing values (filled or dropped)
        - All numeric (text encoded)
        - No errors (Fare=0 rows removed)
        - New features engineered (FamilySize, AgeGroup, Title)
        - All scaled (ranges normalized)
        
        ↓ (feeds clean data into M3)

M3: BASELINE MODEL
Input:  Clean data from M2
Output: Logistic Regression
        - 81.6% accuracy on test set
        - Identifies important features
        - Provides BASELINE to compare against
```

---

## **Why WITHOUT M1, M2 Would Fail**

If you skipped M1 and went straight to M2:
- You wouldn't know Age has 177 holes → might use wrong fill strategy
- You wouldn't know Cabin is 77% missing → might waste time trying to fill it
- You wouldn't know Fare=0 exists → models would train on bad data
- You wouldn't know Sex is text → wouldn't encode it

**Result:** M2 makes random guesses. Data stays dirty. M3 fails.

---

## **Why WITHOUT M2, M3 Would Fail**

If you tried to train M3 model on M1's raw data:
```python
# Trying to train Logistic Regression on raw data
model.fit(X_train, y_train)
# ERROR: "Input X contains NaN"
# Can't train with missing values
```

Even if you got past NaN:
```python
# If you used text columns directly
X = df[['Sex', 'Embarked', 'Age']]
model.fit(X_train, y_train)
# ERROR: "Could not convert string to float"
# Models don't understand text
```

**Result:** M3 can't train. M2 is essential.

---

## **The Lesson: Data Prep Is 80% of ML**

```
Time breakdown in real ML projects:
  M1 (Diagnosis):    10%  (30 min)
  M2 (Cleaning):     70%  (2 hours) ← Most time here
  M3+M4 (Modeling):  20%  (1 hour)

Total: ~3.5 hours to production model

Why? Because:
- Loading data is simple (5 min)
- Diagnosing problems is careful (30 min)
- Fixing problems is tedious (2 hours: handle each error type)
- Building models is straightforward (1 hour: pick algorithm, tune)

Real data is messy. M2 is where you wrangle it into shape.
```

---

## **In Your Project**

**M1 Output** (what you discovered):
```
891 rows, 12 columns
Total missing: 180 values
Problem columns: Age (177), Cabin (687), Embarked (2)
Data types: 6 numeric, 6 text
Suspicious ranges: Fare min=0, Age min=0.42
```

**M2 Input** (what it received):
```
The above problem list + raw data
```

**M2 Output** (what it delivered):
```
876 rows, 18 columns (after drops & feature creation)
Total missing: 0 values
All numeric, properly encoded
All scaled to -2 to +2 range
Ready for M3
```

**M3 Input** (what it worked with):
```
M2's clean output
```

**M3 Output** (what it produced):
```
81.6% accuracy baseline
Identifies Sex & Pclass as top features
Sets benchmark for M4 to beat
```

---

## **Why This Structure Matters for Your Portfolio**

When a recruiter looks at your GitHub:

**They see:** M1 → M2 → M3 → M4 progression

**They think:**
- ✓ "This person understands the pipeline"
- ✓ "They diagnose before they clean"
- ✓ "They clean before they model"
- ✓ "They establish baselines before claiming improvement"

Not:
- ✗ "They just ran code in some order"
- ✗ "They don't know why each step matters"

**The connective tissue shows you understand WHY, not just WHAT.**
