# Keystroke Dynamics Authentication: Project Architecture

## Overview
This project implements **user authentication via keystroke dynamics** — a biometric authentication method that analyzes typing patterns to distinguish genuine users from imposters.

---

## Task Type & Problem Definition

### Problem Statement
**Classification Task**: **Binary Classification** (Multi-user in extended version)

Given a typing sample (fixed vector of keystroke timing features), classify the sample as:
- **Genuine** = the typing sample matches the user's trained keystroke profile
- **Imposter** = the typing sample does NOT match the user's trained keystroke profile

### Model Task
- **Authentication (Verification)**: For each trained user, determine if a new typing sample is from that user (genuine) or from someone else (imposter).
- In the project notebooks, this is a **binary classification**:
  - Positive class (0) = **"Genuine"** 
  - Negative class (1) = **"Imposter"**

---

## Data Structure

### Input CSV Format
**File**: `Data/KeystrokeData.csv` and your `Keystrokes.csv`

#### Columns
1. **User** (string): Name of the user who produced the typing sample.
   - Example: `"Atharwa"`, `"Nikhil"`, etc.
   - In your `Keystrokes.csv`, this column is currently empty (only one user).

2. **Feature Columns** (31 numeric columns, hold times and digraph timings):
   - Columns 2–32: `H.period`, `DD.period.t`, `UD.period.t`, `H.t`, ..., `H.Return`
   - **H.X**: **Hold time** for key X (milliseconds) — time key X is pressed
   - **DD.X.Y**: **Keydown-Keydown time** from key X to key Y (milliseconds) — time between pressing X and pressing Y
   - **UD.X.Y**: **Keyup-Down time** from key X to key Y (milliseconds) — time between releasing X and pressing Y
   - Example: `H.period = 0.119` means holding the "." (period) key for 0.119 ms
   - Example: `DD.period.t = 0.272` means 0.272 ms elapsed between pressing "." and pressing "t"

3. **Target** (string, categorical):
   - **"Genuine"** = typing sample from the actual user
   - **"Imposter"** = typing sample from someone else impersonating the user
   - **Note**: In multi-user setup, this is often replaced with a `user_id` column (int) instead.

#### Example Row
```csv
User,H.period,DD.period.t,UD.period.t,H.t,DD.t.i,UD.t.i,H.i,...,H.Return,Target
Atharwa,0.119,0.272,0.153,0.103,0.208,0.105,0.103,...,0.112,Genuine
Atharwa,0.119,0.272,0.153,0.103,0.216,0.113,0.103,...,0.151,Genuine
Ali,0.234,0.301,0.120,0.095,0.250,0.130,0.110,...,0.098,Imposter
```

---

## Model Input & Output

### Input (X)
- **Type**: Vector of 31 numeric features
- **Shape**: `(n_samples, 31)`
- **Features**: Hold times, keydown-keydown times, keyup-down times (all in milliseconds)
- **Example**:
  ```
  [0.119, 0.272, 0.153, 0.103, 0.208, 0.105, 0.103, 0.288, 0.185, ...]
  ```

