# Keystroke Dynamics Authentication System

A **behavioral biometric authentication system** that analyzes typing patterns to distinguish a genuine user from an imposter using machine learning.

The system captures keystroke timing information while typing a fixed password, extracts temporal features, preprocesses the data, and classifies each sample as **Genuine** or **Imposter**.

## 🎯 Problem Statement

Given a typing sample represented by **31 keystroke timing features**, determine whether it matches the enrolled user's typing pattern.

```text
Keystroke Events
      ↓
31 Temporal Features
      ↓
Preprocessing
      ↓
Feature Selection / PCA
      ↓
ML Classification
      ↓
Genuine / Imposter
```

## 📊 Dataset

The dataset contains samples from the enrolled user and other users attempting to impersonate them.

- **Genuine:** ~39%
- **Imposter:** ~61%
- **Features:** 31 temporal keystroke features
- **Task:** Binary classification

### Feature Types

| Feature | Description |
|---|---|
| **Hold Time (H)** | Time a key remains pressed |
| **Down-to-Down (DD)** | Time between consecutive key presses |
| **Up-to-Down (UD)** | Time between releasing one key and pressing the next |

**31 features = 11 Hold + 10 DD + 10 UD**

## 🔧 Preprocessing

The data pipeline includes:

1. Numeric conversion and data cleaning
2. Removal of invalid/missing samples
3. **3-sigma outlier filtering**
4. **StandardScaler** normalization
5. Train/test split
6. Feature selection and dimensionality reduction experiments using **RFECV and PCA**

## 🤖 Machine Learning Models

Multiple classifiers were evaluated:

- Logistic Regression
- KNN
- Decision Tree
- Random Forest
- Extra Trees
- MLP
- Bagging
- Voting Classifier

Random Forest and other tree-based ensemble methods performed particularly well because the keystroke features can have **nonlinear relationships and feature interactions**, while the dataset is relatively small and tabular.

### Reported Results

| Model | Accuracy | F1 |
|---|---:|---:|
| Random Forest | 98.86% | 0.9885 |
| Decision Tree | 98.86% | 0.9886 |
| MLP | 98.29% | 0.9828 |
| Bagging | 98.29% | 0.9828 |
| KNN | 92.57% | 0.9259 |
| Logistic Regression | 89.14% | 0.8915 |

Authentication-specific evaluation also considers **FAR, FRR and EER**, since false acceptance and false rejection have different consequences.

## 📁 Project Structure

```text
Behavioural Authentication/
├── Data/
│   └── KeystrokeData.csv
├── Jupyter Notebooks/
│   └── Keystroke.ipynb
├── test/
│   └── test.ipynb
├── KeystrokeLoggingApplication.jar
├── Keystrokes.csv
├── KeystrokesInNano.csv
└── PROJECT_ARCHITECTURE.md
```

## 🚀 Getting Started

### Install Dependencies

```bash
python -m venv .venv
source .venv/bin/activate
pip install pandas numpy scikit-learn matplotlib seaborn jupyter
```

### Collect Keystroke Data

```bash
java -jar KeystrokeLoggingApplication.jar
```

The application records key press/release timings while typing the fixed password.

### Train

Open:

```text
Jupyter Notebooks/Keystroke.ipynb
```

Run the notebook to preprocess the dataset, train the classifiers, and compare their performance.

### Test

Open:

```text
test/test.ipynb
```

Use newly collected keystroke samples to generate **Genuine / Imposter** predictions.

## ⚠️ Limitations

- **Fixed-text authentication:** currently relies on a fixed password sequence.
- **Hardware dependence:** typing timings can vary across keyboards and devices.
- **Behavioral drift:** typing patterns can change with fatigue, stress, injury, etc.
- **Dataset diversity:** broader users and multiple sessions are needed for stronger generalization.
- **No continuous authentication:** the current system performs sample-based rather than continuous verification.

## 🔮 Future Improvements

- Continuous authentication using rolling typing windows
- User/session-aware evaluation
- Cross-device validation
- FAR/FRR-based threshold tuning
- Larger and more diverse datasets
- Sequence-based models such as LSTMs
- Multi-factor authentication integration

## 🛠️ Tech Stack

**Python · Scikit-learn · NumPy · Pandas · Matplotlib · Seaborn · Jupyter · Java**
