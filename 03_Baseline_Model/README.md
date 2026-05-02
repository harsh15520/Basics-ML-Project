### 03_Baseline_Model README

---

### Overview
This notebook builds a **simple, explainable baseline** for a binary classification task (the Titanic survival problem). It’s intentionally minimal: a straightforward data split, a logistic regression baseline, a few evaluation plots, and the model saved for reuse. The goal is to establish a clear benchmark you can iterate on.

---

### What this notebook does
- **Prepares data** and splits it into an 80/20 train/test split.  
- **Trains a Logistic Regression** model as a baseline.  
- **Evaluates** the model with accuracy, precision, recall, and ROC-AUC.  
- **Visualizes** a confusion matrix and feature importance (coefficients).  
- **Saves** the trained model and the feature list for downstream use. 

---

### Quickstart
1. **Open the notebook** `03_Baseline_Model.ipynb` in JupyterLab or Jupyter Notebook.  
2. Make sure your environment has the usual data science stack installed: `numpy`, `pandas`, `scikit-learn`, `matplotlib`, `seaborn`, and `pickle`.  
3. Run the cells in order. The notebook prints progress messages so you can follow what’s happening (data split, plotting, final summary).  

**Example commands**
```bash
# start jupyter in the repo root
jupyter lab

# or run the notebook headlessly (if you prefer)
jupyter nbconvert --to notebook --execute 03_Baseline_Model.ipynb --inplace
```

---

### Results and artifacts
- **Model type**: Logistic Regression.  
- **Train/Test split**: 80% train, 20% test.  
- **Performance on test set** (example run in the notebook): **Accuracy 73.0%**, **Precision 73.7%**, **Recall 50.0%**, **ROC-AUC 0.774**.  
- **Top features** reported include `Pclass`, `Parch`, and `SibSp`, and the notebook highlights **Sex** (female) as the strongest predictor of survival.  
- **Saved files**: `baseline_model.pkl` and `feature_cols.pkl`.

These numbers are the baseline from this run — expect variation if you change preprocessing, features, or random seeds.
---

### Notes and gotchas
- **This is a baseline**: the point is to get a working, interpretable model quickly. Don’t expect production-level performance.  
- **Feature handling**: the notebook uses a small set of engineered features (age, sex, passenger class, family size proxies). If you change features, re-save the `feature_cols.pkl` so downstream code knows what to expect. 
- **Reproducibility**: the split uses `random_state=42`. If you want different folds, change or remove that seed.  
- **Interpretation**: coefficients are plotted with color coding (red = negative effect on survival, green = positive). Use that plot to sanity-check feature directions before trying complex models.   [localhost](http://localhost/notebooks/03_Baseline_Model.ipynb)

---
