# MSCS_634_ProjectDeliverable_1
# Telco Customer Churn

MSCS-634-M50 Advanced Big Data and Data Mining · Project Deliverable 1: Data Collection, Cleaning, and Exploration

## Dataset Summary

This project uses IBM's Telco Customer Churn sample dataset ([Kaggle dataset](https://www.kaggle.com/datasets/blastchar/telco-customer-churn) · [IBM source CSV](https://raw.githubusercontent.com/IBM/telco-customer-churn-on-icp4d/master/data/Telco-Customer-Churn.csv)).

- **Size:** 7,043 customers (one row per customer) and 21 columns.
- **What it contains:** demographics, phone and internet services, contract and payment details, tenure, monthly charges, cumulative (total) charges, and the target column `Churn`.
- **Target:** `Churn = Yes` means the customer left within the last month. About 26.5% of customers churned.

### Why this dataset is appropriate

- It is far larger than the 500-record minimum and has well over 8-10 attributes.
- It mixes numeric columns (tenure, monthly charges, total charges) with many categorical columns (contract, services, payment method).
- It supports every later phase of the project from the same customer records: regression on `MonthlyCharges`, classification of `Churn`, clustering of similar customers, and association rule mining on services and account attributes.
- The data is a snapshot, so findings are interpreted as relationships within the sample, not as proof of cause.

## Repository Contents

| File | Description |
|---|---|
| `MSCS_634_ProjectDeliverable_1.ipynb` | Notebook with all code, comments, visualizations and written explanations |
| `README.md` | This summary |

## Data Cleaning Steps

| Step | What we found | What we did |
|---|---|---|
| **Load and inspect** | 7,043 rows, 21 columns. The first null check reported no missing values. | Checked column types, sample rows and churn counts before changing anything. |
| **Missing values** | 11 `TotalCharges` entries contained only spaces, so they were read as text and slipped past the null check. All 11 customers have 0 months of tenure. | Trimmed the text and converted the column to numbers, which exposed the blanks as `NaN`. Confirmed nothing else failed to convert. Kept the 11 customers in the main dataset and used the 7,032 recorded totals only for analyses that need `TotalCharges`. |
| **Duplicates** | 0 exact duplicate rows and 0 repeated customer IDs. | No records removed. |
| **Inconsistent formatting** | No spelling or case problems after trimming whitespace. Labels such as `No internet service` describe a real service status. | Kept those labels as recorded. |
| **Noisy values and outliers** | No negative values in tenure or billing. The 1.5 x IQR rule flagged 0 monthly charges overall, but 342 within the no-internet group. | Kept all records. The 342 flagged customers all have phone service with multiple lines, which explains their higher bills. |

All 7,043 customer records were retained.

## Exploratory Data Analysis and Key Insights

- **Class balance.** About 26.5% of customers churned, so the classes are imbalanced. A model that always predicted "stayed" would look accurate without learning anything.
- **Tenure and churn.** Customers who left are concentrated at short tenures: about 27% of churned customers fall in the shortest tenure bin, compared with about 7% of customers who stayed.
- **Churn by contract.** Churn is 42.7% for month-to-month contracts, 11.3% for one-year contracts and 2.8% for two-year contracts. Contract type is closely tied to churn.
- **Monthly charges and services.** Monthly charges peak near $20 and again between $65 and $105. The median bill is about $92 for fiber optic, $56 for DSL and $20 for customers with no internet. The low peak is mostly customers without internet.
- **Unusual bills within a service group.** Within the no-internet group, 342 bills ($22.95 to $26.90) sit above the group's normal range. All belong to customers with phone service and multiple lines.
- **Total charges and tenure.** Cumulative charges generally rise with tenure and spread out more at longer tenures. Because total charges are built from past monthly bills, they overlap heavily with tenure.

## How These Insights Guide Modeling

- **Regression (`MonthlyCharges`).** Use service features such as `InternetService` and `MultipleLines`. Exclude `TotalCharges` and fields calculated from it, because cumulative charges can indirectly reveal the monthly amount.
- **Classification (`Churn`).** Start with tenure, contract type and services. Use stratified train/test splits to preserve the 26.5% churn rate, and report F1 score and ROC-AUC alongside accuracy. Compare models with and without `TotalCharges`, and handle its missing values inside the training pipeline.
- **Clustering and association rules.** Leave out `customerID`. Scale numeric features for distance-based clustering, and use service and account categories to build customer itemsets for rule mining.

## Challenges and How We Addressed Them

- **Hidden missing values.** The first null check showed none, but 11 `TotalCharges` entries held only spaces and were read as text. We trimmed the text, converted the column to numbers, and verified that nothing else failed to convert.
- **Deciding what to do with those 11 customers.** All had zero months of tenure, so no total had been recorded. We kept them in the dataset and used the 7,032 recorded totals only where `TotalCharges` was needed, instead of deleting valid customers or inventing a value.
- **Outliers that depend on the group.** The overall IQR check flagged nothing, but within the no-internet group it flagged 342 bills. They shared the same service setup, so we kept them instead of treating them as errors.
- **Making the notebook runnable anywhere.** The notebook loads a local CSV if one is present and otherwise downloads the IBM file, so it runs in Colab or offline.

## How to Run the Notebook

1. Open `MSCS_634_ProjectDeliverable_1(2).ipynb` in Jupyter or Google Colab.
2. Run the cells from top to bottom. The first code cell loads the public IBM CSV automatically.
3. For an offline run, place the CSV beside the notebook as `Telco-Customer-Churn.csv`.

**Requirements:** Python 3 with `pandas`, `seaborn` and `matplotlib`.
