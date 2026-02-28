# 📉 Telecom Customer Churn Prediction System

A machine learning project that predicts whether a telecom customer will leave (churn) based on their account details, services used, and billing information.

---

## 📌 Project Overview

Customer churn is one of the biggest challenges in the telecom industry. Losing a customer costs far more than retaining one. This project combines a machine learning pipeline with a Power BI business dashboard to identify customers at high risk of churning, understand customer segments, and track retention metrics — so the business can act before it's too late.

> 🚧 **Status:** Machine learning pipeline complete. Power BI dashboard in progress.

---

## 📂 Dataset

**Source:** [IBM Telco Customer Churn Dataset](https://www.kaggle.com/datasets/blastchar/telco-customer-churn)  
**File:** `WA_Fn-UseC_-Telco-Customer-Churn.csv`

| Property | Value |
|----------|-------|
| Rows | 7,043 customers |
| Columns | 21 (reduced to 20 after dropping ID) |
| Target | `Churn` — Yes / No |
| Class balance | 73.5% No Churn, 26.5% Churn |

### Features

| Category | Columns |
|----------|---------|
| Demographics | `gender`, `SeniorCitizen`, `Partner`, `Dependents` |
| Services | `PhoneService`, `MultipleLines`, `InternetService`, `OnlineSecurity`, `OnlineBackup`, `DeviceProtection`, `TechSupport`, `StreamingTV`, `StreamingMovies` |
| Account | `Contract`, `PaperlessBilling`, `PaymentMethod`, `tenure` |
| Billing | `MonthlyCharges`, `TotalCharges` |
| Target | `Churn` |

---

## 🔧 Tech Stack

### Machine Learning (Python)
| Library | Purpose |
|---------|---------|
| `pandas`, `numpy` | Data loading and manipulation |
| `matplotlib`, `seaborn` | Data visualisation |
| `scikit-learn` | Model training, encoding, evaluation |
| `imbalanced-learn` | SMOTE for class imbalance |
| `xgboost` | XGBoost classifier |
| `shap` | Model explainability |
| `pickle` | Saving and loading the model |

**Install all dependencies:**
```bash
pip install numpy pandas matplotlib seaborn scikit-learn imbalanced-learn xgboost shap
```

### Business Intelligence
| Tool | Purpose |
|------|---------|
| `Power BI Desktop` | Dashboard design and visualisation |
| `DAX` | Calculated measures and KPIs |
| `Power Query` | Data transformation within Power BI |

---

## 🗂️ Project Structure

```
├── Customer_Churn_Prediction.ipynb        # Main ML notebook
├── WA_Fn-UseC_-Telco-Customer-Churn.csv   # Dataset
├── customer_churn_model.pkl               # Saved trained model
├── encoders.pkl                           # Saved label encoders
├── Customer_Churn_Dashboard.pbix          # Power BI dashboard (in progress)
└── README.md
```

---

## 🔄 Project Workflow

### 1. Data Understanding
- Loaded the dataset (7,043 rows × 21 columns)
- Inspected data types, shape, and column names
- Found `TotalCharges` stored as `object` instead of `float` due to 11 blank rows (new customers with `tenure = 0`)

### 2. Data Cleaning
- Dropped `customerID` (irrelevant identifier)
- Replaced blank spaces in `TotalCharges` with `0.0` and converted to `float`
- Confirmed no null values across all columns

### 3. Exploratory Data Analysis (EDA)
- **Histograms** with mean/median lines for `tenure`, `MonthlyCharges`, `TotalCharges`
- **Box plots** to check for outliers in numerical features
- **Correlation heatmap** across numerical columns
- **Count plots** for all categorical features

### 4. Data Preprocessing
- Encoded target column `Churn` → `1` (Yes) / `0` (No)
- Applied `LabelEncoder` to 15 categorical columns
- Saved all encoders to `encoders.pkl` for use in the prediction system

### 5. Handling Class Imbalance
- Training set: **4,138 No Churn** vs **1,496 Churn**
- Applied **SMOTE** (Synthetic Minority Oversampling Technique) on training data only
- After SMOTE: **4,138 vs 4,138** (balanced)

### 6. Model Training
Trained 3 models with default hyperparameters using 5-fold cross-validation:

| Model | CV Accuracy |
|-------|------------|
| Decision Tree | 0.78 |
| Random Forest | **0.84** ✅ |
| XGBoost | 0.83 |

### 7. Model Evaluation (Random Forest)

| Metric | Score |
|--------|-------|
| Accuracy | 77.9% |
| ROC-AUC | **0.823** |
| Precision (Churn) | 0.58 |
| Recall (Churn) | 0.59 |
| F1-Score (Churn) | 0.58 |

### 8. Model Explainability (SHAP)
- Used `TreeExplainer` to calculate SHAP values on the test set
- **Summary bar plot** — overall feature importance
- **Dot summary plot** — direction and magnitude of each feature's impact
- **Waterfall plot** — individual customer prediction explanation
- Sample prediction: Customer at index 0 → **Churn probability: 97%**

### 9. Prediction System
- Loads saved model and encoders from `.pkl` files
- Accepts raw customer input as a dictionary
- Encodes input using saved encoders
- Returns prediction label and churn probability

**Example prediction:**
```python
input_data = {
    'gender': 'Male', 'SeniorCitizen': 1, 'Partner': 'No',
    'tenure': 18, 'InternetService': 'Fiber optic',
    'Contract': 'Month-to-month', 'MonthlyCharges': 94.45, ...
}
# Output: Prediction: Churn | Probability: 72%
```

---

## 📊 Power BI Dashboard *(In Progress)*

A business-facing Power BI dashboard is being developed alongside the machine learning model to make the insights accessible to non-technical stakeholders.

### Purpose
While the ML model predicts churn for individual customers, the Power BI dashboard gives the business a **high-level view** of churn patterns, customer segments, and retention performance — enabling faster, data-driven decisions.

### Planned Dashboard Pages

**Page 1 — Churn Overview**
- Overall churn rate (26.5%)
- Total churned vs retained customers
- Churn trend over customer tenure
- KPI cards: Total Customers, Churned, Revenue at Risk

**Page 2 — Customer Segmentation**
- Churn rate by contract type (Month-to-month vs One year vs Two year)
- Churn rate by internet service type (Fiber optic vs DSL vs None)
- Churn rate by payment method
- Senior citizen vs non-senior churn comparison

**Page 3 — Retention Metrics**
- Average tenure of churned vs retained customers
- Monthly charges distribution by churn status
- Customers with no online security / tech support — churn risk
- High-value customers at risk (high MonthlyCharges + Month-to-month contract)

### Key DAX Measures (Planned)
```dax
Churn Rate = DIVIDE(COUNTROWS(FILTER('Data', 'Data'[Churn] = "Yes")), COUNTROWS('Data'))

Avg Monthly Charges (Churned) = CALCULATE(AVERAGE('Data'[MonthlyCharges]), 'Data'[Churn] = "Yes")

Revenue at Risk = SUMX(FILTER('Data', 'Data'[Churn] = "Yes"), 'Data'[MonthlyCharges])
```

### Data Source
The dashboard connects directly to the same dataset (`WA_Fn-UseC_-Telco-Customer-Churn.csv`) used in the ML pipeline, transformed using Power Query for clean column naming and type formatting.

---

## 📊 ML Results Summary

- **Best Model:** Random Forest (default hyperparameters)
- **ROC-AUC:** 0.823
- **Class imbalance** handled with SMOTE on training data only (no data leakage)
- **Explainability** added via SHAP values

---

## 🚀 How to Run

1. Clone the repository
```bash
git clone https://github.com/yourusername/customer-churn-prediction.git
cd customer-churn-prediction
```

2. Install dependencies
```bash
pip install numpy pandas matplotlib seaborn scikit-learn imbalanced-learn xgboost shap
```

3. Open the notebook
```bash
jupyter notebook Customer_Churn_Prediction.ipynb
```

4. Run all cells from top to bottom

---

## 🔮 Future Improvements

- [ ] Hyperparameter tuning with `RandomizedSearchCV` / `GridSearchCV`
- [ ] Add LightGBM and CatBoost for model comparison
- [ ] Build a Voting Classifier combining the best models
- [ ] Deploy as a web app using Streamlit or Flask
- [ ] Complete and publish the Power BI dashboard
- [ ] Connect Power BI directly to model output for live churn scoring

---

## 👤 Author

Built as a machine learning portfolio project.  
Feel free to fork, star ⭐, or open an issue!
