# 🏦 Bank Marketing Campaign Analysis

## Project Overview

This project analyzes the **Bank Marketing dataset** to understand
customer characteristics, marketing campaign behavior, and factors
associated with term-deposit subscription.

**Python** is used for data loading, cleaning, feature engineering,
exploratory data analysis (EDA), and visualization. **Power BI** is used
to create an interactive dashboard and communicate business insights.

## Problem Statement

Banks conduct marketing campaigns to encourage customers to subscribe to
term deposits. The objective of this project is to identify patterns
associated with subscription and provide data-driven recommendations for
better customer targeting.

## Dataset

-   **Dataset:** Bank Marketing (`bank-full.csv`)
-   **Rows:** 45,211
-   **Columns:** 17
-   **Input features:** 16
-   **Target:** `y`
-   **Target values:** `yes` / `no`
-   **Separator:** `;`

### Target Distribution

-   Subscribed: **5,289**
-   Not subscribed: **39,922**
-   Subscription rate: **approximately 11.7%**

## Features

  Feature     Description
  ----------- ----------------------------------------
  age         Customer age
  job         Occupation
  marital     Marital status
  education   Education level
  default     Credit in default
  balance     Average yearly account balance
  housing     Housing loan
  loan        Personal loan
  contact     Communication method
  day         Day of last contact
  month       Month of last contact
  duration    Last contact duration in seconds
  campaign    Number of contacts in current campaign
  pdays       Days since previous campaign contact
  previous    Number of previous contacts
  poutcome    Previous campaign outcome
  y           Term-deposit subscription

`pdays = -1` means the customer was not previously contacted in an
earlier campaign.

## Technologies

-   Python
-   Pandas
-   NumPy
-   Matplotlib
-   Seaborn
-   Google Colab / Jupyter Notebook
-   Power BI
-   DAX
-   GitHub

## Project Workflow

``` text
Dataset
   ↓
Data Loading
   ↓
Data Validation
   ↓
Data Cleaning
   ↓
Feature Engineering
   ↓
Univariate Analysis
   ↓
Bivariate Analysis
   ↓
Multivariate Analysis
   ↓
Business Insights
   ↓
Power BI Dashboard
   ↓
Recommendations
```

## Data Cleaning & Preprocessing

The dataset was loaded using:

``` python
import pandas as pd
import numpy as np

df = pd.read_csv("bank-full.csv", sep=";")

print("Shape:", df.shape)
print(df.isnull().sum())
print("Duplicate rows:", df.duplicated().sum())
print(df.dtypes)
```

The validation performed showed **no missing values and no duplicate
rows**.

Because this project focuses primarily on EDA, extensive
machine-learning preprocessing was not required.

## Feature Engineering

### Subscription

``` python
df["subscription"] = df["y"].map({
    "no": 0,
    "yes": 1
})
```

### Age Group

``` python
df["age_group"] = pd.cut(
    df["age"],
    bins=[0, 25, 35, 45, 55, 100],
    labels=["18-25", "26-35", "36-45", "46-55", "56+"]
)
```

### Balance Category

``` python
df["balance_category"] = pd.cut(
    df["balance"],
    bins=[-np.inf, 0, 1000, 5000, np.inf],
    labels=["Negative", "Low", "Medium", "High"]
)
```

### Campaign Category

``` python
df["campaign_category"] = pd.cut(
    df["campaign"],
    bins=[0, 1, 3, 5, np.inf],
    labels=["1", "2-3", "4-5", "6+"]
)
```

### Previously Contacted

``` python
df["previously_contacted"] = np.where(
    df["pdays"] == -1,
    "No",
    "Yes"
)
```

### Call Duration in Minutes

``` python
df["duration_minutes"] = df["duration"] / 60
```

# Exploratory Data Analysis

## Univariate Analysis

Univariate analysis examines one variable at a time.

Visualizations include:

1.  Term Deposit Subscription
2.  Customer Age Distribution
3.  Customers by Job
4.  Education Level Distribution
5.  Marital Status Distribution
6.  Account Balance Distribution
7.  Campaign Contacts Distribution
8.  Last Contact Duration Distribution

**Purpose:** Understand the distribution and characteristics of
individual variables.

## Bivariate Analysis

Bivariate analysis examines relationships between two variables.

Visualizations include:

1.  Housing Loan vs Subscription
2.  Personal Loan vs Subscription
3.  Subscription Rate by Job
4.  Subscription Rate by Education
5.  Previous Campaign Outcome vs Subscription
6.  Subscription Rate by Contact Month
7.  Age vs Account Balance

