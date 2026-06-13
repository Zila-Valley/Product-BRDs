# Data Scientist

## Introduction
A Data Scientist goes a step further than a Data Analyst. Instead of just looking at the past to report on what happened, they use advanced mathematics and "Classical" Machine Learning to predict the future. They primarily work with structured, tabular data (like spreadsheets and SQL databases) to solve high-value business problems, rather than unstructured data like images or text.

## Syllabus (Learning Path)
1.  **Advanced Statistics:** Probability, Regression Analysis, Bayesian Statistics.
2.  **Programming:** Python, R.
3.  **Data Manipulation:** Pandas, NumPy.
4.  **Classical Machine Learning:** Scikit-Learn (Linear Regression, Decision Trees, Random Forests, K-Means).
5.  **Gradient Boosting:** XGBoost, LightGBM, CatBoost.
6.  **Experimentation:** Jupyter Notebooks, Feature Engineering, Hyperparameter tuning.

## Roles and Responsibilities
*   Clean and preprocess messy tabular data (handling missing values, outliers).
*   Perform "Feature Engineering" (creating new data columns based on math, like `total_spend / days_active`).
*   Train predictive Machine Learning models on historical data.
*   Deploy models to predict future trends, customer behavior, or financial risks.

## Real-World Example

### Problem Statement
A SaaS (Software as a Service) company is losing 10% of its paying subscribers every month (High Churn Rate). By the time the customer clicks "Cancel Subscription", it is too late to offer them a discount to stay. The company needs a way to predict *which* specific customers are going to cancel *before* they actually do it.

### Solution Approach
Build a predictive Machine Learning classification model that analyzes the historical behavior of past customers to identify the hidden warning signs of a user about to cancel.

### The Steps
1.  **Data Gathering:** Pull 2 years of historical user data from the database, including login frequency, feature usage, support tickets opened, and whether they eventually canceled or stayed.
2.  **Feature Engineering:** The Data Scientist realizes that raw data isn't enough. They create a new mathematical feature: `days_since_last_login`.
3.  **Model Selection:** They choose **XGBoost** (Extreme Gradient Boosting), which is highly effective for tabular data classification.
4.  **Training:** They train the XGBoost model on the historical data. The model mathematically discovers that "Users who opened 3 support tickets and haven't logged in for 14 days have a 92% probability of canceling."
5.  **Deployment:** They run the model every night at midnight against all *current* active users. The model outputs a list of 500 users who are highly likely to cancel tomorrow.
6.  **Action:** An automated script instantly emails those 500 users a "50% off your next month" discount code, saving the company thousands of dollars in lost revenue.

### Tech Stack
*   **Language:** Python / R
*   **Environment:** Jupyter Notebooks
*   **Data Processing:** Pandas / NumPy
*   **Machine Learning Library:** Scikit-Learn / XGBoost

### Algorithm / Architecture
**Random Forests & Gradient Boosting (XGBoost):** These algorithms build hundreds of mathematical "Decision Trees" based on the data. They don't require massive GPUs to train, and they are currently the industry standard for making predictions on spreadsheet-style data.
