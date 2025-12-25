# 🔐 Keystroke Dynamics Authentication

A **machine learning–based behavioural biometric system** that authenticates users by analyzing their **typing patterns (keystroke dynamics)**.  
The system classifies login attempts as **Genuine** or **Imposter** using timing-based features extracted from keystroke data.

---

## 📌 Project Overview

- 📊 **875 keystroke samples**
- ⌨️ **31 timing-based features** (hold time, flight time, etc.)
- 🏷️ Labels: **Genuine (38.7%)**, **Imposter (61.3%)**
- 🔧 Feature engineering + **RFECV feature selection**
- 🤖 Multiple ML models evaluated
- 🏆 **Random Forest achieved best performance**

---

## 🧠 Methods & Models

### Feature Engineering
- Key hold duration
- Inter-key latency
- Statistical normalization
- RFECV-based feature selection

### Machine Learning Models
- Logistic Regression  
- K-Nearest Neighbors (K = 5)  
- Decision Tree  
- **Random Forest (Best Performer)**  
- AdaBoost  
- Extra Trees  
- Voting Classifier (KNN + RF + Extra Trees)

---

## 🏆 Results

| Metric | Score |
|------|------|
| **Accuracy** | **0.989** |
| **F1-Score** | **0.989** |

✅ Ensemble tree-based models consistently outperformed linear models  
✅ Random Forest showed the best stability and generalization

---

## 🧩 System Architecture

- **Java Application**  
  Captures keystroke timing data and stores raw logs.

- **Python ML Pipeline**
  - Data preprocessing & feature extraction  
  - Model training and evaluation  
  - Serialized model for inference  

- **Evaluation Layer**
  - Performance metrics
  - Visualization and analysis

---

## 🛠 Tech Stack

- **Python** (NumPy, Pandas, Scikit-learn)
- **Matplotlib / Seaborn**
- **Java (Keystroke Logger)**