**Purpose:** Identify relationships between customer/campaign variables
and subscription behavior.

## Multivariate Analysis

Multivariate analysis examines three or more variables together.

### 1. Job + Education + Subscription

``` python
job_education = (
    df.groupby(["job", "education"])["subscription"]
      .mean()
      .mul(100)
      .reset_index()
)

sns.barplot(
    data=job_education,
    x="job",
    y="subscription",
    hue="education"
)
```

This identifies subscription patterns across occupation and education
combinations.

### 2. Age Group + balance + Subscription

This compares subscription rates across age groups while considering
housing-loan status.


### 3. Correlation Analysis

A correlation heatmap is used to examine relationships among numerical
variables such as age, balance, duration, campaign, previous contacts,
and subscription.

> Correlation indicates association and does not prove causation.

# Power BI Dashboard

The Power BI dashboard converts the Python analysis into an interactive
business report.

## KPI Cards

-   Total Customers: **45,211**
-   Subscribed Customers: **5,289**
-   Not Subscribed: **39,922**
-   Subscription Rate: **11.7%**
-   Average Balance
-   Average Call Duration

## Dashboard Visuals

1.  Subscription Distribution --- Donut Chart
2.  Subscription Rate by Job --- Bar Chart
3.  Subscription Rate by Education --- Column Chart
4.  Housing Loan vs Subscription --- 100% Stacked Column
5.  Personal Loan vs Subscription --- 100% Stacked Column
6.  Subscription Rate by Contact Month --- Line Chart
7.  Previous Campaign Outcome vs Subscription --- Bar Chart
8.  Customer Age Distribution
9.  Account Balance Distribution
10. Campaign Contacts
11. Age vs Account Balance --- Scatter Chart
12. Multivariate Analysis visuals

## Slicers

-   Job
-   Education
-   Marital Status
-   Housing Loan
-   Personal Loan
-   Month
-   Previous Campaign Outcome

# DAX Measures

``` dax
Total Customers =
COUNTROWS(bank)
```

``` dax
Subscribed Customers =
CALCULATE(
    COUNTROWS(bank),
    bank[y] = "yes"
)
```

``` dax
Not Subscribed =
CALCULATE(
    COUNTROWS(bank),
    bank[y] = "no"
)
```

``` dax
Subscription Rate =
DIVIDE(
    [Subscribed Customers],
    [Total Customers],
    0
)
```

``` dax
Average Balance =
AVERAGE(bank[balance])
```

``` dax
Average Call Duration =
AVERAGE(bank[duration])
```

# Key Insights

1.  The dataset contains **45,211 customers**, with **5,289
    subscriptions**.
2.  The overall subscription rate is approximately **11.7%**.
3.  The target variable is highly imbalanced, with substantially more
    non-subscribers.
4.  Subscription rates vary across occupation and education groups.
5.  Previous campaign outcomes provide useful information about current
    customer response.
6.  Subscription rates vary across contact months.
7.  Campaign variables such as call duration and number of contacts
    provide useful campaign-performance information.

# Business Recommendations

-   Prioritize customer segments with higher historical subscription
    rates.
-   Use previous campaign outcomes for follow-up campaign targeting.
-   Study successful customer conversations to improve engagement.
-   Review campaign timing and focus resources on stronger-performing
    periods.
-   Monitor repeated contacts and avoid inefficient contact strategies.
-   Use the Power BI dashboard to compare customer segments
    interactively.

# Project Outcome

This project demonstrates the complete data analytics workflow:

**Problem Definition → Dataset Selection → Data Cleaning → Feature
Engineering → EDA → Multivariate Analysis → Visualization → Power BI
Dashboard → Business Insights → Recommendations**

The project demonstrates how raw banking campaign data can be
transformed into meaningful insights for data-driven marketing
decisions.

# Future Scope

-   Build a machine-learning model to predict subscription probability.
-   Apply categorical encoding and additional preprocessing for ML.
-   Handle target-class imbalance.
-   Compare classification algorithms.
-   Create a customer propensity score.
-   Connect Power BI to a regularly updated data source.


# Author

**Keerthana Ram**

**Project:** Bank Marketing Campaign Analysis\
**Domain:** Data Analytics\
**Tools:** Python, Power BI, Pandas, NumPy, Matplotlib, Seaborn

# Conclusion

The Bank Marketing Campaign Analysis combines Python and Power BI to
transform customer and campaign data into actionable business insights.
The project covers data preparation, univariate, bivariate and
multivariate analysis, visualization, dashboard development, and
business recommendations. The final dashboard provides an interactive
way to explore customer segments and campaign performance and supports
more informed marketing decisions.

