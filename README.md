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

## 📁 Repository Structure
Data/ # Dataset (if included)
Images/ # Supporting visuals
Jupyter Notebooks/ # Experiments & training
test/ # Test scripts
KeystrokeLoggingApplication.jar
Keystrokes.csv
random_forest_model.pkl # Trained model
PROJECT_ARCHITECTURE.md
README.md

---

## 🔧 Tech Stack
- Python (NumPy, Pandas, Scikit-learn)
- Matplotlib / Seaborn
- Random Forest and ensemble models
- Java-based Keystroke Logging Application

