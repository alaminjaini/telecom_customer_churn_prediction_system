# 📉 Telecom Customer Churn Prediction System

A machine learning project that predicts whether a telecom customer will leave (churn) based on their account details, services used, and billing information.

---

## 📌 Project Overview

Customer churn is one of the biggest challenges in the telecom industry. Losing a customer costs far more than retaining one. This project combines a machine learning pipeline with a Power BI business dashboard to identify customers at high risk of churning, understand customer segments, and track retention metrics — so the business can act before it's too late.

> ✅ **Status:** Machine learning pipeline complete. Power BI dashboard complete — all 3 pages published.

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

## 📊 Power BI Dashboard

A business-facing 3-page Power BI dashboard built alongside the ML model to make insights accessible to non-technical stakeholders.

**Purpose**

While the ML model predicts churn for individual customers, the Power BI dashboard gives the business a high-level view of churn patterns, customer segments, and retention performance — enabling faster, data-driven decisions.

---

### Page 1 — Churn Overview ✅
> Overall churn rate, revenue at risk, and churn trend by customer tenure.

**Visuals:**
- KPI cards: Total Customers (7,043), Churned (1,869), Churn Rate (26.54%), Revenue at Risk ($139K)
- Donut chart: Churned vs Retained breakdown
- Line chart: Churn trend by tenure (months 1–72)
- 3 insight text boxes highlighting key findings

---

### Page 2 — Customer Segmentation ✅
> Churn breakdown by contract type, internet service, payment method, and senior citizen status.

**Visuals:**
- Bar chart: Churn rate by contract type (Month-to-month ~42%, One year ~11%, Two year ~3%)
- Bar chart: Churn rate by internet service (Fiber optic ~41%, DSL ~19%, None ~7%)
- Bar chart: Churn rate by payment method (Electronic check ~45% highest)
- 100% Stacked bar: Senior Citizen vs Non-Senior churn with toggle slicer filter
- 2 insight text boxes

---

### Page 3 — Retention Metrics ✅
> Tenure analysis, service gaps, and high-value customers at risk.

**Visuals:**
- KPI cards: Avg Tenure Churned (18 months), Avg Tenure Retained (37.57 months), Avg Monthly Charges Churned ($74.44)
- KPI cards: High-Value Customers at Risk (1,105), Monthly Revenue at Risk ($96.58K)
- Bar chart: Avg tenure — Churned vs Retained comparison
- Bar chart: Service gaps churn rate (No Online Security / No Tech Support ~42%)
- Column chart: High-Value Customers at Risk by Price Band (Month-to-month, filtered $70+)
- 2 insight text boxes

### Key DAX Measures

```dax
Total Customers = COUNTROWS('WA_Fn-UseC_-Telco-Customer-Churn')

Total Churned = COUNTROWS(FILTER('WA_Fn-UseC_-Telco-Customer-Churn', 
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "Yes"))

Churn Rate = DIVIDE([Total Churned], [Total Customers])

Revenue at Risk = SUMX(FILTER('WA_Fn-UseC_-Telco-Customer-Churn', 
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "Yes"), 
    'WA_Fn-UseC_-Telco-Customer-Churn'[MonthlyCharges])

Avg Tenure Churned = CALCULATE(AVERAGE('WA_Fn-UseC_-Telco-Customer-Churn'[tenure]), 
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "Yes")

Avg Tenure Retained = CALCULATE(AVERAGE('WA_Fn-UseC_-Telco-Customer-Churn'[tenure]), 
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "No")

Avg Monthly Charges Churned = CALCULATE(AVERAGE('WA_Fn-UseC_-Telco-Customer-Churn'[MonthlyCharges]), 
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "Yes")

High Value At Risk Count = CALCULATE(COUNTROWS('WA_Fn-UseC_-Telco-Customer-Churn'),
    'WA_Fn-UseC_-Telco-Customer-Churn'[MonthlyCharges] > 70,
    'WA_Fn-UseC_-Telco-Customer-Churn'[Contract] = "Month-to-month",
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "Yes")

High Value At Risk Revenue = CALCULATE(SUM('WA_Fn-UseC_-Telco-Customer-Churn'[MonthlyCharges]),
    'WA_Fn-UseC_-Telco-Customer-Churn'[MonthlyCharges] > 70,
    'WA_Fn-UseC_-Telco-Customer-Churn'[Contract] = "Month-to-month",
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "Yes")
```

### Data Source
The dashboard connects directly to the same dataset (`WA_Fn-UseC_-Telco-Customer-Churn.csv`) used in the ML pipeline, transformed using Power Query for clean column naming and type formatting.

---

## 📊 ML Results Summary

- **Best Model:** Random Forest (default hyperparameters)
- **ROC-AUC:** 0.823
- **Class imbalance** handled with SMOTE on training data only (no data leakage)
- **Explainability** added via SHAP values


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