### Output (y)
- **Type**: Binary categorical label (string or encoded as 0/1)
- **Classes**:
  - `0` or `"Genuine"` (sample matches the claimed user's profile)
  - `1` or `"Imposter"` (sample does NOT match the user's profile)
- **Example**:
  ```
  Genuine  (output class 0)
  Imposter (output class 1)
  ```

### Interpretation
- **Model predicts "Genuine"** → Accept the user (authentication succeeds)
- **Model predicts "Imposter"** → Reject the user (authentication fails)

---

## Genuine vs. Imposter in the CSV

### How Genuine/Imposter is Assigned
The dataset was created by:
1. **Genuine samples**: Collecting multiple typing sessions from the actual user (e.g., Atharwa) typing the same fixed text.
2. **Imposter samples**: Collecting typing sessions from other users (e.g., Ali, Bob, Charlie) attempting to impersonate the target user (e.g., Atharwa) by typing the **same fixed text**.

### Why Fixed Text?
All samples in the dataset are for the **same password/text** (appears to be a sequence like `.t.i.e.five.Shift.r.o.a.n.l.Return` based on the feature column names).

- This ensures that feature differences come from **typing style variations** (speed, rhythm), not from different passwords.
- In a real deployment, you'd either:
  - Train a model per fixed password, or
  - Use features robust to variable-length passwords (e.g., digraph statistics).

### Data Collection Flow
```
├─ User "Nikhil" types password 100 times
│  └─ All 100 labeled as "Genuine" (Target = Genuine)
├─ User "Ali" types the same password 50 times (trying to pass as Nikhil)
│  └─ All 50 labeled as "Imposter" (Target = Imposter)
├─ User "Bob" types the same password 50 times (trying to pass as Nikhil)
│  └─ All 50 labeled as "Imposter" (Target = Imposter)
└─ ... more imposters
```

---

## Dataset Summary from `KeystrokeData.csv`

- **Total rows**: ~1,423 samples
- **Target user**: "Nikhil" (the user being authenticated against)
- **Genuine samples**: Multiple typing sessions from Nikhil (~30+ rows visible)
- **Imposter samples**: Typing sessions from other users (Ali, Bob, Charlie, etc.) (~30+ rows per imposter)
- **Class distribution**: Roughly balanced or imbalanced (check using `data['Target'].value_counts()`)

---

## Model Architectures (from project notebooks)

### 1. **K-Nearest Neighbors (KNN)** — `KNearestNeighbourModel.ipynb`
- **Algorithm**: Classify based on distance to k nearest training samples.
- **Hyperparameter**: `n_neighbors = 5` (default; optimized via error rate plots).
- **Training**: Stores all training data (lazy learner).
- **Output**: "Genuine" or "Imposter"

### 2. **Logistic Regression** — `LogisticRegressionModel.ipynb`
- **Algorithm**: Linear probabilistic classifier.
- **Hyperparameter**: `max_iter = 200`, `solver = 'lbfgs'`
- **Training**: Learns a linear decision boundary.
- **Output**: "Genuine" or "Imposter"

### 3. **Keystroke Feature Notebook** — `Keystroke.ipynb`
- Likely covers data exploration, feature engineering, and distribution analysis.

---

## Training & Evaluation Pipeline

### Train-Test Split
```python
train, test = train_test_split(data, test_size=0.2)
# 80% for training, 20% for testing
# Each split includes both genuine and imposter samples
```

### Feature Scaling
```python
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)
# Standardizes features to mean=0, std=1 for fair distance/weight comparison
```

### Model Training
```python
knn = KNeighborsClassifier(n_neighbors=5)
knn.fit(X_train, y_train)
predictions = knn.predict(X_test)
```

### Evaluation Metrics
- **Accuracy**: Percentage of correct predictions.
  ```
  accuracy = (TP + TN) / (TP + TN + FP + FN)
  ```
- **Confusion Matrix**:
  ```
         Predicted
       Genuine  Imposter
  Real Genuine    TP       FN
       Imposter   FP       TN
  ```
- **Classification Report**:
  - Precision (of "Genuine"): How many predicted genuine are actually genuine?
  - Recall (of "Genuine"): How many actual genuine were correctly identified?
  - F1-Score: Harmonic mean of precision and recall.

---

## Your Current Situation

### `Keystrokes.csv`
- **Status**: Contains 48 samples (all from 1 user, no Target column yet)
- **User column**: Empty
- **Target column**: Missing (no "Genuine" or "Imposter" labels)

### `KeystrokesInNano.csv`
- **Status**: Similar to `Keystrokes.csv` but times in nanoseconds instead of milliseconds.

### What You Need to Do
1. **Add a Target column** (or decide if this is single-user verification):
   - For authentication, you need both genuine and imposter samples.
   - If you only have data from one user so far, collect impostor typing samples from other users.

2. **Ensure consistent units** (ms vs ns):
   - Convert `KeystrokesInNano.csv` to milliseconds, or use only one file.

3. **Decide on authentication scope**:
   - **Single-user authentication**: Nikhil vs. imposters (current project approach)
   - **Multi-user identification**: Which of N users typed this? (need samples from all N users)

---

## Next Steps (Recommended)

### Phase 1: Prepare Your Data
- [ ] Collect more users' keystroke samples (for impostor negatives)
- [ ] Ensure CSV columns: `User`, features (H.*, DD.*, UD.*), `Target` (or `user_id`)
- [ ] Verify units (milliseconds) and handle missing values

### Phase 2: Train Models
- [ ] Use the provided `KNearestNeighbourModel.ipynb` and `LogisticRegressionModel.ipynb` as templates
- [ ] Split: 70% training, 30% testing (or stratified k-fold)
- [ ] Train KNN, Logistic Regression, and optionally SVM/RandomForest

### Phase 3: Evaluate
- [ ] Compute accuracy, confusion matrix, precision, recall, F1-score
- [ ] Plot ROC curves and compute EER (Equal Error Rate) — standard for biometric systems
- [ ] Analyze per-user performance (if multi-user)

### Phase 4: Deploy
- [ ] Save trained model + scaler with `joblib`
- [ ] Optionally export to ONNX for Java integration
- [ ] Create a REST API or Java wrapper for live predictions

---

## Key Concepts

### Hold Time (H.X)
Time (in ms) that key X is held down (pressed and held).
- **Genuine pattern**: User consistently holds certain keys for ~0.1–0.3 ms.
- **Imposter challenge**: Hard to replicate exact hold times of another user.

### Digraph Times (DD, UD)
Times between consecutive key presses/releases (rhythm).
- **DD.X.Y**: Press X → press Y (time to move fingers + press next key)
- **UD.X.Y**: Release X → press Y (time between lifting finger and pressing next)
- **Genuine pattern**: User has consistent inter-keystroke rhythm.
- **Imposter challenge**: Imposters type at different speeds and rhythms.

### Keystroke Dynamics Advantage
Unlike passwords, keystroke patterns are:
- **Hard to copy**: Even if someone knows the password, they type differently.
- **Passive**: Verified during normal login (no extra hardware).
- **Complementary**: Works alongside passwords for stronger authentication.

---

## References
- IEEE paper on keystroke dynamics: https://ieeexplore.ieee.org/abstract/document/5270346
- Scikit-learn KNeighborsClassifier: https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsClassifier.html
- Scikit-learn LogisticRegression: https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html

---

## Summary Table

| Aspect | Value |
|--------|-------|
| **Task Type** | Binary classification (Genuine vs. Imposter) |
| **Input** | 31 numeric features (hold times, digraph times) in milliseconds |
| **Output** | Label: "Genuine" or "Imposter" |
| **Target User** | One user being authenticated against |
| **Imposters** | Other users attempting to impersonate the target user |
| **Algorithms** | KNN, Logistic Regression, SVM, RandomForest |
| **Evaluation** | Accuracy, Confusion Matrix, Precision, Recall, F1, ROC, EER |
| **Data Collection** | Fixed-text password typed multiple times by each user |
| **Current Data** | 1 user (48 samples); need impostor samples to complete |

---
