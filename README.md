# AI-Powered Customer Retention & Revenue Intelligence Pipeline

## 📌 Executive Summary
This project is an end-to-end data engineering and machine learning pipeline built to solve customer churn. Rather than performing isolated analysis, this project simulates a production backend environment: extracting raw data from a MySQL database, processing it through a Python-based predictive engine, calculating financial risk, and writing the final intelligence back into the database for downstream enterprise systems to consume.

---

## ⚙️ System Architecture & Data Flow
The pipeline follows a complete Extraction, Transformation, Machine Learning, and Load (ETML) workflow:
1. **Extract:** Fetch raw customer data from a local MySQL database via `mysql-connector-python`.
2. **Transform:** Clean missing values, format data types, and encode categorical variables using `pandas`.
3. **Predict:** Train a Random Forest Classifier (`scikit-learn`) to predict individual churn probability.
4. **Strategize:** Apply business logic to calculate **Revenue-at-Risk** and assign targeted retention strategies.
5. **Load:** Push the newly generated intelligence table back into the MySQL database using `SQLAlchemy`.

---

## 🛠️ Technology Stack
* **Core Language:** Python 3.13
* **Relational Database:** MySQL
* **Data Ingestion & Connection:** SQLAlchemy, MySQL-Connector-Python, urllib
* **Data Transformation:** Pandas, NumPy
* **Machine Learning:** Scikit-Learn (Random Forest, Classification Metrics)
* **Environment:** Jupyter Notebooks, Git/GitHub

---

## 📖 Deep-Dive Methodology: How It Was Built

### Phase 1: Database Connection & Data Extraction
The project begins by establishing a secure connection to the local MySQL instance (`churn_retention_lab`). 
* Executed a SQL query (`SELECT * FROM customers;`) directly through Python.
* Extracted over 7,000 customer records into a Pandas DataFrame for in-memory processing.

### Phase 2: Data Cleaning & Preprocessing
Raw subscription data often contains hidden formatting issues.
* **Handling Nulls:** Discovered that new customers had blank spaces (`' '`) in their `TotalCharges` column. 
* **Transformation:** Replaced spaces with `NaN`, converted the entire column to numeric float types, and imputed the missing values with `0` to ensure machine learning algorithms could process the data.

### Phase 3: Exploratory Data Analysis (EDA)
Conducted statistical analysis to identify baseline metrics and commercial risk factors:
* **Baseline Churn:** Established a baseline churn rate of **~26.5%**.
* **Contract Risk:** Identified that "Month-to-month" contracts hold the highest volume of churn compared to 1-year and 2-year commitments.
* **Tenure Risk:** Discovered via histogram distribution that the highest risk of customer abandonment occurs within the **first 1 to 3 months** of the customer lifecycle.

### Phase 4: Feature Engineering
Prepared the dataset for the Random Forest algorithm:
* Dropped non-predictive columns (`customerID`).
* Mapped the binary target variable (`Churn`) to integers (`1` and `0`).
* Applied **One-Hot Encoding** (`pd.get_dummies`) to all categorical variables, utilizing `drop_first=True` to prevent multicollinearity (the dummy variable trap).
* Split the dataset into 80% Training and 20% Testing sets, utilizing **stratified sampling** to maintain the 26.5% churn ratio in both sets.

### Phase 5: Machine Learning & Explainability
* **Model Training:** Initialized and trained a `RandomForestClassifier` with 100 estimators.
* **Evaluation:** Generated a classification report and Confusion Matrix. Prioritized **Recall** to ensure the model successfully identified the maximum number of true at-risk customers.
* **Feature Importance:** Extracted the algorithmic decision drivers, mathematically validating that `TotalCharges`, `MonthlyCharges`, `Tenure`, and `Contract Type` were the strongest predictors of churn.

### Phase 6: Revenue-at-Risk & Business Logic
Instead of outputting binary predictions, the model was engineered to output **probability scores (0% to 100%)**.
* **Financial Impact:** Multiplied the churn probability by the customer's `MonthlyCharges` to create a new `Revenue_at_Risk` metric.
* **Risk Segmentation:** Built a custom Python function to segment customers into `High Risk` (≥ 70%), `Medium Risk` (40-69%), and `Low Risk` (< 40%).
* **Actionable Intelligence:** Automated business strategies based on risk and revenue (e.g., High-Risk customers generating > $70/month were flagged for a "Personal Customer Success Call", while lower-value high-risk customers were flagged for an "Automated Retention Discount").

### Phase 7: Closing the Loop (Database Write-Back)
To make the data actionable for a hypothetical frontend application or CRM, the final intelligence dataframe was pushed back to MySQL.
* Handled secure password parsing for special characters using `urllib.parse.quote_plus`.
* Utilized **SQLAlchemy** to generate a database engine and executed `to_sql()` to automatically build and populate a new table named `customer_retention_intelligence` inside the MySQL schema.

---

## 🚀 How to Run This Project Locally
1. Clone this repository to your local machine.
2. Ensure MySQL is installed and running locally.
3. Install the required Python dependencies:
   ```bash
   pip install pandas numpy scikit-learn mysql-connector-python sqlalchemy matplotlib seaborn
