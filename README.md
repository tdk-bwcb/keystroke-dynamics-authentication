# 🔐 Behavioural Authentication Using Keystroke Dynamics

A machine-learning–based authentication system that classifies login attempts as **Genuine** or **Imposter** by analysing keyboard typing patterns.  
Built using **31 timing-based keystroke features** such as hold durations and inter-key latencies.

---

## 📌 Overview
- 📊 **875 samples** with 31 timing features  
- 🏷️ Labels: **Genuine (38.7%)** | **Imposter (61.3%)**  
- 🔧 Preprocessing + **RFECV feature selection**  
- 🤖 Multiple ML models trained & compared  
- 🏆 **Random Forest delivered the best performance**

---

## 🧠 Methods Used
- Data preprocessing  
- **RFECV** for optimal feature subset  
- **PCA** (comparison experiment)  
- Models:
  - Logistic Regression  
  - KNN (k = 5)  
  - Decision Tree  
  - **Random Forest** ⭐  
  - AdaBoost  
  - Extra Trees  
  - Voting Classifier (KNN + RF + Extra Trees)

---

## 🏆 Results
**Random Forest** performed the best:

- ✔️ Accuracy: **0.989**  
- ✔️ F1-Score: **0.989**

Ensemble tree-based models proved highly effective for keystroke-based authentication.

---

keystroke-authentication/
│
├── data/
│   ├── raw/                    # Original untouched data
│   │   └── keystrokes.csv
│   ├── processed/              # Cleaned / feature-engineered data
│   │   └── keystrokes_processed.csv
│
├── notebooks/
│   ├── 01_exploration.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_model_training.ipynb
│   └── 04_evaluation.ipynb
│
├── src/
│   ├── __init__.py
│   ├── preprocessing.py
│   ├── feature_selection.py
│   ├── models/
│   │   ├── random_forest.py
│   │   ├── knn.py
│   │   ├── adaboost.py
│   │   └── ensemble.py
│   ├── evaluation.py
│   └── utils.py
│
├── models/
│   └── random_forest.pkl
│
├── app/
│   └── KeystrokeLoggingApplication.jar
│
├── reports/
│   ├── figures/
│   └── results.md
│
├── tests/
│   └── test_pipeline.py
│
├── README.md
├── requirements.txt
├── PROJECT_ARCHITECTURE.md
├── .gitignore
└── LICENSE


---

## 🔧 Tech Stack
- Python (NumPy, Pandas, Scikit-learn)
- Matplotlib / Seaborn
- Random Forest and ensemble models
- Java-based Keystroke Logging Application

